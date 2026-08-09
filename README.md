# genavise.com

The Genavise Marketing website. Plain HTML and CSS — no build step, no framework,
nothing to install. Every file here is exactly what the browser receives.

**Everything sits at the top level on purpose.** No subfolders. GitHub's drag-and-drop
uploader flattens folders, which silently breaks the paths, so this version avoids
folders entirely.

## Files

```
index.html              home page
about-us.html           About Us
booking.html            Book A Strategy Call  ← paste your scheduler embed here
privacy-policy.html     Privacy Policy
terms-conditions.html   Terms & Conditions
disclaimer.html         Disclaimer
404.html                shown for any unknown URL

site.css                ONE stylesheet shared by every page
logo.png                the wordmark (header + footer, all pages)
favicon.ico             browser tab icon
favicon-32.png          tab icon, modern browsers
apple-touch-icon.png    icon when saved to an iPhone home screen

robots.txt              lets search engines crawl the site
sitemap.xml             lists all six pages for search engines
BUILD-SPEC.md           full design spec — every font, colour and measurement
```

---

## Image slots

Drop files in the top level using these exact names and the pages pick them up
automatically. Each slot already has its space reserved, so nothing shifts while
images load. Any slot with no file shows a soft grey block at the right shape — an
unfinished page still looks deliberate rather than broken.

| Filename | Display size | Where |
|---|---|---|
| `logo.png` | 220 × 48 header, 110 × 44 footer | every page |
| `proof-ads-manager.png` | 894 × 202 | home, results section |
| `step-1-facebook-ads.png` | 480 × 284 | home, Step 1 |
| `step-2-crm-automation.png` | 446 × 309 | home, Step 2 |
| `step-3-calendar.png` | 476 × 309 | home, Step 3 |

Supply screenshots at **2× the listed size** so they stay sharp on phones and retina
screens. Aspect ratios are locked in CSS — an image of a different shape gets
centre-cropped, not stretched.

For the logo, SVG is better than PNG (sharp at any size). If you use one, search each
`.html` file for `logo.png` and change it to `logo.svg`.

---

## Putting it online — GitHub + Cloudflare Pages

Free, no watermark, custom domain included. About 15 minutes.

### 1. GitHub repository

1. **github.com** → **+** top-right → **New repository**
2. Name it `genavise-site`, keep it **Public**, don't tick "Add a README"
3. **Create repository**

### 2. Upload

Click **uploading an existing file**, then drag in **the contents of this folder** —
all 16 files at once, not the folder itself.

`index.html` must end up at the top level of the repo. If you see a folder name in the
file list instead of the files, delete it and re-drag the contents.

Scroll down → **Commit changes**.

### 3. Cloudflare Pages

1. **dash.cloudflare.com** → sidebar **Workers & Pages** → **Create** → **Pages** tab
   → **Connect to Git**
2. Authorise GitHub, pick `genavise-site`
3. Build settings — **leave all of it blank**:
   - Framework preset: **None**
   - Build command: *(blank)*
   - Build output directory: `/`
   
   There is nothing to compile. Any build command here will fail the deploy.
4. **Save and Deploy**

You get a live URL like `genavise-site.pages.dev` within a minute. Open it and click
every page before going further.

### 4. Point genavise.com at it

1. In the Pages project: **Custom domains** → **Set up a domain**
2. Add `genavise.com`, then repeat for `www.genavise.com`
3. Follow the DNS records Cloudflare gives you

**The domain currently points at Framer.** The moment you switch DNS, the Framer
version goes dark — so confirm the `.pages.dev` URL is right first. HTTPS is automatic
and free.

### 5. After it's live

- Cancel Framer — the custom-domain fee is what you're dropping
- Submit the sitemap in Google Search Console: `https://genavise.com/sitemap.xml`
- Add redirects if any old Framer URLs differ from these filenames, so existing links
  and Google results don't break

---

## Editing later

Click any file on GitHub → pencil icon → edit → commit. Cloudflare redeploys in under
a minute. No re-uploading, no cache to clear.

- **Text** → the relevant `.html` file
- **Colours, sizes, spacing** → `site.css`. One change updates all six pages
- **Images** → replace the file, keep the name
- **Booking widget** → `booking.html`, in the block marked `SCHEDULER EMBED SLOT`

## Still to do

- [ ] Replace `logo.png` with the real logo (a stand-in is in place)
- [ ] Add the four screenshots in the table above
- [ ] Paste the scheduler embed into `booking.html`
- [ ] Replace the three favicon files — currently a placeholder `G`
