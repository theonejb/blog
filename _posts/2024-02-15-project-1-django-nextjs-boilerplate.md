---
layout: post
title: "Project 1: Django + NextJS Boilerplate"
date: 2024-02-15
build_log: "/build-logs/project-1"
redirect_from: /project-1-django-nextjs-boilerplate
---

Links:

- [Gumroad page](https://asadjb.gumroad.com/l/nextjs-django-template)
- [Build Log]({{ page.build_log }})

---

My _accidental_ [new years resolution]({% post_url 2023-12-31-i-have-not-failed-enough %}) was to work on the 1 problem that has plagued me for my entire adult life; failure to commit and focus. I decided to work in 6 week "sprints" (inspired by [Shape Up](https://basecamp.com/shapeup)) and complete the projects I start - for some **known** definition of complete.

This is the 1st project I have decided to work on. I'll work on this from today (15th Feb 2024) to (28th Mar 2024). I'll follow-up then with another post talking about how it went.

## The project

The goal is to make & sell a Django + NextJS boilerplate template. What's a boilerplate template?

It's the source code for a project that's already setup with many things that are needed in a new project; for example:

- Stripe subscriptions functionality
- Background jobs
- CSS framework
- User/team management

A great example is [Saas Pegasus](https://www.saaspegasus.com/), which seems like an amazing boilerplate loved by many people.

My boilerplate is going to be _much_ simpler - and also much cheaper. SaaS Pegasus comes with so many features that it's worth the $249 starting price. I'm aiming for $5-$10.

## Goals

My goal is to sell this boilerplate to at least 10 people - and have them be happy using it. This means:

- talking to prospective customers and seeing if this can be useful to them. People will have the option of scheduling a 15 minute pre-purchase call with me for $5 to see if this would be useful to them. The payment is purely to make sure that I only spend time talking to people who are somewhat serious about purchasing.
- providing excellent after sales support. I'll include a 60 minute setup call with me for any purchase. While a 60 minute call for a $10 sale isn't scalable, it's a great way for me to talk to customers at the start.
- having a no questions asked refund policy. My experiences with running an [e-commerce store](https://khalil-ahmed.com/) in the past tell me this is an amazing way to build trust.
- provide on-going support, updates, and fixes over email.
- build a mailing list of people interested in my work who I can email when I launch my future projects.

## The deliverable

The boilerplate will allow developers to quickly start a project that uses Django for the backend and NextJS for the frontend. My recent experiences with another project in this tech stack required me to spend significant time on:

- figuring out how to setup authentication b/w Django & NextJS (this took the most time & effort)
- setting up Django Rest Framework so I could write APIs that would be used by the frontend
- writing Docker files that would build 2 containers - backend & frontend
- writing Terraform scripts to deploy those containers to AWS ECS
- writing config & scripts to run the project on Gitpod so it could be easily worked on by my team members

My plan is to build a boilerplate that already has most those features built in, plus a few extras:

- Celery with Redis for background task processing
- Tailwind CSS for the frontend (in my project I used ChakraUI but Tailwind would be a better option for a boilerplate)
- If there's demand for it, a stretch goal is to include social auth (sign-in with Google/Apple/etc)

Once complete, I'll put this on Gumroad and create a landing page there. From then on, it's all about marketing it; that's the part which I have no experience with and hope to learn the most from.

## The marketing plan

This is the area where I lack _any_ experience; so I'm not sure how I'm going to market this. Some ideas I have:

- build it in public on Twitter. I have a tiny Twitter following (312 followers) so not sure how useful this could be. But I have to try something.
- share it with people asking how to setup Django & NextJS on forums like Reddit, Stackoverflow, and others.
- _maybe_ write a blog post on how to setup Django & NextJS and then link to the boilerplate from there. The blog post would provider all the steps necessary for the basic setup and the boilerplate would go beyond that with something that's ready to use.

## The build log

I'd also like to create a build log with this project. This will be a daily note of what I did for this project. I'll keep it in my notes app [Reflect](https://reflect.app/) and periodically put it here in this blog post. These daily notes might also serve as content for my build-in-public marketing strategy.
