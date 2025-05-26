---
layout: archive
title: "Portfolio"
permalink: /portfolio/
author_profile: true
---
{% for post in site.portfolio %}
  {% capture year %}{{ post.date | date: '%Y' }}{% endcapture %}
  {% if year != written_year %}
    {% capture written_year %}{{ year }}{% endcapture %}
  {% endif %}
  {% include archive-single.html %}
{% endfor %}
