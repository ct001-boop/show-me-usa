# Show-me: Where to Buy (US) mini site

Single-file static site. Lists US retailers, tracks page views and per-retailer link clicks in Google Analytics 4.

**Live:** https://show-me-usa.vercel.app
**Repo:** https://github.com/ct001-boop/show-me-usa
**GA4 property:** Show-me US Where to Buy (account: Eastpoint Global) — Measurement ID `G-JMVXK6FJYP`

Every push to `main` redeploys automatically. Editing `index.html` on GitHub in the browser is enough to update the live site.

## Files

```
index.html      the whole site (HTML + CSS + JS in one file)
vercel.json     caching headers + security headers
README.md       this file
```

---

## 1. Add or edit retailers

Everything you edit is in one array in `index.html`, near the bottom under `RETAILER LIST`:

```js
{
  name: "Quill",
  url: "https://www.quill.com/search?keywords=show-me&filter=Brand_Show-me",
  blurb: "Full Show-me range priced by the pack and the class set. Best for offices and school buyers.",
  tags: ["Bulk & classroom packs", "Business accounts", "Full range"],
  logo: null,          // or "logos/quill.svg"
  color: "#c8102e",    // wordmark colour used when logo is null
  ctaName: "Quill",    // optional shorter label for the button
  enabled: true        // false = greyed out "Coming soon" card, no link
}
```

| Field | What it does |
|---|---|
| `name` | Card heading **and** the label used in your GA4 reports. Keep it stable once live, or your reporting history splits across two labels. |
| `url` | Exact destination. All six now point at brand-filtered pages, not keyword searches. |
| `blurb` | One line under the name. |
| `tags` | Small pills. Use `[]` for none. |
| `logo` | Path to an image, or `null` for a styled text wordmark. |
| `color` | Wordmark colour. Must clear 3:1 on white, and all six clear 5:1. Do not use a retailer's raw brand hex without checking: Amazon's `#ff9900` is 2.1:1 and fails. |
| `ctaName` | Optional shorter name for the button, so a long retailer name does not wrap it onto two lines. Omit to use `name`. |
| `enabled` | `false` renders a placeholder card that is not clickable and sends no events. |

All six cards are live: Amazon, Walmart, Target, Quill, School Specialty and Geyer Instructional. There are no placeholders left.

**Three lists must agree.** If you change a URL, change it in all three places or the page will contradict itself:

1. the `RETAILERS` array at the foot of `index.html`
2. the `<noscript>` block inside `<section class="grid">` (the no-JavaScript fallback)
3. the JSON-LD `ItemList` in `<head>` (what Google reads)

All six URLs were verified live on 9 Aug 2026 and each returns genuine Show-me products: Amazon 66 results, Walmart 27, Target 32, Quill 29, School Specialty 6, Geyer 27.

### To edit

GitHub → `index.html` → pencil icon → edit → **Commit changes**. Vercel redeploys in about 20 seconds.

### Using real logos

Create a `logos/` folder in the repo and add files (SVG or transparent PNG, roughly 200x60), then set `logo: "logos/amazon.svg"`. Most retailers publish brand assets in their affiliate or partner portal. Check each retailer's brand guidelines before use.

### Copy

Copy is US English: dry erase (not drywipe), lap boards and dry erase boards (not mini whiteboards), classroom packs (not class packs), school supplies (not stationery), authorized, retailer (not stockist). Keep new copy consistent with that.

The contact sentence links to the show-me.uk contact form rather than a mailto, so no individual work address is published.

---

## 2. Reading your numbers in GA4

Standard reports lag 24 to 48 hours. **Realtime** updates within seconds.

### Site visitors

**Reports** → **Engagement** → **Pages and screens**. Views and users for the mini site.

### Clicks per retailer

**Quick total** — **Reports** → **Engagement** → **Events** → `retailer_click`. Total clicks and total users who clicked.

**Broken down by retailer** — the `Retailer` custom dimension is already registered against the `retailer_name` parameter. Build the report:

**Explore** → **Blank** → **Dimension** = `Retailer`, **Metric** = `Event count`, filter `Event name exactly matches retailer_click`. Save it and it stays in your Explore list.

Note: custom dimensions do not backfill. `Retailer` was registered on 8 Aug 2026, so data from that point forward is covered.

### Events being sent

| Event | When | Parameters |
|---|---|---|
| `page_view` | Automatic on page load | standard |
| `retailer_click` | Retailer card clicked | `retailer_name`, `link_url`, `link_domain`, `list_position` |
| `click` | Same click, GA4 standard outbound event | `link_url`, `link_domain`, `link_text`, `outbound` |

`retailer_click` is the one to report on. The `click` event is a backup so clicks appear in GA4's built-in reports with no configuration. Enhanced measurement is also on at the stream level, which catches outbound clicks independently.

---

## 3. Custom domain (optional)

Vercel project → **Settings** → **Domains** → add e.g. `us.show-me.co.uk`, then add the CNAME record Vercel gives you at your DNS provider. After that, update the two `show-me-usa.vercel.app` references in `index.html` (canonical and og:url).

---

## Branding

Styling follows show-me.uk:

| Token | Value | Used for |
|---|---|---|
| `--ink` | `#212121` | Body text, headings, CTA buttons |
| `--accent` | `#00aa84` | Show-me teal: note bar, focus ring, dot |
| `--accent-dark` | `#008e6e` | Teal for hover fills and the skip link |
| `--tint` | `#f5f5f5` | Tag pills, note panel, footer |
| `--line` | `#e3e4e6` | Card borders |
| Typeface | Work Sans (400-800) | Loaded from Google Fonts |

The logo and favicon are pulled live from show-me.uk, so a rebrand there flows through automatically. If that URL ever moves, the header falls back to a styled text wordmark rather than breaking. To self-host instead, drop the file in the repo and change the `<img src>` in the masthead.

Dark mode was removed. The Show-me identity is a white and black system with a single teal accent, and a dark variant pulled it off-brand.

## Notes

- Cards open in a new tab. This keeps the page alive so the analytics event always completes before the user leaves.
- Ad blockers block GA4. Expect real click counts to be roughly 10 to 30 percent higher than GA4 reports. The relative ranking between retailers stays accurate, which is usually what matters.
- No cookie banner. GA4 sets cookies, so UK and EU visitors need consent under PECR/GDPR. The page targets the US, but it is reachable from anywhere. Worth a check with whoever handles Eastpoint's privacy compliance.
- GA4 reporting time zone is United Kingdom; currency is USD.
- Mobile layouts and keyboard navigation work with no extra setup. Dark mode is deliberately not implemented.
- Accessibility: all wordmark colours clear 5:1 on white, the eyebrow teal was darkened to `#00775c` for AA, and "Coming soon" cards use muted colour tokens rather than `opacity`, which used to drag their text below AA.
- With JavaScript disabled the grid renders a plain `<noscript>` list of the six retailers instead of a blank page.
- Structured data: a JSON-LD `@graph` in `<head>` describes the page, the Show-me brand and the six retailers as an `ItemList`.
