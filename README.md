# norbyt3s — markmoreto.github.io

Mark Moreto's personal blog at [norbyt3s.com](https://norbyt3s.com).

Migrated from Hexo (2018) to **Astro 4 + TinaCMS** in 2026.

---

## Stack

| Layer | Technology |
|---|---|
| Static site generator | [Astro 4](https://astro.build) |
| Content format | MDX (Markdown + JSX) |
| Local CMS UI | [TinaCMS](https://tina.io) (local mode, no cloud account needed) |
| Styling | Plain CSS (`src/styles/global.css`) |
| Deployment | GitHub Actions → GitHub Pages |
| Custom domain | `norbyt3s.com` via `public/CNAME` |

---

## System Requirements

- **Node.js**: 20 or higher (see `.nvmrc`)
- **npm**: 10 or higher (comes with Node 20)
- **nvm** (recommended): to manage Node versions

> The system default Node may be older. Always confirm you're on Node 20+ before running any command.

---

## First-Time Setup

```bash
# 1. Switch to the correct Node version
nvm use 20

# (Optional) Make Node 20 your permanent default so you don't need to run this every time
nvm alias default 20

# 2. Install dependencies
npm install
```

---

## Commands

| Command | What it does |
|---|---|
| `npm run dev` | Starts Astro dev server at `http://localhost:4321` |
| `npm run cms` | Starts Astro dev server + TinaCMS editor UI at `http://localhost:4001/index.html#/` |
| `npm run build` | Builds the static site into `dist/` |
| `npm run preview` | Serves the built `dist/` locally to preview production output |

### When to use `dev` vs `cms`

- Use **`npm run dev`** when you are editing code (layouts, styles, pages).
- Use **`npm run cms`** when you want to write or edit blog posts through a visual UI instead of editing MDX files manually.

---

## Project Structure

```
markmoreto.github.io/
├── .github/
│   └── workflows/
│       └── deploy.yml          # GitHub Actions: build + deploy on push to master
├── public/                     # Static files copied as-is to dist/
│   ├── CNAME                   # Custom domain: norbyt3s.com
│   ├── google5af05bfb1659fb8e.html  # Google Search Console domain verification
│   ├── images/                 # Favicon, logo, apple-touch-icon
│   └── blog/                   # Blog post images organised by slug
│       ├── keto-fasting-diet/
│       └── cloud-torrent/
├── src/
│   ├── content/
│   │   ├── config.ts           # Astro Content Collection schema (Zod validation)
│   │   └── blog/               # Blog posts as .mdx files — one file per post
│   ├── layouts/
│   │   ├── BaseLayout.astro    # HTML shell: <head>, nav, footer
│   │   └── BlogPost.astro      # Wraps BaseLayout with article header + meta
│   ├── pages/
│   │   ├── index.astro         # Home page — lists all posts
│   │   ├── about.astro         # About page
│   │   └── blog/
│   │       ├── index.astro     # /blog — all posts list
│   │       └── [...slug].astro # /blog/<slug> — individual post page
│   └── styles/
│       └── global.css          # All CSS — no framework, plain variables + classes
├── tina/
│   └── config.ts               # TinaCMS schema: defines CMS fields for blog posts
├── astro.config.mjs            # Astro config: site URL, integrations (MDX)
├── tsconfig.json               # TypeScript config (extends astro/tsconfigs/base)
├── .nvmrc                      # Pins Node version to 20
└── package.json
```

---

## How Content Works

### Writing a new post (via CMS UI — recommended)

1. Run `npm run cms`
2. Open `http://localhost:4001/index.html#/`
3. Click **Blog Posts → New Post**
4. Fill in: Title, Date, Description, Tags, Cover Image, Body
5. Click **Save** — TinaCMS writes the `.mdx` file to `src/content/blog/`
6. Commit and push

### Writing a new post (manually)

Create a file in `src/content/blog/your-post-slug.mdx`:

```mdx
---
title: "Your Post Title"
description: "A short summary shown in the post list."
date: 2026-05-24
tags: ["tag1", "tag2"]
cover: "/blog/your-post-slug/banner.jpg"
---

Your content here in **Markdown**.

## Section heading

More content...
```

Put any images for the post in `public/blog/your-post-slug/` and reference them as `/blog/your-post-slug/image.jpg`.

### Content schema

Defined in `src/content/config.ts` and mirrored in `tina/config.ts`:

| Field | Type | Required | Notes |
|---|---|---|---|
| `title` | string | yes | Post title |
| `description` | string | yes | Shown in post list and meta tags |
| `date` | date | yes | Used for sorting |
| `tags` | string[] | no | Displayed as pills on the post |
| `cover` | string | no | Path to cover image, e.g. `/blog/slug/banner.jpg` |
| `body` | MDX | yes | The post body (everything below the frontmatter) |

---

## How Deployment Works

### Automatic (production)

Every push to the `master` branch triggers `.github/workflows/deploy.yml`:

1. GitHub Actions checks out the repo
2. Installs dependencies with `npm ci` on Node 20
3. Runs `npm run build` → Astro generates the `dist/` folder
4. Uploads `dist/` as a GitHub Pages artifact
5. Deploys to GitHub Pages

The live site at `norbyt3s.com` is updated within ~1–2 minutes of a push.

### GitHub Pages configuration (one-time)

In the repo settings on GitHub:
**Settings → Pages → Source → GitHub Actions**

This must be set to "GitHub Actions" (not "Deploy from a branch") for the workflow to work.

### What goes in `dist/`

Astro copies everything from `public/` into `dist/` verbatim, then adds the compiled HTML pages. Key files in `dist/`:

- `index.html` — home page
- `about/index.html` — about page
- `blog/index.html` — post listing
- `blog/<slug>/index.html` — each post
- `CNAME` — tells GitHub Pages to use `norbyt3s.com`
- `google5af05bfb1659fb8e.html` — Google Search Console verification
- `images/` — favicons, logo
- `blog/<slug>/` — post images

---

## Maintaining the Site

### Updating dependencies

```bash
nvm use 20
npm update
npm run build   # verify nothing broke
```

### Adding a new page (not a blog post)

Create `src/pages/your-page.astro`, import `BaseLayout`, and add a nav link in `src/layouts/BaseLayout.astro`.

### Changing the theme / styles

All styles live in `src/styles/global.css`. CSS custom properties at the top of the file control colours, fonts, and max width:

```css
:root {
  --color-text: #1a1a1a;
  --color-accent: #2563eb;  /* link colour */
  --max-width: 720px;
  ...
}
```

### Changing the nav links

Edit the `<ul>` inside `src/layouts/BaseLayout.astro`.

---

## Debugging

### Build fails locally

```bash
nvm use 20        # confirm Node 20 is active
node --version    # should print v20.x.x
npm run build
```

### Page not found after deploy

- Check the GitHub Actions run in the **Actions** tab of the repo — look for red failures.
- Confirm GitHub Pages source is set to **GitHub Actions** in repo settings.
- The `CNAME` file must be in `public/` (it is) so it gets included in `dist/`.

### TinaCMS editor won't open

- Make sure you ran `npm run cms`, not `npm run dev`.
- CMS UI is at `http://localhost:4001/index.html#/` — not at the Astro port (4321).
- If port 4001 is taken, kill the process: `lsof -ti:4001 | xargs kill`

### Content not showing up

- Check the frontmatter of the `.mdx` file — `date` and `title` are required.
- Run `npm run dev` and look at the terminal for Content Collection validation errors.

### Google Search Console / domain verification

- The verification file `google5af05bfb1659fb8e.html` is in `public/` and is deployed automatically.
- It is served at `https://norbyt3s.com/google5af05bfb1659fb8e.html`.

---

## History

| Year | Stack |
|---|---|
| 2018 | Hexo static site generator, compiled HTML pushed directly to `master` |
| 2026 | Migrated to Astro 4 + TinaCMS, deployed via GitHub Actions |
