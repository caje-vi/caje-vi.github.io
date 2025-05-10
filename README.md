---
layout: home
title: Welcome
---

# Hey, I'm Jean Carlos

So I finally got around to making this blog. After three false starts and a lot of procrastination, here we are!

I'm a CS student trying to make sense of this crazy field. Between debugging assignments at 3 AM and trying to understand why my algorithms professor thinks sorting problems are "fun," I figured I should document this journey somewhere.

## What's this blog about?

Honestly? It's mostly for me. A place to dump my thoughts, notes, and occasional breakthroughs when I finally understand something that's been driving me nuts. But if my struggles and small victories help someone else, even better!

You'll probably find:
- Notes from classes that actually clicked for me
- Projects I'm working on (both the ones that work and the spectacular failures)
- Cool papers or concepts I'm trying to wrap my head around
- Random coding problems that kept me up at night

## Latest Posts

{% for post in site.posts limit:3 %}
<div class="post-preview">
  <h3><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h3>
  <p class="post-meta">{{ post.date | date: "%B %d, %Y" }}</p>
  <p>{{ post.excerpt | strip_html | truncatewords: 30 }}</p>
  <a href="{{ post.url | relative_url }}">Read more →</a>
</div>
<hr>
{% endfor %}

## Why "Future Proof"?

I picked this name because, well, nothing in CS ever seems to stay the same. By the time you master one language or framework, there's a new one everyone's talking about. So this is my attempt to learn the stuff that doesn't change - the fundamentals that hopefully keep me from becoming obsolete.

Is it working? Ask me in five years.

If you're on this weird journey too, maybe we can figure some things out together. I'll try to post at least a couple times a month, but no promises during finals week.

Oh, and there's an [RSS feed](/feed.xml) if you want to keep up with my occasional insights and frequent confusion.

Anyway, thanks for stopping by!

~ JC
