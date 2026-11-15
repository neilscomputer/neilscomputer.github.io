# Site Cleanup Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Strip all Einstein/template example content and re-wire the al-folio site so the blog is the front page, projects is second in the nav, and the about page lives at /about off-nav.

**Architecture:** Pure content + config edits — no layouts or gems to touch (those live in plugin gems). Every change is in `_config.yml`, `_data/`, `_pages/`, `_posts/`, `_projects/`, `_news/`, `_books/`, or `_bibliography/`.

**Tech Stack:** Jekyll, al-folio v1 starter (gem-based), jekyll-paginate-v2, jekyll/scholar.

---

### Task 1: Create the cleanup branch

**Files:**
- (git only)

- [ ] **Step 1: Create and check out branch**

```bash
git checkout -b cleanup
```
Expected: `Switched to a new branch 'cleanup'`

- [ ] **Step 2: Verify**

```bash
git branch --show-current
```
Expected: `cleanup`

---

### Task 2: Update `_config.yml` — search, scholar identity, blog name, external sources

**Files:**
- Modify: `_config.yml`

- [ ] **Step 1: Disable search (3 flags)**

Change these three lines near the top of `_config.yml`:
```yaml
search_enabled: false
socials_in_search: false
posts_in_search: false
```

- [ ] **Step 2: Update blog name and description**

```yaml
blog_name: Neil's Blog
blog_description: Projects, notes, and explorations.
```

- [ ] **Step 3: Update scholar author identity**

```yaml
scholar:
  last_name: [Jones]
  first_name: [Neil, N.]
```

- [ ] **Step 4: Remove external_sources entries**

Remove the entire `external_sources:` block (the medium.com and Google Blog entries) so it becomes:
```yaml
external_sources:
```
(empty, or delete the block entirely)

- [ ] **Step 5: Commit**

```bash
git add _config.yml
git commit -m "config: disable search, update scholar identity, blog name, remove external sources"
```

---

### Task 3: Update `_data/socials.yml` — Neil Jones identity

**Files:**
- Modify: `_data/socials.yml`

- [ ] **Step 1: Replace the entire file content**

```yaml
cv_pdf:
email: ncjonez@gmail.com
rss_icon: true
scholar_userid: FQO07SYAAAAJ
```

(Remove `inspirehep_id`, the placeholder `cv_pdf`, and the `custom_social` Albert Einstein block.)

- [ ] **Step 2: Commit**

```bash
git add _data/socials.yml
git commit -m "data: update socials to Neil Jones (scholar ID, email, remove Einstein)"
```

---

### Task 4: Move the about page to `/about`, remove from nav

**Files:**
- Modify: `_pages/about.md`

- [ ] **Step 1: Rewrite the frontmatter and body**

Replace the full content of `_pages/about.md` with:

```markdown
---
layout: about
title: about
permalink: /about
subtitle: Software engineer. Curious builder.

profile:
  align: right
  image: prof_pic.jpg
  image_circular: false
  more_info: >
    <p>ncjonez@gmail.com</p>

selected_papers: false
social: true

announcements:
  enabled: false

latest_posts:
  enabled: false
---

Hi, I'm Neil Jones. I build things and write about them here.
```

- [ ] **Step 2: Commit**

```bash
git add _pages/about.md
git commit -m "pages: move about to /about, disable from nav, Neil Jones placeholder bio"
```

---

### Task 5: Make blog the front page (`/`)

**Files:**
- Modify: `_pages/blog.md`

- [ ] **Step 1: Change permalink and update blog nav_order**

Change only these two lines in `_pages/blog.md`'s frontmatter:
```yaml
permalink: /
nav_order: 1
```
Leave pagination config and all Liquid content unchanged.

- [ ] **Step 2: Commit**

```bash
git add _pages/blog.md
git commit -m "pages: make blog the front page at /"
```

---

### Task 6: Set projects as nav position 2, simplify content

**Files:**
- Modify: `_pages/projects.md`

- [ ] **Step 1: Update frontmatter**

Replace the frontmatter block:
```yaml
---
layout: page
title: projects
permalink: /projects/
description: Projects I've been working on.
nav: true
nav_order: 2
horizontal: false
---
```
(Remove `display_categories` — with no project files there are no categories to show.)

- [ ] **Step 2: Replace body with the category-free display only**

```markdown
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
```

- [ ] **Step 3: Commit**

```bash
git add _pages/projects.md
git commit -m "pages: projects at nav_order 2, empty state message, no categories"
```

---

### Task 7: Disable nav on repositories, teaching, people, submenus; fix remaining nav orders

**Files:**
- Modify: `_pages/repositories.md`
- Modify: `_pages/teaching.md`
- Modify: `_pages/profiles.md`
- Modify: `_pages/dropdown.md`
- Modify: `_pages/publications.md`
- Modify: `_pages/cv.md`

- [ ] **Step 1: Set `nav: false` on repositories**

In `_pages/repositories.md` change:
```yaml
nav: false
```

- [ ] **Step 2: Set `nav: false` on teaching**

In `_pages/teaching.md` change:
```yaml
nav: false
```

- [ ] **Step 3: Set `nav: false` on profiles (people)**

In `_pages/profiles.md` change:
```yaml
nav: false
```

- [ ] **Step 4: Set `nav: false` on dropdown (submenus)**

In `_pages/dropdown.md` change:
```yaml
nav: false
```

- [ ] **Step 5: Adjust publications to nav_order 3**

In `_pages/publications.md` change:
```yaml
nav_order: 3
```

- [ ] **Step 6: Adjust cv to nav_order 4**

In `_pages/cv.md` change:
```yaml
nav_order: 4
```

- [ ] **Step 7: Commit all**

```bash
git add _pages/repositories.md _pages/teaching.md _pages/profiles.md _pages/dropdown.md _pages/publications.md _pages/cv.md
git commit -m "pages: disable repositories/teaching/people/submenus from nav; renumber publications/cv"
```

---

### Task 8: Delete all example posts; add one test post

**Files:**
- Delete: all `_posts/*.md`
- Create: `_posts/2026-11-15-hello-world.md`

- [ ] **Step 1: Delete example posts**

```bash
rm _posts/*.md
```

- [ ] **Step 2: Create the test post**

Create `_posts/2026-11-15-hello-world.md`:
```markdown
---
layout: post
title: Hello, World
date: 2026-11-15 12:00:00
description: First post. More to come.
tags: []
categories: []
---

This is my first post. The site is under construction — real content coming soon.
```

- [ ] **Step 3: Commit**

```bash
git add _posts/
git commit -m "posts: replace all example posts with one hello-world test post"
```

---

### Task 9: Delete all example projects, news, and books

**Files:**
- Delete: all `_projects/*.md`
- Delete: all `_news/*.md`
- Delete: all `_books/*.md`

- [ ] **Step 1: Delete example content**

```bash
rm _projects/*.md
rm _news/*.md
rm _books/*.md
```

- [ ] **Step 2: Commit**

```bash
git add _projects/ _news/ _books/
git commit -m "content: remove all example projects, news items, and books"
```

---

### Task 10: Clear the example bibliography

**Files:**
- Modify: `_bibliography/papers.bib`

- [ ] **Step 1: Empty the file**

Replace the entire content of `_bibliography/papers.bib` with:
```
% Bibliography placeholder — add your own publications here.
```

- [ ] **Step 2: Commit**

```bash
git add _bibliography/papers.bib
git commit -m "bibliography: clear example Einstein papers"
```

---

### Task 11: Update `_data/cv.yml` with Neil Jones placeholder

**Files:**
- Modify: `_data/cv.yml`

- [ ] **Step 1: Replace file with Neil Jones skeleton**

```yaml
cv:
  name: Neil Jones
  label: Software Engineer
  email: ncjonez@gmail.com
  location: ""
  image: ""
  summary: Software engineer and builder. Interested in verifiable systems and interesting problems.

  social_networks:
    - network: GitHub
      username: neilscomputer

  sections:
    Education:
      - institution: Your University
        area: Your Field
        studyType: Your Degree
        start_date: 2000
        end_date: 2004

    Experience:
      - company: Your Company
        position: Your Role
        start_date: 2024
        end_date: present
        summary: ""
        highlights: []
```

- [ ] **Step 2: Commit**

```bash
git add _data/cv.yml
git commit -m "data: replace Einstein CV with Neil Jones placeholder"
```

---

### Task 12: Verify build

- [ ] **Step 1: Install dependencies**

```bash
bundle install
```

- [ ] **Step 2: Build the site**

```bash
bundle exec jekyll build --baseurl /al-folio
```
Expected: build completes with no errors (warnings about missing posts/projects are fine).

- [ ] **Step 3: Spot-check the output**

```bash
grep -r 'Einstein\|Albert\|al-folio blog' _site/index.html | head -5
```
Expected: no matches (or only footer attribution to al-folio framework).

- [ ] **Step 4: Final commit if any fixes needed**

```bash
git add -A
git commit -m "cleanup: fix any build issues"
```
