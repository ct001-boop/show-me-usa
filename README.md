# Show-me: Where to Buy (US) mini site

Single-file static site. Lists US retailers, tracks page views and per-retailer link clicks in Google Analytics 4.

**Live:** https://show-me-beige.vercel.app
**Repo:** https://github.com/ct001-boop/show-me
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
  blurb: "Full Show-me brand range. Best for schools and bulk classroom orders.",
  tags: ["Bulk / class packs", "School accounts", "Full range"],
  logo: null,          // or "logos/quill.svg"
  color: "#c8102e",    // wordmark colour used when logo is null
  enabled: true        // false = greyed out "Coming soon" card, no link
}
```

| Field | What it does |
|---|---|
| `name` | Card heading **and** the label used in your GA4 reports. Keep it stable once live, or your reporting history splits across two labels. |
| `url` | Exact destination. Amazon and Walmart currently point at search URLs; swap for a brand store page if you have one. |
| `blurb` | One line under the name. |
| `tags` | Small pills. Use `[]` for none. |
| `logo` | Path to an image, or `null` for a styled text wordmark. |
| `enabled` | `false` renders a placeholder card that is not clickable and sends no events. |

Retailers 4, 5 and 6 are disabled placeholders. Fill in the name, url and blurb, then set `enabled: true`.

### To edit

GitHub → `index.html` → pencil icon → edit → **Commit changes**. Vercel redeploys in about 20 seconds.

### Using real logos

Create a `logos/` folder in the repo and add files (SVG or transparent PNG, roughly 200x60), then set `logo: "logos/amazon.svg"`. Most retailers publish brand assets in their affiliate or partner portal. Check each retailer's brand guidelines before use.

### Still placeholder

- The contact sentence near the bottom of the page has no link. To add one, follow the HTML comment right below it.

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

Vercel project → **Settings** → **Domains** → add e.g. `us.show-me.co.uk`, then add the CNAME record Vercel gives you at your DNS provider. After that, update the two `show-me-beige.vercel.app` references in `index.html` (canonical and og:url).

---

## Notes

- Cards open in a new tab. This keeps the page alive so the analytics event always completes before the user leaves.
- Ad blockers block GA4. Expect real click counts to be roughly 10 to 30 percent higher than GA4 reports. The relative ranking between retailers stays accurate, which is usually what matters.
- No cookie banner. GA4 sets cookies, so UK and EU visitors need consent under PECR/GDPR. The page targets the US, but it is reachable from anywhere. Worth a check with whoever handles Eastpoint's privacy compliance.
- GA4 reporting time zone is United Kingdom; currency is USD.
- Dark mode, mobile layouts and keyboard navigation all work with no extra setup.
