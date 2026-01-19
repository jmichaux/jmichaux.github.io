---
layout: page
title: About
permalink: /about/
---

<div class="about-container">

<div class="about-text">

I am a PhD graduate from the Robotics Department at the University of Michigan.
Previously, I earned a PhD in Cell and Molecular Biology from the University of Chicago
and a BA in Mathematics from Wayne State University.

I work on robotics, AI, and biology.

Email: **jmichaux15@gmail.com**

[Google Scholar](https://scholar.google.com/citations?user=17UDvBAAAAAJ&hl=en) ·
[LinkedIn](https://www.linkedin.com/in/jbmichaux) ·
[GitHub](https://github.com/jmichaux)

</div>

<div class="about-photo">
  <img src="/assets/images/headshot.jpg" alt="Jonathan Michaux">
</div>

</div>

## Research

{% for pub in site.data.publications %}
<div class="publication">
  <div class="publication-title">{{ pub.title }}</div>
  {{ pub.authors }}  
  <em>{{ pub.venue }}</em><br>
  <div class="publication-links">
    <a href="{{ pub.pdf }}">pdf</a>
    {% if pub.project %}<a href="{{ pub.project }}">project</a>{% endif %}
  </div>
</div>
{% endfor %}