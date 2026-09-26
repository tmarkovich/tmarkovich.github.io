# thomasmarkovich.com

Source for [thomasmarkovich.com](https://thomasmarkovich.com), a Jekyll site
hosted on GitHub Pages.

The theme is adapted from [jekyllBear](https://github.com/knhash/jekyllBear) by
knhash, itself a Jekyll port of the [Bear Blog](https://bearblog.dev) theme.

## Writing a post

Add a file to `_posts/` named `YYYY-MM-DD-slug.md`:

```yaml
---
layout: post
title: "Post Title"
subtitle: "Optional deck"
category: essays    # essays | engineering | notes
comments: true      # set false to hide the comment thread
math: false         # set true only to opt out; MathJax loads by default
---
```

Put `<!--more-->` after the opening paragraphs — everything above it becomes the
excerpt used for meta descriptions.

Permalinks are `/articles/:year-:month/:title` and must not change; existing
posts are linked from elsewhere.

## Comments

Comments are [giscus](https://giscus.app), backed by GitHub Discussions on this
repo. Three one-time steps, in order:

1. Settings -> General -> Features -> tick **Discussions**.
2. Install the [giscus app](https://github.com/apps/giscus) on this repository.
3. Fill `giscus.category_id` in `_config.yml` with the value giscus.app shows
   for the Announcements category.

Nothing renders until `category_id` is set, so the site is safe to deploy
before then. Per-post, `comments: false` hides the thread.

## Local preview

```
bundle exec jekyll serve
```

## Layout

- `_layouts/` — `default`, `page`, `post`, `resume`, plus jekyllBear's `compress`
- `_includes/` — `head`, `header`, `footer`, `mathjax`, `giscus`, `custom-head`
- `assets/style.css` — the whole theme; `--font-main` and `--font-scale` at the
  top are the knobs worth touching
- `_data/index/` — resume content
- `CNAME` — custom domain, load-bearing; deleting it breaks the domain
