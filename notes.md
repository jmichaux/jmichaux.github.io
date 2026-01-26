---
layout: page
title: Notes
permalink: /notes/
---

These are informal, evolving technical notes. They are written while learning,
revisiting, or stress-testing ideas. Clarity is sometimes sacrificed for rigor.
Errors and gaps are part of the process.

## Ongoing research

<ul>
  {% assign ongoing = site.notes | where_exp: "n", "n.path contains 'ongoing_research/'" | sort: "title" %}
  {% for n in ongoing %}
    <li><a href="{{ n.url }}">{{ n.title }}</a></li>
  {% endfor %}
</ul>

## Working notes

<ul>
  {% assign working = site.notes | where_exp: "n", "n.path contains 'working_notes/'" | sort: "title" %}
  {% for n in working %}
    <li><a href="{{ n.url }}">{{ n.title }}</a></li>
  {% endfor %}
</ul>