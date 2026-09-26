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
