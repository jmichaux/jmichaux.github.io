---
layout: page
title: ""
---

## About {#about}

<div class="about-container">

<div class="about-text">

I am a PhD graduate from the Robotics Department at the University of Michigan.
Previously, I earned a PhD in Cell and Molecular Biology from the University of Chicago
and a BA in Mathematics from Wayne State University. Over the years, my research work has focused on robotics and cell biology, with an emphasis on safe motion planning, robust control, and quantitative analysis of biological systems. You can reach me by email: jmichaux15 at gmail dot com.

<div class="icon-row">
  <a href="https://scholar.google.com/citations?user=17UDvBAAAAAJ&hl=en" aria-label="Google Scholar">
    <i class="fa-solid fa-graduation-cap"></i>
  </a>
  <a href="https://www.linkedin.com/in/jbmichaux" aria-label="LinkedIn">
    <i class="fa-brands fa-linkedin"></i>
  </a>
  <a href="https://github.com/jmichaux" aria-label="GitHub">
    <i class="fa-brands fa-github"></i>
  </a>
</div>

</div>

<div class="about-photo">
  <img src="/assets/images/headshot.jpg" alt="Jonathan Michaux">
</div>

</div>

## Research

<div class="pub-list">
{% for pub in site.data.publications %}
  <div class="pub">
    {% if pub.image %}
    <div class="pub-fig">
      <img src="{{ pub.image }}" alt="{{ pub.title }}">
    </div>
    {% endif %}

    <div class="pub-body">
      <div class="pub-title">{{ pub.title }}</div>
      <div class="pub-meta">
        {{ pub.authors }}<br>
        <em>{{ pub.venue }}</em>
      </div>
      <div class="pub-links">
        {% if pub.pdf %}<a href="{{ pub.pdf }}">pdf</a>{% endif %}
        {% if pub.project %}<a href="{{ pub.project }}">project</a>{% endif %}
      </div>
    </div>
  </div>
{% endfor %}
</div>