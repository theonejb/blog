---
layout: post
title: Bootstrap with Ruby on Rails 7
date: 2022-08-07
redirect_from: /bootstrap-with-ruby-on-rails-7
---
If you have a brand new RoR 7 project that you created with the defaults by running `rails new <PROJECT>` then you can safely follow the following steps to get Bootstrap 5 installed in your project.

1. **Install gems**
Add the following to your `Gemfile` and run `bundle install`.
```ruby
gem 'bootstrap', '~> 5.2.0'
gem 'jquery-rails'
gem 'sass-rails' # This may already be present in the file in a commented line, in which case you should uncomment it.
```

2. **Setup Javascript**
In your `app/javascript/application.js`, add the following at the top.
```javascript
//= require jquery3
//= require popper
//= require bootstrap
```

3. **Load Javascript in your views**
In the `<head>` section of your `app/views/layouts/application.html.erb`, add this:
```erb
<%= javascript_importmap_tags %>
```

4. **Import Bootstrap CSS**
Rename the existing `app/assets/stylesheets/application.css` to `app/assets/stylesheets/application.scss` and add a line with `@import "bootstrap"` near the top.

The `sass-rails` Gem allows processing SCSS files to CSS on the fly. RoR 7 is already setup to make use of it without any additional configuration beyond installing the Gem.

In your HTML the CSS is loaded by the tag `<%= stylesheet_link_tag "application", "data-turbo-track": "reload" %>` which should already be present in your `app/views/layouts/application.html.erb`.

## Why did I write this?

I’ve been helping a non-tech fried learn programming for the past few months. He’s working through a Ruby on Rails course, and he’s now at the point where the course walks him through building mini apps; simple web socket based chat, stock trackers, etc…

Unfortunately the course uses Rail 6, and Rails 7 introduced a couple of new things that changed how JS and CSS files were processed and added. My friend has had constant problems getting Bootstrap to work nicely inside the apps he creates.

I’ve tried helping him by hacking away over Zoom, following instructions from a bunch of different sources. It worked sometimes, but the last few times he’s asked me to help, I couldn’t not get Bootstrap working, and I had to ask him to move to the next lesson without Bootstrap. It wasn’t a blocker, but it wasn’t a great experience either.

So today, I spent a few hours pouring over the documentation. What always confused me before was the 2 different ways of processing Javascript that RoR 7 has:

1. Import maps: [Working with Javascript in Rails](https://guides.rubyonrails.org/working_with_javascript_in_rails.html)
2. [The asset pipeline](https://guides.rubyonrails.org/asset_pipeline.html)

I thought these were 2 different systems and you had to choose one over the other. Unfortunately the official Rails Guides (linked above) don’t clarify this in the guides for both of these systems.

After reading the documentation and experimenting with a local Rails app, I was able to finally understand the basics of these two systems, and how they work together. I’ll describe it next for the next person who faces this confusion.

## How import maps and the asset pipeline fit together

Import maps are a way to import Javascript modules directly from the browser. Here’s a nice official (I think) resource about it: https://github.com/WICG/import-maps

Import maps in Rails 7 let you define mappings between the "bare" name you want to use in `import React from "react"` and the ESM compatible specifier that must be one of; absolute path, relative path, or a URI.

That’s it. Import maps have no business in how the files are pre-processed on loaded. If you use the import map tag in your HTML file, it will spit out the following code in the HTML:

```html
<script type="importmap" data-turbo-track="reload">
    {
      "imports": {
        "application": "/assets/application-45b83ea01a8c68b3493391ceecb79f31baf4159ca091fee6fd122bf413d79500.js",
        "@hotwired/turbo-rails": "/assets/turbo.min-e5023178542f05fc063cd1dc5865457259cc01f3fba76a28454060d33de6f429.js",
        "@hotwired/stimulus": "/assets/stimulus.min-b8a9738499c7a8362910cd545375417370d72a9776fb4e766df7671484e2beb7.js",
        "@hotwired/stimulus-loading": "/assets/stimulus-loading-1fc59770fb1654500044afd3f5f6d7d00800e5be36746d55b94a2963a7a228aa.js",
        "controllers/application": "/assets/controllers/application-368d98631bccbf2349e0d4f8269afb3fe9625118341966de054759d96ea86c7e.js",
        "controllers/hello_controller": "/assets/controllers/hello_controller-549135e8e7c683a538c3d6d517339ba470fcfb79d62f738a0a089ba41851a554.js",
        "controllers": "/assets/controllers/index-2db729dddcc5b979110e98de4b6720f83f91a123172e87281d5a58410fc43806.js"
      }
    }
</script>
<link rel="modulepreload" href="/assets/application-45b83ea01a8c68b3493391ceecb79f31baf4159ca091fee6fd122bf413d79500.js">
<link rel="modulepreload" href="/assets/turbo.min-e5023178542f05fc063cd1dc5865457259cc01f3fba76a28454060d33de6f429.js">
<link rel="modulepreload" href="/assets/stimulus.min-b8a9738499c7a8362910cd545375417370d72a9776fb4e766df7671484e2beb7.js">
<link rel="modulepreload" href="/assets/stimulus-loading-1fc59770fb1654500044afd3f5f6d7d00800e5be36746d55b94a2963a7a228aa.js">
<script src="/assets/es-module-shims.min-d89e73202ec09dede55fb74115af9c5f9f2bb965433de1c2446e1faa6dac2470.js" async="async" data-turbo-track="reload"></script>
<script type="module">
  import "application"
</script>
```

The actual loading of the files is left to the Asset Pipeline. Which is why you can use the import map tag in your HTML file, while still using the //= require jquery directives in your JS files. The Asset Pipeline also provides the fingerprinting that you see in the filenames above.