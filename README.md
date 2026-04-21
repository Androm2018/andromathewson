# andromathewson.com

Personal site for Andro Mathewson — researcher in war studies and OSINT practitioner.

Static HTML/CSS. No build step, no framework. Hosted on GitHub Pages behind Cloudflare DNS.

## Structure

```
.
├── index.html          About / landing
├── research.html       Publications and writing
├── projects.html       Unmanned Systems Tracker + others
├── contact.html        Contact details and links
├── style.css           Stylesheet (all pages)
├── CNAME               Custom-domain file for GitHub Pages
├── assets/             Images, CV PDF, etc.
└── README.md
```

## Local preview

```bash
# any static server will do
python3 -m http.server 8000
# then visit http://localhost:8000
```

## Deployment — GitHub Pages + Cloudflare

### 1. Push to GitHub

Create a new public repo (for example `andromathewson-site`) and push:

```bash
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin git@github.com:YOUR-USERNAME/andromathewson-site.git
git push -u origin main
```

### 2. Enable GitHub Pages

In the repo on GitHub → **Settings → Pages**:

- **Source:** Deploy from a branch
- **Branch:** `main`, folder `/ (root)`
- Save. The first build takes a minute.

GitHub will note that the site uses the custom domain `andromathewson.com` (picked up from the `CNAME` file). Leave the "Enforce HTTPS" box unchecked for now — you'll tick it once DNS is live.

### 3. Point Cloudflare DNS at GitHub

In the Cloudflare dashboard for `andromathewson.com` → **DNS → Records**, add:

| Type  | Name | Content                          | Proxy  |
| ----- | ---- | -------------------------------- | ------ |
| A     | @    | 185.199.108.153                  | DNS only |
| A     | @    | 185.199.109.153                  | DNS only |
| A     | @    | 185.199.110.153                  | DNS only |
| A     | @    | 185.199.111.153                  | DNS only |
| CNAME | www  | YOUR-USERNAME.github.io          | DNS only |

Important: set **Proxy status to DNS only (grey cloud), not Proxied (orange)**. GitHub Pages handles its own TLS via Let's Encrypt, and Cloudflare's proxy interferes with the certificate issuance on a custom apex domain. Once the site is live and HTTPS is working, you can experiment with turning the proxy back on if you want Cloudflare's analytics / cache, but start with it off.

In Cloudflare → **SSL/TLS → Overview**: set mode to **Full**. Not Flexible.

### 4. Verify and enforce HTTPS

- Wait ~10 minutes for DNS to propagate (often faster).
- Visit `https://andromathewson.com`. Confirm it loads over HTTPS with a valid certificate.
- Back in GitHub → **Settings → Pages**, tick **Enforce HTTPS**.

### 5. Add `www` → apex redirect (optional)

If you want `www.andromathewson.com` to redirect to the apex, add a Cloudflare Page Rule or Redirect Rule: forward `www.andromathewson.com/*` → `https://andromathewson.com/$1` (301).

## Editing

Everywhere you see square brackets `[like this]`, there's a placeholder to fill in:
- `index.html` — institution, dissertation topic, publication venues
- `research.html` — real publications
- `contact.html` — email, handles, ORCID, scholar profile

Search the repo for `[` to find them all.

## Adding a publication

In `research.html`, copy an existing `<li class="entry">` block and adjust. The year goes in `.entry__year`, title in `.entry__title`, journal/venue in `.entry__meta`, links in `.entry__desc`.

## Adding a photo

Drop a file (e.g. `portrait.jpg`) into `assets/` and add an `<img>` in `index.html` inside `.hero` — the layout leaves room for one if you want it.

## Design notes

- Fonts: Fraunces (display), Newsreader (body), IBM Plex Mono (labels)
- Palette: warm off-white paper (#f4f0e6), near-black ink (#1a1612), deep ink-blue accent (#1e3a5f)
- Single shared stylesheet, no JS (except fonts)
- Respects `prefers-reduced-motion`
