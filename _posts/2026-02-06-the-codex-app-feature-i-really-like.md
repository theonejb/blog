---
layout: post
title: "The 1 feature I'm really liking in the OpenAI Codex App"
date: 2026-02-06
---

For the past few months I've been using AI coding agents heavily. So far I've used:
- AmpCode - I started out mostly with their VSCode extension & then moved exclusively to the CLI
- Claude Code - CLI
- Codex - CLI
- OpenCode - CLI
- Codex - App

I've been using the Codex app from OpenAI for a few days now — they made it available via the $20/m subscription for a trial period :)

While I've only been using Codex for a few days, I'm really enjoying using it. It's fast, an app instead of a CLI, allows organizing sessions by project, and shows a history of previous sessions that's easy to access.

That part about being an app instead of a CLI has some great benefits; the one I'm really liking is their Git diff viewer and commenting system.

In other agents if I have to ask for some changes, I have to refer to code by its file and maybe line number if I can find it, or try other ways like saying "change this variable inside this function in this file". With Codex, I can comment inline in the diff. This is really nice.

It's certainly doable in other agents, asking it to change specific lines of code it generated, but the GitHub PR-like commenting mechanism just makes it so easy. You go through your list of changes, comment on the things you need changed, and then submit all your comments to the agent.

I think this will be the future of AI coding agents - doing everything in a CLI is fine, but having a rich UI unlocks so much. I guess this is also why we're seeing more GUIs pop up around agents like Conductor or Emdash, which allow you to coordinate multiple agents in the same screen - replacing what people previously used terminal multiplexers like Tmux - or in my case, Zellij.

