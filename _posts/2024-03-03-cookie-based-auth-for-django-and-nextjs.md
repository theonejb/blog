---
layout: post
title: "Cookie Based Auth for Django and NextJS"
date: 2024-03-03
---

> If you're just looking for implementation instructions, skip my ramblings and go straight to the [code here](#implementation).

I'm currently working on my [first project]({% post_url 2024-02-15-project-1-django-nextjs-boilerplate %}) after deciding that I needed to [fail more]({% post_url 2023-12-31-i-have-not-failed-enough %}) and practice finishing projects instead of abandoning them midway once they got "boring".

Anyways... This one is till in it's interesting phase, so here's a blog post with some things I learned yesterday while working on it.

The project is a [boilerplate template](https://asadjb.gumroad.com/l/nextjs-django-template) that should make it easy for devs. to start a new project with a Django backend and a Next.js frontend, something I had to struggle with recently.

## The problem

The first thing I'm looking to solve is authentication. That was my biggest challenge when working on the contracting project that inspired this template.

While there are a number of good posts around how to setup authentication b/w Django & Next.js, nothing "definitive" came up and I had to cobble together a
weird mess of Django+DRF (Django Rest Framework) and Next.js+NextAuth, sharing
a token from Django that was masquarading as a JWT token for Next.js. It wasn't pretty and I knew I could do better.

## The options

I considered 2 options for authenticating the Next.js frontend with the Django backend:

1. Token based auth. On logging in, a user receives a token that is stored in local storage by the frontend and send with every request to the backend.
2. Session/Cookie based auth. This is how authentication works in Django by default and is very easy to get started with - it basically comes for free out of the box when you start a new Django project.

While token based auth. is what almost everyone suggests to use when using a Next.js frontend with any backend technology, I wanted to give session based auth. a try. I was curious what it would take to make it work - if it was even possible.

**tl;dr:** It was possible to use cookie/session auth. b/w Django & Next.js - though with a few constraints which make it less appealing than the token based solution

What follows are my notes on how to set it up, the problems I faced, and why for the template I'm going to go with token based auth. instead.

## Learning how CORS & Set-Cookie works

It took me a few hours to get my head around how cross-origin requests and cookies work together, but the actual implementation was surprisingly straight forward.

This "mini-quest" gave me a chance to learn a lot about how CORS and cookies work, and I'm happy with the time I spent on this. These are the resources which helped me the most (all are from MDN):

- [Cross-Origin Resource Sharing](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)
- [Same-origin policy](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy)
- [Using HTTP cookies](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies)
- [Set-Cookie](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie#samesitesamesite-value)

And finally, there was a surprise waiting for me! Browsers are almost universally making changes to restrict 3rd party or cross-domain cookies because of their privacy implications. Here's a nice article from MDN about it: [Saying goodbye to third-party cookies in 2024](https://developer.mozilla.org/en-US/blog/goodbye-third-party-cookies/).

This is the reason why; while this approach works, I won't be using it in the template. [More on that later](#why-not).

## Implementation {#implementation}

Implementing the session based auth. b/w Django & Next.js is pretty simple.

### Django configuration

1. Install the [`django-cors-headers`](https://github.com/adamchainz/django-cors-headers) Python package.
   1. Add `"corsheaders",` to your `INSTALLED_APPS`.
   1. Add the `"corsheaders.middleware.CorsMiddleware",` middleware, right above the existing `CommonMiddleware`.
   1. Set `CORS_ALLOWED_ORIGINS = ["http://localhost:3000"]`, replacing the URL with your frontend URL.
   1. Set `CORS_ALLOW_CREDENTIALS = True`
1. Configure `settings.py` to allow cross-domain access for the session cookie.
   1. Set `SESSION_COOKIE_SAMESITE = "None"`
   1. Set `SESSION_COOKIE_SECURE = True`

### Next.js configuration

No configuration is needed on the frontend. However, you do need to use the `credentials: "include",` option when using the `fetch()` API to access your backend.

Here's a minimal example.

```javascript
"use client";

import { BACKEND_URL } from "@/constants";

async function signIn() {
  const loginData = new FormData();
  loginData.append("username", "admin");
  loginData.append("password", "admin");

  return await fetch(`${BACKEND_URL}/accounts/login/`, {
    method: "POST",
    body: loginData,
    credentials: "include",
  });
}

async function whoAmI() {
  console.log(
    await fetch(`${BACKEND_URL}/accounts/me/`, {
      method: "GET",
      credentials: "include",
    }),
  );
}

export default function Home() {
  return (
    <main className="flex min-h-dvh w-full flex-col justify-around">
      <h1 className="text-center">Home</h1>
      <button className="" onClick={signIn}>
        Sign In
      </button>
      <button onClick={whoAmI}>Who Am I</button>
    </main>
  );
}
```

That's it. That simple piece of code & configuration took me hours to find. Hopefully you can use this example to skip all that time spent trying to figure things out.

_Side quest log_: Initially, I was not using the `credentials: "include"` option in the `signIn()` function above; thinking that I didn't need to send any cookies with the login call, only the second API call to the `/accounts/me` endpoint.

That mistake cost me about 2 hours of debugging time. If I had [RTFM](https://developer.mozilla.org/en-US/docs/Web/API/fetch#credentials) correctly the first time, I would have seen this:

> `include`: Tells browsers to include credentials in both same- and cross-origin requests, and always use any credentials sent back in responses.

The `credentials: "include"` not only controls if cookies are sent, but also if they are saved when returned by the server.

## Why I won't use this solution in the template {#why-not}

Browsers are phasing out 3rd party cookies ([Saying goodbye to third-party cookies in 2024](https://developer.mozilla.org/en-US/blog/goodbye-third-party-cookies/)) and adding features to work around that restriction where needed.

The simplest way that doesn't require much change is to use [Cookies Having Independent Partitioned State (CHIPS)](https://developer.mozilla.org/en-US/docs/Web/Privacy/Privacy_sandbox/Partitioned_cookies).

To enable CHIPS, you simply put a `Partitioned` flag on your `Set-Cookie` header, like so:

`Set-Cookie: session_id=1234; SameSite=None; Secure; Path=/; Partitioned;`

Unfortunately, there's no straight forward way to do this in Django for now. There's an open issue to resolve this, but looking at the comments, it won't likely be solved anytime soon.

Considering this, I opted to use the token based auth. method for my template. I'll write a blog on that once I get it working over the next few days.
