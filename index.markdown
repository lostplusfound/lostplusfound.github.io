---
title: "lost+found"
layout: splash
permalink: /
date: 2025-05-26
header:
  overlay_color: "#000"
  overlay_filter: "0.4"
  overlay_image: /assets/images/landing-header.jpg
  cta_label: "Visit my GitHub"
  cta_url: "https://github.com/lostplusfound"
excerpt: "Hi, I'm Stephen. Here, I share what I’m building, learning, and exploring. Check out my latest blog posts below, or explore my full portfolio and learn more about me above."
---

{% for post in site.posts %}
  {% capture year %}{{ post.date | date: '%Y' }}{% endcapture %}
  {% if year != written_year %}
    {% capture written_year %}{{ year }}{% endcapture %}
  {% endif %}
  {% include archive-single.html %}
{% endfor %}
