# Príbehy a poviedky — Hugo Short Story Blog

A multilingual (Slovak/English) blog for publishing short stories, built with Hugo and the custom **Storyteller** theme. Deploys automatically to GitHub Pages. Includes a web-based CMS (Decap CMS) for writing and managing stories from the browser.

## Quick Start (Local Development)

```bash
# Install Hugo extended >= 0.128
# https://gohugo.io/installation/

hugo server -D
# Open http://localhost:1313
```

## Deployment to GitHub Pages

### 1. Create a GitHub Repository

```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin git@github.com:YOUR_USERNAME/YOUR_REPO.git
git push -u origin main
```

### 2. Enable GitHub Pages

1. Go to **Settings → Pages** in your repository
2. Under **Source**, select **GitHub Actions**
3. The included workflow (`.github/workflows/deploy.yml`) will build and deploy on every push to `main`

### 3. Custom Domain (Optional)

1. Add your domain in **Settings → Pages → Custom domain**
2. Create a `static/CNAME` file containing your domain:
   ```
   yourdomain.com
   ```
3. Configure DNS:
   - For apex domain: add `A` records pointing to GitHub Pages IPs
   - For subdomain: add a `CNAME` record pointing to `YOUR_USERNAME.github.io`
4. Check **Enforce HTTPS**

See [GitHub Pages custom domain docs](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site) for details.

## Decap CMS Setup (Web Editor)

The CMS lives at `/admin` on the deployed site. It requires a GitHub OAuth proxy for authentication.

### 1. Create a GitHub OAuth App

1. Go to **GitHub → Settings → Developer settings → OAuth Apps → New OAuth App**
2. Fill in:
   - **Application name**: `Your Blog CMS`
   - **Homepage URL**: `https://yourdomain.com`
   - **Authorization callback URL**: `https://YOUR_AUTH_WORKER.workers.dev/callback`
3. Note the **Client ID** and generate a **Client Secret**

### 2. Deploy the Cloudflare Worker (OAuth Proxy)

This uses the open-source [sveltia-cms-auth](https://github.com/sveltia/sveltia-cms-auth) project.

1. Sign up for a free [Cloudflare](https://dash.cloudflare.com) account
2. Install Wrangler CLI:
   ```bash
   npm install -g wrangler
   wrangler login
   ```
3. Clone and deploy the auth worker:
   ```bash
   git clone https://github.com/sveltia/sveltia-cms-auth.git
   cd sveltia-cms-auth
   wrangler deploy
   ```
4. Set secrets on the worker:
   ```bash
   wrangler secret put GITHUB_CLIENT_ID
   # Enter the Client ID from your OAuth App
   wrangler secret put GITHUB_CLIENT_SECRET
   # Enter the Client Secret from your OAuth App
   ```

### 3. Update CMS Config

Edit `static/admin/config.yml`:

```yaml
backend:
  name: github
  repo: YOUR_USERNAME/YOUR_REPO
  branch: main
  base_url: https://YOUR_AUTH_WORKER.workers.dev
```

Replace the placeholders with your actual values.

### 4. Use the CMS

1. Navigate to `https://yourdomain.com/admin`
2. Click **Login with GitHub**
3. Authorize the OAuth app
4. Write and publish stories directly from the browser

## Cusdis Comments

[Cusdis](https://cusdis.com) provides lightweight, privacy-friendly comments.

### Setup

1. Create a free account at [cusdis.com](https://cusdis.com)
2. Create a new site in the Cusdis dashboard
3. Copy the **App ID** from the embed code
4. Update `hugo.toml`:
   ```toml
   [params.cusdis]
     host = "https://cusdis.com"
     appId = "YOUR_ACTUAL_APP_ID"
   ```
5. Moderate comments from the Cusdis dashboard

## Content Structure

```
content/
  sk/                    # Slovak content
    posts/               # Stories in Slovak
    about/_index.md      # About page
  en/                    # English content
    posts/               # Stories in English
    about/_index.md      # About page
```

### Front Matter Fields

```yaml
---
title: "Story Title"
date: 2026-03-15
categories: ["Pre deti"]     # Used for filtering
tags: ["rozprávka", "les"]   # Displayed on story page
thumbnail: "/images/pic.jpg" # Optional cover image
excerpt: "Short description" # Shown on story cards
---
```

### Pre-configured Categories

- **Pre deti** (For children)
- **Pre ženy** (For women)

Add more by simply using new category names in front matter.

## Project Structure

| Path | Purpose |
|------|---------|
| `hugo.toml` | Site configuration (languages, menus, params) |
| `content/` | Markdown content organized by language |
| `themes/storyteller/` | Custom theme (layouts, CSS, i18n) |
| `static/admin/` | Decap CMS configuration |
| `static/images/` | Uploaded images |
| `.github/workflows/` | GitHub Actions deployment |

## License

MIT
