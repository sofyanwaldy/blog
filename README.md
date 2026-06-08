# 🌐 Sofyan Waldy's Blog & Portfolio

Welcome to the repository for my personal blog and portfolio website, accessible at [waldy.id](https://waldy.id/). This site is built using **Hugo** (a fast static site generator written in Go) and styled using the elegant, minimal **PaperMod** theme.

Here, I share my personal insights, ideas, thoughts on technology, book summaries, and stories.

---

## 🛠️ Tech Stack & Theme

- **SSG:** [Hugo](https://gohugo.io/) (Extended version recommended)
- **Theme:** [Hugo-PaperMod](https://github.com/adityatelange/hugo-PaperMod) (Integrated as a Git Submodule)
- **Deployment:** [Vercel](https://vercel.com/) / [Netlify](https://www.netlify.com/) / [GitHub Pages](https://pages.github.com/)
- **Domain:** [waldy.id](https://waldy.id/)

---

## 🚀 Getting Started

### 📋 Prerequisites

You need the **Hugo Extended** version to compile styles and run the server.

#### Installing Hugo Extended:
- **macOS** (via Homebrew):
  ```bash
  brew install hugo
  ```
- **Windows** (via Scoop or Chocolatey):
  ```bash
  scoop install hugo-extended
  # OR
  choco install hugo-extended
  ```
- **Linux** (Debian/Ubuntu):
  Download the `.deb` package for the Extended version from the [Hugo Releases](https://github.com/gohugoio/hugo/releases) page.

---

### 📥 Installation & Setup

1. **Clone the Repository:**
   Make sure you clone the repository along with its submodules (since the theme is a Git submodule):
   ```bash
   git clone https://github.com/sofyanwaldy/blog.git
   cd blog
   ```

2. **Initialize Submodules:**
   If you cloned the repo without submodules, initialize them manually:
   ```bash
   git submodule update --init --recursive
   ```

3. **Verify Installation:**
   Check your Hugo version to ensure it is installed correctly:
   ```bash
   hugo version
   ```

---

### 💻 Running Locally (Development)

To run the local development server, execute the following command in the root directory:

```bash
hugo server
```

- Access the local preview at: **`http://localhost:1313/`**
- The server supports **Live Reload**, so any changes you make to content or config files will be updated automatically in the browser.

#### Useful Development Commands:
| Command | Description |
| :--- | :--- |
| `hugo server -D` | Start server including draft posts (`draft = true`) |
| `hugo server -F` | Start server including future-dated posts |
| `hugo server --disableFastRender` | Start server and rebuild fully on change (useful for layout changes) |

---

## ✍️ Content Creation

The website contains different sections (posts, stories, books) configured in `hugo.yaml`. 

To create new content, use the `hugo new` command:

### 📝 Write a New Blog Post
```bash
hugo new content posts/my-new-post.md
```

### 📚 Write a New Book Summary
```bash
hugo new content books/my-new-book.md
```

### Front Matter Format
All markdown files start with a front matter section containing metadata. We use the TOML format (`+++` separators):

```toml
+++
title = 'My New Post Title'
date = 2026-06-08T11:00:00+07:00
draft = true
+++

Write your content here in Markdown...
```

To publish a post, change `draft = true` to `draft = false` (or omit the field).

---

## 🏗️ Production Build

To generate the static HTML files for production:

```bash
hugo --minify
```

This will create a `public/` directory containing all your static assets, minified for optimal performance.

---

## 🚢 Deployment

You can deploy this Hugo blog to multiple platforms:

### Option 1: Vercel (Recommended for simplicity)
1. Import your repository into Vercel.
2. Vercel automatically detects Hugo.
3. Configure the following build settings:
   - **Build Command:** `hugo --minify`
   - **Output Directory:** `public`
4. Set the environment variable `HUGO_VERSION` if needed (e.g., `0.148.2`).

### Option 2: Netlify
1. Create a `netlify.toml` file in the root directory:
   ```toml
   [build]
     publish = "public"
     command = "hugo --minify"

   [context.production.environment]
     HUGO_VERSION = "0.148.2"
     HUGO_ENV = "production"
   ```
2. Link your repository to Netlify and it will deploy automatically on push.

### Option 3: GitHub Pages (via GitHub Actions)
Create a workflow file `.github/workflows/hugo.yml`:
```yaml
name: Deploy Hugo site to Pages

on:
  push:
    branches:
      - main  # or master

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

defaults:
  run:
    shell: bash

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
          fetch-depth: 0

      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v4

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: '0.148.2'
          extended: true

      - name: Build with Hugo
        run: |
          hugo \
            --gc \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/"

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

---

## 📁 Project Directory Structure

```text
├── content/             # Markdown files containing your posts, books, etc.
│   ├── books/           # Book summaries / reviews
│   ├── posts/           # Blog posts
│   ├── About.md         # About page
│   └── stories.md       # Stories page
├── public/              # Automatically generated production site (static files)
├── static/              # Static files (images, favicons, robots.txt, etc.)
├── themes/
│   └── PaperMod/        # Git Submodule containing the Hugo PaperMod theme
├── hugo.yaml            # Main site configuration file
└── README.md            # You are here!
```

---

## 💬 Social & Links

- **Website:** [waldy.id](https://waldy.id/)
- **GitHub:** [@sofyanwaldy](https://github.com/sofyanwaldy)
- **LinkedIn:** [Sopyan Waldy](https://www.linkedin.com/in/sopyan-waldy/)
- **Mastodon:** [@swaldy@mastodon.social](https://mastodon.social/@swaldy)
- **Email:** sofyanwaldy@gmail.com
