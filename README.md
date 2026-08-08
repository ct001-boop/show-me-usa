# Show-me: Where to Buy (US) mini site

Single-file static site. Lists US retailers, tracks page views and per-retailer link clicks in Google Analytics 4.

## Files

```
index.html      the whole site (HTML + CSS + JS in one file)
logos/          optional: drop retailer logo images here
vercel.json     caching headers + clean URLs
README.md       this file
```

---

## 1. Set up GA4 (5 minutes)

1. Go to [analytics.google.com](https://analytics.google.com) → **Admin** (bottom left).
2. **Create** → **Property**. Name it e.g. `Show-me US Where to Buy`. Set reporting time zone and currency.
3. Choose **Web** as the platform. Enter your site URL (you can change it later) and a stream name.
4. Copy the **Measurement ID**. It looks like `G-A1B2C3D4E5`.
5. Open `index.html`, find this line near the top and paste your ID in:

```js
var GA_MEASUREMENT_ID = 'G-XXXXXXXXXX';
```

That is the only place the ID appears.

> Until you replace it, the site still works and logs clicks to the browser console, but nothing is sent to Google.

---

## 2. Add your retailers

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
| `name` | Card heading **and** the label used in your GA4 reports. Keep it stable once live, or your reporting history splits. |
| `url` | Exact destination. Amazon and Walmart are pre-filled with search URLs; swap for a brand store page if you have one. |
| `blurb` | One line under the name. |
| `tags` | Small pills. Use `[]` for none. |
| `logo` | Path to an image, or `null` for a styled text wordmark. |
| `enabled` | `false` renders a placeholder card that is not clickable and sends no events. |

Retailers 4, 5 and 6 ship as disabled placeholders. Fill them in and set `enabled: true`.

### Using real logos

Put files in `logos/` (SVG or transparent PNG, roughly 200x60), then set `logo: "logos/amazon.svg"`. Most retailers publish brand assets in their affiliate or partner portal. Check each retailer's brand guidelines before use.

### Also replace

- `REPLACE-WITH-YOUR-DOMAIN.vercel.app` in the `<link rel="canonical">` and Open Graph tags (2 places)
- `REPLACE@yourdomain.com` in the contact link near the bottom of the page

---

## 3. Deploy

### Push to GitHub

```bash
cd showme-us-buy
git init
git add .
git commit -m "Show-me US where-to-buy mini site"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/YOUR-REPO.git
git push -u origin main
```

### Deploy on Vercel

1. [vercel.com/new](https://vercel.com/new) → **Import** your GitHub repo.
2. Framework preset: **Other**. Leave build command and output directory empty.
3. **Deploy**.

Live in about 20 seconds. Every push to `main` redeploys automatically.

### Custom domain (optional)

Vercel project → **Settings** → **Domains** → add e.g. `us.yourdomain.com`, then add the CNAME record Vercel gives you at your DNS provider.

---

## 4. Reading your numbers in GA4

Data takes 24 to 48 hours to appear in standard reports. **Realtime** works within seconds, so use that to confirm tracking works.

### Confirm it works

**Reports** → **Realtime**. Open your live site, click a retailer card, and watch `retailer_click` appear in the event count card.

For detail, use **Admin** → **DebugView** with the [Google Analytics Debugger](https://chrome.google.com/webstore/detail/google-analytics-debugger/jnkmfdileelhofjcijamephohjechhna) Chrome extension on. You'll see each event with its parameters.

### Site visitors

**Reports** → **Life cycle** → **Engagement** → **Pages and screens**. Views and users for the mini site.

### Clicks per retailer

Two ways:

**Quick** — **Reports** → **Engagement** → **Events**, click `retailer_click`. You get total clicks and total users who clicked.

**Broken down by retailer** — GA4 needs a custom dimension registered before it will report on the `retailer_name` parameter:

1. **Admin** → **Data display** → **Custom definitions** → **Create custom dimension**
2. Dimension name: `Retailer`
3. Scope: **Event**
4. Event parameter: `retailer_name`
5. Save.

Important: custom dimensions only populate from the moment you create them. They do not backfill. Set this up before you drive any traffic.

Then build the report: **Explore** → **Blank** → set **Dimension** = `Retailer`, **Metric** = `Event count`, and add a filter `Event name exactly matches retailer_click`. That gives you a clean clicks-per-retailer table you can save and re-open.

### Events being sent

| Event | When | Parameters |
|---|---|---|
| `page_view` | Automatic on page load | standard |
| `retailer_click` | Retailer card clicked | `retailer_name`, `link_url`, `link_domain`, `list_position` |
| `click` | Same click, GA4 standard outbound event | `link_url`, `link_domain`, `link_text`, `outbound` |

`retailer_click` is the one to report on. The `click` event is a backup so clicks appear in GA4's built-in reports without any configuration.

---

## Notes

- Cards open in a new tab. This keeps the page alive so the analytics event always completes before the user leaves.
- Ad blockers block GA4. Expect your real click count to be roughly 10 to 30 percent higher than GA4 reports. The relative ranking between retailers stays accurate, which is usually what matters.
- No cookie banner is included. GA4 sets cookies, so if you expect EU or UK visitors you need consent. Since this is a US-targeted page, check with whoever handles your privacy compliance before launch.
- Dark mode, mobile layouts and keyboard navigation all work with no extra setup.
