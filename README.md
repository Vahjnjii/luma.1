# 🌟 LUMA — AI Content Studio

> A fully free, GitHub-powered AI image & video generator. No server, no subscription, no cloud costs — everything runs on GitHub Actions + GitHub Pages.

---

## ✨ What it does

- **Image Generator** — Paste any text → Groq AI structures it → GitHub Actions renders each post as a stunning 9:16 PNG image
- **Video Generator** — Type text → GitHub Actions creates an animated 9:16 video with your text overlay, stored in GitHub Releases

**100% free plan compatible** — uses GitHub Actions minutes (2,000/month free) and GitHub Releases (unlimited storage) and GitHub Pages (free hosting).

---

## 🚀 Setup (5 minutes)

### Step 1 — Fork this repo

Click **Fork** at the top right of this page. Give it any name you like.

---

### Step 2 — Get a Groq API Key (for images)

1. Go to [console.groq.com](https://console.groq.com)
2. Sign up (free)
3. Create an API key
4. Copy it — you'll need it in Step 4

---

### Step 3 — Get a GitHub Personal Access Token

1. Go to [github.com/settings/tokens](https://github.com/settings/tokens)
2. Click **"Generate new token (classic)"**
3. Name it `LUMA`
4. Select scope: ✅ `public_repo` (or `repo` if your fork is private)
5. Click **Generate token** and **copy it now** (you can't see it again!)

---

### Step 4 — Add Secrets to your repo

In your forked repo → **Settings** → **Secrets and variables** → **Actions** → **New repository secret**

Add these secrets:

| Secret name       | Value |
|-------------------|-------|
| `GH_PAT`          | Your GitHub Personal Access Token from Step 3 |
| `GROQ_API_KEY`    | Your Groq API key from Step 2 |

> **Video backgrounds**: The video workflow generates animated backgrounds using FFmpeg's built-in lavfi (no external dataset needed). If you want custom video backgrounds from Kaggle, also add `KAGGLE_USERNAME` and `KAGGLE_KEY`.

---

### Step 5 — Create Issue #1

In your repo, go to **Issues** → **New Issue**

- Title: `LUMA Job Queue` (any title works)
- Click **Submit new issue**

The issue number must be `1` (or update `CFG.issue` in `index.html`).

---

### Step 6 — Enable GitHub Pages

In your repo → **Settings** → **Pages**

- Source: **Deploy from a branch**
- Branch: `main` / `/ (root)`
- Click **Save**

Your app will be live at:
```
https://YOUR_USERNAME.github.io/YOUR_REPO_NAME/
```

---

### Step 7 — Configure the app

Open `index.html` and find the `CFG` object near the top of the `<script>` tag:

```javascript
const CFG = {
  owner:  'YOUR_GITHUB_USERNAME',   // ← Your GitHub username
  repo:   'YOUR_REPO_NAME',         // ← Your repo name (e.g. "luma")
  issue:  1,                        // ← Issue number (1 by default)
  token:  'ghp_YOUR_TOKEN_HERE',    // ← Your GitHub Personal Access Token
};
```

Replace the placeholder values, commit, and push.

---

## 💡 How it works

```
User types text → App posts comment to GitHub Issue
        ↓
GitHub Actions detects the comment → Workflow runs
        ↓
  [Image workflow]              [Video workflow]
  Calls Groq AI API             Runs FFmpeg
  Generates SVG images          Creates animated video
  Saves status JSON             Uploads to GitHub Releases
  Commits to repo               Writes status JSON
        ↓                              ↓
App polls raw.githubusercontent.com every 8s
        ↓
Shows results in the UI
```

---

## 📊 Free Plan Limits

| Resource | Free Limit | LUMA Usage |
|----------|-----------|------------|
| GitHub Actions minutes | 2,000/month | ~1-2 min per job |
| GitHub Pages | Free | Hosts the app + status files |
| GitHub Releases | Unlimited storage | Stores generated videos |
| Groq API | Free tier | AI text processing |

At ~1-2 minutes per job, you get **1,000–2,000 generations/month free**.

---

## 🔒 Security note

Your GitHub token is stored in `index.html` and visible in the browser source. This is acceptable for personal use with a minimal-scope token (`public_repo` only). For a production/public deployment, use a backend proxy to keep the token secret.

---

## 🛠 Project structure

```
├── index.html                    ← The entire app (single file)
├── api/
│   └── status/
│       ├── .gitkeep              ← Keeps folder in git
│       └── *.json                ← Job status files (auto-created by workflows)
├── .github/
│   └── workflows/
│       ├── render-image.yml      ← Image generation workflow
│       └── render-video.yml      ← Video generation workflow
└── README.md
```

---

## ❓ Troubleshooting

**"Token invalid"** → Check the token has `public_repo` scope and hasn't expired.

**"Repo not found"** → Verify `CFG.owner` and `CFG.repo` match your fork exactly.

**"Issue not found"** → Create Issue #1 in your repo, or update `CFG.issue`.

**Actions not triggering** → Make sure the workflow files are in `.github/workflows/` on the `main` branch.

**Pages not working** → Wait 2-3 minutes after enabling, then check Settings → Pages for the URL.

**Jobs timing out** → Free plan GitHub Actions can occasionally queue. Just wait a few minutes and try again.
