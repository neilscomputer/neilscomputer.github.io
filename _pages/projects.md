---
layout: page
title: projects
permalink: /projects/
description: Projects I've been working on.
nav: true
nav_order: 2
horizontal: false
---

<!-- pages/projects.md -->
<div class="projects">
{% assign sorted_projects = site.projects | sort: "importance" %}
{% if sorted_projects.size == 0 %}
  <p>No projects yet — check back soon.</p>
{% else %}
  <div class="row row-cols-1 row-cols-md-3">
    {% for project in sorted_projects %}
      {% include projects.liquid %}
    {% endfor %}
  </div>
{% endif %}
</div>
