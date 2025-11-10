# Brandon's Technical Blog

A technical blog showcasing deep dives into performance engineering, systems debugging, and AI infrastructure.

## Live Site

**Blog**: https://brandongeraci.com/

## About

This blog is a collection of technical investigation series where I explore complex performance issues, debug mysterious system behaviors, and share detailed findings.

### Current Series

1. **[DGX Spark Deep Dive](https://brandongeraci.com/projects/dgx-spark/)** - A 6-part investigation into Docker container memory overhead on NVIDIA DGX Spark with Grace Hopper's unified memory architecture

## Tech Stack

- **Static Site Generator**: Hugo
- **Theme**: PaperMod
- **Deployment**: GitHub Pages (docs/ folder)

## Building Locally

```bash
# Clone with submodules
git clone --recurse-submodules git@github.com:brandonrc/blog.git
cd blog

# Build
hugo server

# Build for production
hugo --minify
```

Visit http://localhost:1313/blog/

## Adding New Content

### New Project Series

```bash
# Create project directory
mkdir -p content/projects/new-series-name

# Create project index
cat > content/projects/new-series-name/_index.md << 'EOF'
---
title: "Series Title"
date: 2025-XX-XX
series: ["new-series-name"]
---

Series description.
EOF

# Add posts
hugo new content/projects/new-series-name/01-first-post.md
```

### Build and Deploy

```bash
hugo --minify
git add .
git commit -m "Add new content"
git push
```

## License

Content: CC BY 4.0
Code: MIT

## Author

Brandon Geraci
GitHub: [@brandonrc](https://github.com/brandonrc)
