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

## Analytics

Cloudflare Web Analytics, via the beacon in `_includes/custom-head.html`. The
token is public by design -- it ships in the page source of every site using
Web Analytics -- so it lives in the repo rather than in a secret.

In the Cloudflare dashboard this site must be set up with **"Enable with JS
snippet installation"**, not automatic setup. Automatic injection is done by
Cloudflare's zone HTML rewriter, which only touches responses Cloudflare
proxies; GitHub Pages responses never pass through it, so the automatic
toggle silently reports zeros forever.

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
