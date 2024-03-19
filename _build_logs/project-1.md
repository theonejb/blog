---
layout: post
title: "[Build Log] Project 1: Django + NextJS Boilerplate"
date: 2024-02-20
---

Build log for [Project 1: Django + NextJS Boilerplate]({% post_url 2024-02-15-project-1-django-nextjs-boilerplate %}).

- 19/3/2024 :: Forgot to update the build log for a while. Here's what's happened since last time.
  - Finished token based auth. It uses a DRF API endpoint to sign in and stores the auth token in local storage.
  - The same token can be used in `fetch` calls to make authenticated requests to the Django backend.
  - Built a sign-in & sign-up form. Need to get the sign-up form hooked up with the backend, which is next.
- 6/3/2024
  - Create a proof-of-concept for token based auth. The backend & frontend are able to work together to sign in and use the token for authentication.
  - Made the [repo](https://github.com/Waraq-Labs/next.js-django-template/tree/Authentication) public for now. Will keep it public until finished. The hope is that this leads to conversations with people who're looking for a solution to the Django/Next.js integration and I can learn what they need.
- 3/2/2024
  - Finished adding helper scripts that
    - Allow renaming the backend project to something more appropriate for the developer
    - Setup PyRight LSP for Zed (and other editors that use it)
  - Worked on using 3rd party cookies to authenticate the API.
    - Some problems
      - Cross-domain cookies need HTTPS enabled. Will enable that with a simple self-signed cert and test it out.
        - **This isn't true. The problem was somewhere else.**
      - 3rd party/cross-domain cookies are being phased out. I'm only going through this to understand if/how I can make it work. This won't make it into the final template.
        - I was thinking of keeping this as the primary mechanism for authentication. Keeps things simple - and can be changed later if needed. However, further thoughts made me change my mind.
          - Doesn't require the use of a context to keep an authenticated token to send with all requests.
          - Conversely, the frontend has no way of knowing if it's authenticated or not without making an API call. With a token, once you have a valid token you're much more likely to be authenticated.
          - Django has no good way of setting the `Partitioned` flag on a cookie for now. There's an [issue open](https://code.djangoproject.com/ticket/34613), but no fix yet. Very soon ([Q1 2024 according to MDN](https://developer.mozilla.org/en-US/blog/goodbye-third-party-cookies/)) Chrome will roll out 3rd party cookie blocking and there won't be a simple fix.
    - Cross-domain cookies being phased out and replaced with other technologies to allow the same use case; in our case it's using cookies to authenticate to our own backend on a different domain.
      - Some further reading on this:
        - <https://developer.mozilla.org/en-US/blog/goodbye-third-party-cookies/>
        - <https://developers.google.com/privacy-sandbox/3pcd>
      - Another very viable alternative (though not as developer friendly for the local development environment) is having the backend and frontend be on the same base domain; api.site.com & www.site.com for example.
        - Will try to check this out as well.
    - **Summary**
      - I was able to get cross-domain cookies working - will write a blog post with my experiences & thoughts.
      - However, with the constraints I talked about above, that doesn't seem to be the best way forward.
      - Instead, I'll transition to using token based authentication.
- 23/2/2024
  - Finished setting up the initial backend & frontend app. Next step is to code the basic integration b/w the two.
- 22/2/2024
  - Completed initial Django project setup with Poetry
- 20/2/2024
  - Asked for feedback on Reddit & updated blog post with Gumroad link.
  - Added build logs to blog.
  - Asked for feedback on Reddit & updated blog post with Gumroad link.
  - Added build log pages to my blog. This is the first one!
- 18/2/2024
  - Setup Github repo & GitButler. This small low-stakes projects seems like a good place to learn how to use GitButler.
  - Wrote a list of tasks that I'll need to complete over the next few days. Breaking down large projects into very small manageable pieces helps me immensely. When I don't do this I keep "staring into the void" and don't make any meaningful progress.
- 17/2/2024
  - Finished the 1st draft of the [Gumroad page](https://asadjb.gumroad.com/l/nextjs-django-template) & setup Calendly for paid meeting.
- 16/2/2024
  - Wrote the [launch post]({% post_url 2024-02-15-project-1-django-nextjs-boilerplate %}) with some details and goals. Posted about it on Twitter.
