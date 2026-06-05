# App Privacy Center — `privacy.amit-lal.com`

Static site that hosts the **privacy policy** for every app you publish
(PyMaster today, more later). Built to host on **Cloudflare Pages** under a
dedicated subdomain so it never collides with your existing GitHub Pages site on
the apex domain.

```
privacy-site/
├── index.html            ← hub: lists all apps  → privacy.amit-lal.com/
├── pymaster/index.html   ← PyMaster policy      → privacy.amit-lal.com/pymaster/
├── _template-app/        ← copy this to add a new app
├── assets/style.css      ← shared dark theme (matches the app)
├── _headers              ← Cloudflare security headers
├── 404.html · robots.txt · sitemap.xml
└── PRIVACY-POLICY.md     ← Markdown source of the PyMaster policy
```

---

## Why Cloudflare Pages (not GitHub Pages) for this?

| | Cloudflare Pages | GitHub Pages |
|---|---|---|
| Your apex `amit-lal.com` | already used by GitHub Pages | already in use |
| Add `privacy.` subdomain | **1 click** (domain already in Cloudflare) | needs extra repo + DNS |
| You already run Pages projects | ✅ yes (2 of them) | — |
| Many apps / folders | ✅ trivial | ✅ ok |
| Global CDN, free SSL, security headers | ✅ built-in | basic |

**Recommendation:** host this on **Cloudflare Pages** at **`privacy.amit-lal.com`**.
Your App Store URL for PyMaster becomes:

```
https://privacy.amit-lal.com/pymaster/
```

> You could also keep it dead-simple on GitHub Pages
> (`https://amitlals.github.io/<repo>/pymaster/`), but since you asked for
> Cloudflare and already use Pages, the subdomain route below is cleaner and
> gives you a branded, permanent URL.

---

## Deploy — Option A: Direct upload (fastest, ~3 min)

Best if you just want it live right now.

1. Go to **Cloudflare dashboard → Workers & Pages → Create → Pages → Upload assets**.
2. Name the project, e.g. `app-privacy`.
3. Drag the **contents of this `privacy-site/` folder** (not the parent folder)
   into the upload box → **Deploy**.
4. You'll get a `https://app-privacy.pages.dev` URL. Verify it loads.
5. Add your domain → see **[Custom domain](#custom-domain)** below.

To update later, re-upload the folder (creates a new deployment).

---

## Deploy — Option B: Git-connected (best for many apps)

Auto-deploys every time you push. Recommended long-term.

1. Put this `privacy-site/` folder in its **own GitHub repo** (e.g.
   `amitlals/app-privacy`). From inside the folder:
   ```bash
   git init
   git add .
   git commit -m "App privacy center: PyMaster + hub"
   git branch -M main
   git remote add origin https://github.com/amitlals/app-privacy.git
   git push -u origin main
   ```
2. Cloudflare dashboard → **Workers & Pages → Create → Pages → Connect to Git**.
3. Pick the repo. Build settings:
   - **Framework preset:** `None`
   - **Build command:** *(leave empty)*
   - **Build output directory:** `/`  (the repo root is already the site)
4. **Save and Deploy.** Now every `git push` redeploys automatically.

> If you'd rather keep the folder inside this monorepo, set
> **Root directory** = `privacy-site` in the Pages build settings and output
> directory = `/`.

---

## <a name="custom-domain"></a>Point `privacy.amit-lal.com` at it

Because `amit-lal.com` is **already in your Cloudflare account**, this is one step
and the DNS record is created for you:

1. Open your Pages project → **Custom domains → Set up a custom domain**.
2. Enter `privacy.amit-lal.com` → **Continue → Activate domain**.
3. Cloudflare automatically adds the `CNAME privacy → <project>.pages.dev`
   record and issues SSL. Live in ~1–2 minutes.

That's it — no manual DNS edits. (You do **not** touch the existing apex/`www`
GitHub Pages records.)

---

## Add a new app later (2 minutes)

1. Copy the `_template-app/` folder → rename to your app slug, e.g. `myapp/`.
2. Find & replace the `{{PLACEHOLDERS}}` at the top of its `index.html`.
3. Edit the sections to match what that app actually collects.
4. Add a card in `index.html` and a `<url>` in `sitemap.xml`.
5. Push (Option B) or re-upload (Option A).
   → Live at `https://privacy.amit-lal.com/myapp/`.

---

## Preview locally

```bash
# from inside privacy-site/
python -m http.server 8080
# open http://localhost:8080
```

---

## Paste into App Store Connect

App Store Connect → your app → **App Privacy** and **App Information →
Privacy Policy URL**:

```
https://privacy.amit-lal.com/pymaster/
```

The policy already covers everything in your `app.json` privacy manifest
(email + name, linked, **not** used for tracking) plus the AI-tutor (Groq)
disclosure and the in-app **Profile → Delete Account** flow Apple requires.
