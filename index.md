---
layout: home
title: Welcome
---

[comment](
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
)

## Why "Future Proof"?

I picked this name because, well, nothing in CS ever seems to stay the same. By the time you master one language or framework, there's a new one everyone's talking about. So this is my attempt to learn the stuff that doesn't change - the fundamentals that hopefully keep me from becoming obsolete.

Is it working? Ask me in five years.

If you're on this weird journey too, maybe we can figure some things out together. I'll try to post at least a couple times a month, but no promises during finals week.

Oh, and there's an [RSS feed](/feed.xml) if you want to keep up with my occasional insights and frequent confusion.

Anyway, thanks for stopping by!

~ JC
