---
layout: post
title: Deploying Ruby on Rails to AWS with Kamal
date: 2024-06-22
---
As part of a contracting project, I've been building an analytics dashboard for a feedback collection SaaS. The app is built in Ruby on Rails and given all the nice things I've heard about [Kamal](https://kamal-deploy.org/); I decided to use it for deploying the app.

The experience has been phenomenal; outside of some frustration with the initial deployment.

The app is deployed on a pretty standard AWS setup; a couple of EC2 servers hosting the web app running inside Docker containers, and a load balancer in front.

One of the problems I faced during the initial deployment was forwarding headers from the AWS application load balancer to the RoR server running in the Docker container.

The challenge with Kamal is that it relies heavily on [Traefik](https://traefik.io/traefik/), and while Traefik is a great tool, it takes some getting used to. It's configuration is not very intuitive, and there's no easy way to see how things are configured outside of looking at the text logs.

The Traefik document is pretty thorough, so a bit of searching led me to this CLI argument which needs to be passed to the Traefik container:

`entrypoints.http.forwardedheaders.insecure: true`

However, no matter what I tried, when I added this, the app container would stop responding to web requests. Without the config the container would work but throw an exception related to the `Origin` header not matching the configured hosts.

After a lot of experimentation, I stumbled upon the other config I needed to add by pure luck.

`entrypoints.http.address: ":80"`

As far as I can tell, when I added the `forwardedheaders` config, the entrypoint no longer got the correct `address` configuration. I'm not sure if this is related to Kamal or Traefik.

## Kamal `deploy.yml`
If you're looking to replicate a similar setup, here's the Kamal `deploy.yml` file that I am using with this project to deploy to AWS, with a load balancer terminating the SSL connection and forwarding traffic to web servers that are configured via Kamal. As a bonus, this config also deploys [Sidekiq](https://sidekiq.org/) for background tasks.

```yaml
service: <SERVICE NAME>

image: <IMAGE NAME>

ssh:
  user: ubuntu
  proxy: "ubuntu@A.B.C.D"

servers:
  web:
    hosts:
      - "A.B.C.D"
      - "A.B.C.D"
    labels:
      traefik.http.routers.<SERVICE NAME>-web.rule: Host(`<YOUR HOST NAME>`)
  sidekiq:
    hosts:
      - "A.B.C.D"
      - "A.B.C.D"
    traefik: false
    cmd: bundle exec sidekiq


registry:
  server: <AWS ACCOUNT ID>.dkr.ecr.<AWS REGION>.amazonaws.com
  username: AWS
  password: <%= %x(aws ecr get-login-password --region <AWS REGION>) %>

builder:
  local:
    arch: amd64 # Because I develop on a Apple Silicon machine, I need to use a build target

env:
  clear:
    - DATABASE_URL: <DATABASE URL>
  secret:
    - RAILS_MASTER_KEY
    - DB_PASSWORD

traefik:
 args:
   entrypoints.http.address: ":80"
   entrypoints.http.forwardedheaders.insecure: true
   log.level: DEBUG
   accesslog: true
   accesslog.format: json
```
