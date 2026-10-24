# MADBLOX252 — Portfolio Analysis

> **Status:** historical audit of the site as it was before the repair pass.
> Most items below have since been fixed — see the git history for details.

**Analysed:** 2026-10-23
**Live URL:** https://madbl0x252.github.io/Ro-Portfolio/ (verified 200)
**Repo:** `MADBL0X252/Ro-Portfolio`, branch `main`, last deploy **2026-07-15** (3 months stale)
**Stack:** Static site export (mirrored) → GitHub Pages via `deploy.yml`

**Overall: 5.5 / 10.** The design is genuinely good and the craft is visible. But the site
currently publishes unfinished template content, tells Google to rank a *different* domain
instead of this one, and gives a paying client no way to verify your work or contact you
by email. None of that is a design problem — it's roughly a day of fixes.

---

## 1. What's actually working

Verified, not assumed:

- **All 14 routes return 200.** `/`, `/showcase`, `/aboutme`, `/services`, `/contact`, `/tos`,
  `/building`, `/ui`, `/modelling`, `/animations`, `/gfx-thumbnails`, `/services/building`,
  `/services/ui`, `/services/scripting`.
- **Internal nav is fully relative** (`showcase/index.html`, `../aboutme/index.html`, …) —
  no dead links to the old the site builder domain in navigation.
- **3 responsive breakpoints** on every page: `≥1200px`, `810–1199px`, `≤809px`.
- **`lang="en"`, viewport, OG and Twitter cards** present on all 14 pages.
- **Assets are localised** — 22 homepage images, 6 showcase images, all `src` attributes point
  into `_external/`. 0 remote `<img src>`.
- **The ToS page is genuinely strong.** Payment thresholds (15,000 Robux upfront), revision
  policy, rights transfer, refund policy, confidentiality, Roblox ToU compliance. Most Roblox
  freelancers have nothing like this. It reads as professional.
- **Commission workflow is a 4-step process** with clear expectations. Good trust signal.

---

## 2. Critical — fix these first

### 2.1 Every page tells Google to rank the the site builder site, not this one

All 14 pages carry a canonical pointing at the old domain:

```html
<link href="https://the original template domain/" rel="canonical"/>
<link href="https://the original template domain/services" rel="canonical"/>
```

A canonical tag says *"the authoritative copy of this page lives there."* Search engines
will transfer any ranking signal from `madbl0x252.github.io/Ro-Portfolio/` to
`the original template domain/` and drop this site from the index. Your GitHub Pages site is
currently self-deindexing.

**Fix:** rewrite all 14 canonicals to `https://madbl0x252.github.io/Ro-Portfolio/<path>`, or
better, set a custom domain and canonicalise to that.

### 2.2 The Talentify job-board template is still live on your homepage

This is the single most damaging item. Your homepage has a full four-step section about a
recruitment platform:

> **Step 01 — Browse remote jobs.** Explore a curated selection of remote tech job listings
> that match your expertise and career goals.
> **Step 02 — Apply role of your choice.** Submit applications directly through Talentify…
> **Step 03 — Meet Talentify.** Have a quick chat with our team…
> **Step 04 — Land your dream role.** After the final interview process, secure your next
> remote tech job…

A Roblox developer's homepage, explaining how to apply for remote tech jobs. It appears
twice (desktop + mobile variants).

Related leftovers:

- Three page titles still read **`Building | Talentify – Website Template`**,
  `Scripting | Talentify – Website Template`, `UI Designing | Talentify – Website Template`
  (`services/building`, `services/scripting`, `services/ui`). These show in browser tabs,
  bookmarks, Discord/Twitter link previews and search results.
- In the "Why I'm better than most devs?" section, two of the four cards are **empty
  template stubs** with recruitment copy and no body text at all:
  `### Onboarding support.` and `### Diversify recruitment.`
  A third, `### Tailored Solutions, Not Templates`, has a heading and **no description**.
  So the section that's meant to sell you has 1 of 4 cards actually written.

**Fix:** delete the Talentify section entirely, rewrite the three empty cards, retitle the
three service subpages.

### 2.3 Two pages have the wrong title

| Page | Current `<title>` | Should be |
|---|---|---|
| `building/index.html` | `Modelling` | `Building` |
| `ui/index.html` | `Modelling` | `UI/UX Designing` |

Three pages now compete for the same "Modelling" title, and the Building/UI pages are
unfindable by name.

### 2.4 Fonts: 868 remote declarations, 0 local — and 1.3 MB of downloaded fonts nobody uses

Measured across all pages, every `@font-face` resolves to a remote CDN:

```
798  @font-face src → the external CDN
 70  @font-face src → fonts.gstatic.com
  0  @font-face src → local _external/
```

66 `@font-face` rules ship in each page's inline CSS. The mirror tool downloaded
**62 `.woff2` files (1.3 MB)** into `_external/` — but never rewrote the `src: url(...)`
values, so those files are fetched by nothing. They are committed to Git, uploaded on every
deploy, and served to nobody.

Consequences:

- The site's typography hard-depends on `the external CDN` staying up. If the the site builder
  project or plan lapses, fonts break site-wide.
- ~1.3 MB of dead weight in the repo and in every Pages artifact.
- No `rel="preload"` for the 2–3 fonts actually used above the fold → render-blocking swap.

**Fix:** point `@font-face src` at the local `_external/` copies you already have, delete the
61 unused font faces per page, keep 3–4, add `preload` for the display font.

### 2.5 No way to verify your work, and no way to email you

Two gaps that directly cost commissions:

**No Roblox links anywhere.** I grepped every page for `roblox.com` — zero matches. You claim
**531,000+ contributed visits**, **13.6K favorites**, **19 studios**. A client reading that has
no way to check any of it. No profile link, no group link, no experience links.

**The "Experiences that stand out" section on `/showcase` is empty.** It has a heading and a
paragraph — *"Dive into the worlds I've crafted, where unique gameplay, attention to detail…"*
— and then nothing. No games, no links, no cards. The only link in that whole section is the
nav link to `/aboutme`. This is the section a client most wants to see.

**"Trusted by roblox studios" logos are inert.** Codificators, Willzwu and Infinique Studio
appear as text/logos inside **0 anchor tags**. Unlinked social proof reads as decoration.

**No email, no form.** Across all 14 pages: `mailto:` ×0, `<form>` ×0, `<input>` ×0,
`<textarea>` ×0. The only contact paths are X, Instagram and
`https://discord.com/channels/@me/869443200304152606/` — a **DM deeplink**, which fails
silently for anyone you haven't got DMs open with, and which studios often can't use on
company accounts. For 15,000+ Robux commissions, a studio needs an email address.

---

## 3. High priority — content credibility

### 3.1 Your experience numbers contradict each other

| Location | Claim |
|---|---|
| Homepage H1 + all 14 meta descriptions | "6+ years" / "over 6+ years" |
| `/aboutme` | "over six years", "In **2019**, when I was 9 years old, I entered… Roblox development" |
| `/services/building` | "Experience: **7.5 years**" / "almost 8 years" |
| `/services/ui` | "Experience: **2.5 years**" |
| `/services/scripting` | "Experience: **0.8 years**" (body text says "6–7 months") |

2019 → 2026 is **7 years**, so "6+" undersells you and is stale. But "0.8 years" of scripting
directly undercuts the homepage H1 **"Roblox game Developer (Full Stack)"** — a full-stack
Roblox dev with 6–7 months of Lua is the first thing a technical client will question.

Also, **"over 6+ years"** is a double hedge (33 occurrences, sitewide). Pick one: "7+ years".

**Fix:** one number per skill, consistent everywhere. Consider leading with your strongest
discipline rather than "Full Stack" if scripting is genuinely junior.

### 3.2 Copy-pasted service descriptions

On `/showcase`, **UI Designing** has the exact GFX description:

> **UI Designing** — "I use blender for this, I've been using blender for around 6-7 years
> now. I usually charge around 200-10K Depending on complexity of the project."

That's verbatim the GFX/Thumbnails copy. It also contradicts `/services/ui`, which says UI
uses **Figma** with **2 years** experience. Two pages, two tools, two durations.

### 3.3 Placeholder testimonial is live

The homepage testimonial carousel leads with:

> **Example 21** — *"What can you do?"* — 8.5/10

`Example 21` is a the site builder placeholder name and "What can you do?" is placeholder copy. It's
the **first** thing in the carousel, so it's what every visitor sees first. It also appears
3 times across the carousel loops.

Also: a testimonial quoting *"I need to call my friend an #d$#t"* (10/10) is funny but
undercuts the professional tone you've built in the ToS.

### 3.4 The carousel repeats the same 5 testimonials 4 times

Same 5 clients (Example 21, Happy Blox, Fuse Tag, Kawaii, Click to Grow) rendered in four
loops. Combined with #3.3, a visitor scrolling sees 20 testimonials and realises there are 4.

### 3.5 Ratings presented without context

`8.5/10`, `6/10`, `10/10` labelled "Job Satisfaction". Publishing a **6/10** on your own
portfolio invites the question. Either drop the numeric scores and keep the quotes, or
aggregate ("Average client rating 9.1/10 across 14 commissions").

### 3.6 Typos

| Where | Text |
|---|---|
| Homepage hero CTA | **"Veiw Showcase"** → View Showcase |
| `/showcase` H2 (×2) | **"GFX/Thubmnails"** → GFX/Thumbnails |

The hero CTA is the highest-traffic button on the site.

### 3.7 Services page is missing 2 of your 5 services

`/showcase` promotes **5** categories: Animations, Modelling, GFX/Thumbnails, Building,
UI Designing.
`/services` lists **3**: Building, UI Designing, Scripting.

So **GFX/Thumbnails, Modelling and Animations — arguably your strongest work, with the most
showcase content — cannot be commissioned.** Meanwhile Scripting, your weakest area, gets a
dedicated booking page. The funnel is inverted.

Also `/services` has a **"Load More" button with 0 `onclick` handlers** — it does nothing.

---

## 4. Medium priority — performance

| Page | HTML | Local assets | Note |
|---|---|---|---|
| `/gfx-thumbnails` | 205 KB | **15.11 MB** | 11.74 MB video + 2.46 MB PNG |
| `/animations` | 199 KB | **12.65 MB** | 11.74 MB video |
| `/` | 429 KB | 2.73 MB | 1.70 MB PNG |
| `/showcase` | 231 KB | 2.20 MB | 1.25 MB PNG |

**Mitigating factor:** all 6 videos use `preload="none"` with `muted playsinline loop`, so
they don't download until interacted with. Good call by the site builder.

**Problems:**

- **No `poster` images on any of the 6 videos** → they render as blank boxes until play.
  Add a poster frame; it's the difference between a portfolio and a broken page.
- **Zero WebP/AVIF.** 69 PNGs, 6 SVGs, 0 WebP. Largest PNG is **865 KB**
  (`3RB4z40mHBnYa5rhIU1XAmkvM__q_81b8f09516.png`). Converting thumbnails to WebP would cut
  `/gfx-thumbnails` and `/showcase` by ~70%.
- **Alt text missing on 42 of 51 images** (9 have it). Zero on `/building`, `/ui`,
  `/modelling`, `/gfx-thumbnails`, and all 3 service subpages — your actual portfolio pages.
- **`loading="lazy"` on only 13 of 51 images.**
- Homepage HTML is **429 KB** — large for a page with 1,365 words of real content. the site builder
  inlines the full hydration payload.

---

## 5. Medium priority — SEO & infrastructure

- **No `robots.txt`** (404 verified), **no `sitemap.xml`** (404 verified),
  **no `404.html`** — GitHub Pages API confirms `custom_404: false`.
- **Multiple `<h1>` on two pages:** `/services` (2 — "View the services I provide" *and*
  "View services I provide", near-duplicate text) and `/contact` (2 — both "Reach out now.").
- **the site builder badge on all 14 pages:** "Create a free website with the site builder, the website builder
  loved by startups, designers and agencies." On a site charging up to 150K Robux.
- **Broken `<link rel="preconnect">`** on all 14 pages:
  `href="_external/fonts.gstatic.com/index.html"` — a preconnect to an HTML file.
- **Deploy artifact includes `.git` (23 MB).** `deploy.yml` uses `path: '.'`, so every deploy
  uploads the Git history. Excluding it would cut artifact size ~45%.
- **`.mirror_state.json` (20 KB)** — mirror tooling metadata — is deployed publicly.
- **the site builder analytics is broken:** the tracking script `src` was rewritten to a local HTML file
  (`_external/events.the template vendor/script/index__q_5e1f15b6b0.html`) served with an HTML
  content-type and parsed as JS. You have no analytics at all right now.

---

## 6. Low priority

- `og:image` uses a **relative** path (`_external/the external CDN/assets/uZW8ENFZ…png`).
  Most scrapers need an absolute URL — your link previews may show no image.
- `package.json` `build` is a naive `cpSync` of everything including `.git`.
- No `README.md` — nothing documents that this is a mirrored the site builder export or how to update it.
- No `.gitignore`.
- `/aboutme` discloses "when I was 9 years old". Honest, and a decent story, but it lets a
  client anchor on age rather than the 531K visits. Consider framing as years-of-experience
  and keeping the origin story secondary.

---

## 7. Recommended fix order

**Pass 1 — credibility (highest impact, ~2 hours)**
1. Delete the Talentify section from the homepage (both breakpoints).
2. Write the 3 empty "Why choose me" cards.
3. Retitle `services/building`, `services/scripting`, `services/ui` (drop "Talentify – Website Template").
4. Fix `building` → "Building", `ui` → "UI/UX Designing" page titles.
5. Fix "Veiw Showcase" and "GFX/Thubmnails".
6. Replace the "Example 21" testimonial.

**Pass 2 — conversion (~2 hours)**
7. Populate "Experiences that stand out" with real Roblox experiences + links.
8. Add your Roblox profile link (header and footer).
9. Add an email address and/or a working contact form to `/contact`.
10. Link the 3 studio logos.
11. Add GFX, Modelling and Animations to `/services`; remove or dead-end the inert "Load More".

**Pass 3 — SEO (~1 hour)**
12. Rewrite all 14 canonicals to the Pages domain.
13. Add `robots.txt`, `sitemap.xml`, `404.html`.
14. Fix duplicate `<h1>` on `/services` and `/contact`.
15. Make `og:image` absolute.
16. Remove the the site builder badge; fix or remove the broken preconnect.

**Pass 4 — performance (~2 hours)**
17. Repoint `@font-face` to local files; prune to the 3–4 fonts actually used; delete unused `.woff2`.
18. Convert showcase PNGs to WebP.
19. Add `poster` images to all 6 videos.
20. Add `alt` text to the 42 missing images; add `loading="lazy"` to below-fold images.
21. Exclude `.git` and `.mirror_state.json` from the deploy artifact.

---

## Method

Findings were produced by parsing all 14 pages, running the site locally on port 3000 and
requesting every route, resolving every internal link against the filesystem, classifying all
`@font-face` sources, counting image attributes, fetching the live GitHub Pages deployment,
and querying the GitHub API for Pages config and workflow run history.

**Not verified (no outbound access to those hosts):** whether the X, Instagram and Discord
links resolve to live accounts, and whether `the original template domain` is still online.
