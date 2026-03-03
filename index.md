---
layout: page
title: ""
---

## About {#about}

<div class="about-container">

<div class="about-text">

I have PhDs in Robotics (University of Michigan) and Cell & Molecular Biology (University of Chicago), and a BA in Mathematics from Wayne State University. During my PhDs, I led research on provably-safe robot motion planning and studied the biophysics of cellular force generation and RNA structural dynamics. I care about building AI and autonomous systems that are safe, controllable, and genuinely useful for people, with applications in science, health, education, and manufacturing. You can reach me by email: jmichaux15 at gmail dot com.





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