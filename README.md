# 0xNegan — GitHub.io Deployment Guide

## Architecture

```
0xnegan.github.io/
├── _config.yml                    # Site config (branding, links, metadata)
├── _sass/addon/
│   └── variables.scss             # Color scheme + font overrides
├── assets/
│   ├── css/
│   │   └── style.scss             # Full custom CSS (crimson brand, monospace headings)
│   └── img/
│       ├── avatar.png             # YOUR profile photo (add this)
│       └── living-off-the-user/
│           ├── banner.png         # Blog post banner
│           └── banner.svg         # Vector source
├── _tabs/
│   └── about.md                   # Complete About page
├── _posts/
│   └── 2026-04-03-living-off-the-user-post-credential-phishing.md
├── Gemfile
└── README.md
```

## Deployment — Step by Step

### Step 1: Fork the Chirpy Starter

1. Open: **[github.com/cotes2020/chirpy-starter](https://github.com/cotes2020/chirpy-starter)**
2. Click **"Use this template"** → **"Create a new repository"**
3. Repository name: **`<your-github-username>.github.io`**
   - Example: `0xnegan.github.io`
4. Set to **Public**
5. Click **Create repository**

### Step 2: Clone Locally

```bash
git clone https://github.com/<username>/<username>.github.io.git
cd <username>.github.io
```

### Step 3: Drop In Custom Files

Copy every file from this package into your cloned repo, **replacing** existing files:

```bash
# From this package directory:
cp _config.yml <your-repo>/
cp Gemfile <your-repo>/
cp -r _sass/ <your-repo>/
cp -r assets/ <your-repo>/
cp -r _tabs/ <your-repo>/
cp -r _posts/ <your-repo>/
```

### Step 4: Add Your Avatar

Place your profile photo at:
```
assets/img/avatar.png
```
- Recommended: 512x512px minimum, square crop
- Format: PNG or JPG

### Step 5: Update Personal Links

In **`_config.yml`**:
- Replace `0xnegan` with your actual GitHub username in `url` and `github.username`
- Update email if desired

In **`_posts/2026-04-03-living-off-the-user-post-credential-phishing.md`**:
- Replace `[YOUR_LINKEDIN_PLACEHOLDER]` at the bottom with your LinkedIn URL

### Step 6: Push & Deploy

```bash
git add .
git commit -m "🚀 Initial deploy — 0xNegan's security research blog"
git push origin main
```

### Step 7: Enable GitHub Pages

1. Go to your repo → **Settings** → **Pages**
2. Under "Build and deployment":
   - Source: **GitHub Actions**
3. Wait 2-3 minutes for the build
4. Your site is live at: `https://<username>.github.io`

### Step 8: Verify

Check these pages work:
- `https://<username>.github.io` — Home (should show your blog post)
- `https://<username>.github.io/about/` — About page
- `https://<username>.github.io/tags/` — Tags cloud
- `https://<username>.github.io/categories/` — Categories
- `https://<username>.github.io/archives/` — Archives

---

## What Makes This Design Different

| Feature | Default Chirpy (DbgMan) | 0xNegan Custom |
|---|---|---|
| **Accent Color** | Blue/Purple | Crimson Red (#DC2626) |
| **Heading Font** | System sans-serif | JetBrains Mono (monospace) |
| **Body Font** | System default | Inter |
| **Sidebar** | Standard dark | Near-black with red accent border |
| **Avatar** | Standard ring | Red glow ring with hover effect |
| **Code blocks** | Default | JetBrains Mono with red syntax |
| **Tags** | Default pills | Monospace with red borders |
| **Tables** | Standard | Red header backgrounds |
| **Blockquotes** | Standard | Red left border + tinted bg |
| **Scrollbar** | Default | Custom red scrollbar |
| **Selection** | Default | Red highlight |
| **About page** | Basic text | Terminal-style headings, stat cards, cert badges |

---

## Adding Future Blog Posts

Create new files in `_posts/`:

```
_posts/YYYY-MM-DD-your-post-title.md
```

Front matter template:

```yaml
---
title: "Your Post Title"
description: "Brief description for SEO and social cards"
author: 0xnegan
date: YYYY-MM-DD HH:MM:SS +0200
categories: [Category1, Category2]
tags: [tag1, tag2, tag3]
pin: false
image:
  path: /assets/img/your-post/banner.png
  alt: "Banner alt text"
---

Your content here in Markdown...
```

---

## Custom Domain (Optional)

To use a custom domain like `0xnegan.com`:

1. Buy domain from Cloudflare/Namecheap
2. Add CNAME record: `www` → `<username>.github.io`
3. Add A records pointing to GitHub Pages IPs:
   ```
   185.199.108.153
   185.199.109.153
   185.199.110.153
   185.199.111.153
   ```
4. In repo: create file `CNAME` containing your domain
5. Settings → Pages → Custom domain → enter your domain → Enforce HTTPS

---

## Troubleshooting

**Build fails:** Check GitHub Actions tab for error logs. Most common issue: missing Gemfile or incorrect _config.yml syntax.

**Styles not applying:** Clear browser cache. The SCSS files need to compile during build — if `assets/css/style.scss` doesn't have the front matter dashes (`---`) at the top, it won't compile.

**Posts not showing:** Verify the filename format `YYYY-MM-DD-title.md` and that the date is not in the future.

---

© 2026 0xNegan. All rights reserved.
