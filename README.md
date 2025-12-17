# Code & Context

A modern technical blog powered by Jekyll and the Chirpy theme. Hosted on GitHub Pages.

## About

This blog features technical deep-dives on:
- AI-assisted development workflows
- Systematic engineering practices
- Building production-ready systems
- Development process optimization

## Setup

This site uses Jekyll with the Chirpy theme for a modern, technical blog experience.

### Local Development

To run the site locally:

1. **Install Ruby dependencies** (requires Ruby 3.2+ and ruby-dev):
   ```bash
   sudo apt-get install ruby-dev  # Ubuntu/Debian
   bundle install
   ```

2. **Run the development server**:
   ```bash
   bundle exec jekyll serve
   ```

3. **View the site** at `http://localhost:4000/frankbria-blog/`

### GitHub Pages Deployment

The site automatically deploys to GitHub Pages when you push to the `main` branch.

**Setup Steps:**

1. **Enable GitHub Pages**:
   - Go to repository Settings → Pages
   - Source: GitHub Actions
   - The workflow will build and deploy automatically

2. **Add new blog posts** to the `_posts/` directory:
   - File naming: `YYYY-MM-DD-title.md`
   - Include front matter with title, date, categories, tags

3. **Customize**:
   - Edit `_config.yml` for site metadata
   - Update `_tabs/about.md` with your information
   - Add social links in `_config.yml`

## Blog Post Format

Create new posts in `_posts/` with this front matter:

```yaml
---
layout: post
title: "Your Post Title"
date: YYYY-MM-DD HH:MM:SS -0500
categories: [Category1, Category2]
tags: [tag1, tag2, tag3]
description: "Brief description for SEO"
toc: true
---

Your content here...
```

## Theme Features

The Chirpy theme includes:
- Responsive design
- Dark/light mode toggle
- Table of contents for posts
- Category and tag organization
- Archive pages
- Search functionality
- Code syntax highlighting
- SEO optimization

## Live Site

Visit the blog at: https://lab.frankbria.com/

## License

Content is © Frank Bria. Theme is [MIT licensed](https://github.com/cotes2020/jekyll-theme-chirpy/).

