---
layout: post
title: "Production deployments to exe.dev"
date: 2026-07-19
hn_link: https://news.ycombinator.com/item?id=48973087
---

Recently I've been doing most of my personal development work on VMs from [https://exe.dev](https://exe.dev){:target="_blank"}. More on why in a future post, but in summary; these [long lived VMs](https://exe.dev/docs/serverful){:target="_blank"} offer a safe way to run agentic coding tools like Claude Code in `--dangerously-skip-permissions` mode, which makes the models really shine.

For small side projects, these VMs can also be used for production deployments; even exe.dev [talks about this](https://exe.dev/docs/use-case-dev-prod-test){:target="_blank"}. It's not HA; no failover, and there's little backup, but it works. More importantly, you can have an agent running on the VM do the entire setup for you.

I've done just this for 2 of my own projects; [https://keepyourtribe.com](https://keepyourtribe.com){:target="_blank"} and [https://vishlist.my](https://vishlist.my/m/eougj4wc7jij){:target="_blank"}. Both have production deployments on single exe.dev VMs. I've setup a few things in both that help make it a safer production environment. I hope this helps others set up their own production apps on such VMs as well.

## The CD pipeline

I use regular Github actions to power my CI/CD pipeline. A Github workflow triggers on pushes/merges to main. Once the tests pass, they trigger a deployment. This is where having a production setup on exe.dev differs from Fly.io or ECS, my previous favourites.

I use [https://github.com/adnanh/webhook](https://github.com/adnanh/webhook){:target="_blank"}, which is a small Linux HTTP server which listens to webhooks. It's running as a systemd service and always starts with the VM. It listens on a specific port for a simple POST request on `/hooks/deploy`.

The Github CD action is just a simple curl to this path.

```yaml
- name: Trigger deploy on production VM
  run: |
    curl -fsS --max-time 600 -X POST \
      -H "X-Exedev-Authorization: Bearer ${{ secrets.EXE_DEV_DEPLOY_TOKEN }}" \
      https://VM_NAME.exe.xyz:PORT/hooks/deploy
```

The webhook server triggers a `bin/deploy` script when it receives this webhook. At this point your security alarms might be going off, but I feel confident in this setup for one reason, the webhook is secured by the [HTTPS proxy](https://exe.dev/docs/proxy){:target="_blank"} offered by exe.dev. The webhook endpoint can only be accessed after authenticating with the proxy; which in the case of the Github action is done via a [HTTPS Token](https://exe.dev/docs/https-tokens-for-vms){:target="_blank"}.

That script also accepts no input. It just runs a Rails deployment. Here's the code:

```bash
#!/usr/bin/env bash
set -euo pipefail
export RAILS_ENV=production

cd "$(dirname "$0")/.."

# Serialize deploys (belt-and-braces; the Actions concurrency group already
# serializes triggers)
exec 9>/tmp/kyt-deploy.lock
flock 9

git pull --ff-only origin main
bundle install
bin/rails db:prepare
bin/rails assets:precompile
sudo systemctl restart keep-your-tribe-jobs.service
sudo systemctl restart keep-your-tribe-production.service

# Wait for /up to come back before declaring success
for i in $(seq 1 30); do
  sleep 2
  if curl -fsS http://127.0.0.1:3000/up > /dev/null; then
    echo "Deploy OK: $(git rev-parse --short HEAD)"
    exit 0
  fi
done
echo "App did not become healthy after restart" >&2
exit 1
```

With this, I have a functioning CI/CD system that updates my server with every update to the `main` branch.

With continuous deployment handled, let's make sure we have a way to protect ourselves against data loss as well.

## Database setup & loss prevention

I run both my apps on a sqlite database. This can easily scale to a few thousand users; currently I have ZERO!

It's easy to setup - there's literally nothing I had to do. The only downside is having no backups.

There's where [litestream](https://litestream.io){:target="_blank"} comes in. Litestream is a sqlite replication service. With some acceptable replication delay (about a second I think), it replicates all changes to my application database to a Cloudflare R2 bucket - using the S3 compatible API Cloudflare offers.

That's it. Nothing fancy, but it works. An added benefit I figured out recently was that I could use litestream on a dev VM to make a quick clone of the DB from the same R2 bucket; this helps a ton in debugging app issues. I can clone the DB on the dev VM, and point an AI agent at it.

## Closing thoughts

I was sceptical when I first saw exe.dev [mention](https://exe.dev/docs/use-case-dev-prod-test){:target="_blank"} that these VMs could be used for production. Without the deployment webhook and the litestream replication, I wouldn't have been confident in deploying a production environment to exe.dev either.

With them however, I think this can work for quite a while; or even forever if my apps don't get any users. :) :(

Checkout [https://keepyourtribe.com](https://keepyourtribe.com){:target="_blank"} - I made it as a way to remind myself to keep in touch with the people I care about.
