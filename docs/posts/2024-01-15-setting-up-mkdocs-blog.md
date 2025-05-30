---
title: "How to Add Blog Posts - Quick Reference"
date: 2024-01-15
authors:
  - Gabryel Soh
description: "Quick reference guide for adding new blog posts to this MkDocs blog - for future me."
---

# How to Add Blog Posts - Quick Reference

This is a reference guide for future me on how to add new blog posts to this MkDocs blog.

## Creating a New Blog Post

### 1. File Naming Convention
```
docs/posts/YYYY-MM-DD-topic-name.md
```

**Examples:**
- `2024-01-16-spring-security-basics.md`
- `2024-01-17-postgresql-indexing-tips.md`
- `2024-01-18-debugging-microservices.md`

### 2. File Template
Every post needs this frontmatter at the top:

```yaml
---
title: "Your Post Title Here"
date: 2024-01-16
authors:
  - Gabryel Soh
description: "Brief description of the post content"
---

# Your Post Title Here

Your content goes here...
```

## Adding Posts to Navigation

After creating the post file, add it to `mkdocs.yml`:

```yaml
nav:
  - Home: index.md
  - Blog:
    - posts/index.md
    - "How to Add Posts": posts/2024-01-15-setting-up-mkdocs-blog.md
    - "Your New Post Title": posts/2024-01-16-your-new-post.md  # Add here
  - About: about.md
```

## Development Workflow

### Start Development Server
```bash
source .venv/bin/activate
mkdocs serve --dev-addr 127.0.0.1:8001
```

### Build Site
```bash
source .venv/bin/activate
mkdocs build
```

### Deploy (via GitHub Actions)
Just push to main branch - GitHub Actions runners and Github pages will update the site automatically.

## Quick Checklist

When adding a new post:

- [ ] Create file with correct naming: `YYYY-MM-DD-topic.md`
- [ ] Add proper frontmatter with title, date
- [ ] Write content with clear structure
- [ ] Add to navigation in `mkdocs.yml`
- [ ] Test locally with `mkdocs serve`
- [ ] Commit and push

## Future Improvements

### Tags Implementation
- **Add tags system**: Implement MkDocs Material tags plugin for automatic tag pages

### Automatic Blog Listing
- **Blog plugin**: Use `mkdocs-material[blog]` plugin for automatic post listing
- **Chronological sorting**: Posts automatically sorted by date
- **Pagination**: Handle large numbers of posts
- **RSS feed**: Automatic RSS generation for subscribers
- **Archive pages**: Monthly/yearly archives

### Enhanced Features
- **Search improvements**: Better search with tag and category filters
- **Reading time**: Automatic reading time estimation
- **Recent posts widget**: Show latest posts on homepage
- **Category pages**: Automatic category listing pages

### Categories
- Will add as I go along

---

*This reference guide will be updated as the blog workflow evolves.*