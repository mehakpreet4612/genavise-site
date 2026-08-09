# Build Spec — Genavise.com (pixel-exact replication, all 6 pages)

**READ THIS FIRST.** You are being asked to rebuild the whole site at
`https://genavise.com` — the home page plus five subpages — as static files.
You do not need to visit the site. Every measurement, colour, font setting, and word
of copy you need is in this document. All values were extracted from the live page's
computed styles at a 1536 px-wide viewport on 2026-08-08.

> **Version note.** The live Genavise page was measured first, then deliberately
> tuned: the wordmark on the real site is a 220px-wide image whose weight the
> live-text version was under-shooting, and the whole page was asked to read a
> touch bolder and larger. **The values in this document are the tuned target — build
> exactly these.** Where a value was changed from the live site, the original is shown
> in brackets like `(site: 700)` so you can tell measurement from intent. Do not
> "correct" a tuned value back to the site value.

**Rules for you, the builder:**

1. Put all CSS in ONE shared `assets/site.css` linked by all six pages — see §13.
   No CSS or JS frameworks.
2. Do not invent, improve, reword, or "polish" anything. Copy strings are verbatim,
   including the curly apostrophes (’), em dashes (—), en dashes (–), and the
   middle dot (·). Do not straighten quotes.
3. Do not add anything not described here — no dividers, no drop shadows on sections,
   no gradients, no borders, no accent colour, no hero background image, no dark mode.
   The page is pure white with black/grey type. Restraint is the design.
4. Where this spec gives a pixel value, use that pixel value. Do not substitute a
   "nicer" round number.
5. Build desktop first, then apply the two breakpoints in §10.

---

## 1. What this page is

A single-scroll marketing landing page for **Genavise**, a done-for-you lead
generation agency for residential real estate agents. Aesthetic: Apple-style
minimalism — pure white canvas, one tight geometric sans in two weights, black and
three greys, generous-to-extreme vertical whitespace, fully-rounded pill buttons,
big numeric proof points, no decoration whatsoever.

Total document height: **8771 px** on the live site at 1536 px wide; **8693 px** for
this tuned build at 1512 px. It is a long, airy page — the whitespace is not a bug, it
is the design. Do not compress it.

**Document head:**

```html
<html lang="en">
<title>Genavise Marketing</title>
<meta name="description" content="Lead generation and appointment setting systems for real estate agents using Facebook ads and automated follow-up.">
<meta property="og:title" content="Genavise Marketing">
<meta property="og:description" content="Lead generation and appointment setting systems for real estate agents using Facebook ads and automated follow-up.">
<meta name="viewport" content="width=device-width, initial-scale=1">
```

---

## 2. Typography — THE most important section

### 2.1 The typeface

The entire page uses **Inter Variable** and nothing else. No secondary font, no
serif, no mono. Load the variable font including the **optical-size (`opsz`) axis** —
this is required, because the display sizes are set to `opsz 32`, which is a visibly
different (tighter, more display-like) shape than default Inter.

> **⚠ Do NOT load Inter from Google Fonts.** The Google Fonts build of Inter is
> subsetted and **strips the `cvXX` character-variant features** listed in §2.2. The
> CSS will still say `font-feature-settings: "cv11" 1`, the browser will report it as
> applied, and the glyphs will silently not change. This was tested and confirmed.
> The result is a page that looks "almost right" and is impossible to debug.
> Use the **official Inter distribution**, whose family name is `InterVariable`:

```html
<link rel="preconnect" href="https://rsms.me/">
<link rel="stylesheet" href="https://rsms.me/inter/inter.css">
```

Font stack: `font-family: "InterVariable", "Inter var", "Inter", sans-serif;`

Equivalent self-hosted options (both retain all features):
`npm i inter-ui` → `inter-ui/variable-latin/InterVariable-subset.woff2`, or the
`InterVariable.woff2` from the official Inter GitHub release. Declare it as:

```css
@font-face{
  font-family:"InterVariable";
  src:url("InterVariable.woff2") format("woff2");
  font-weight:100 900; font-style:normal; font-display:swap;
}
```

For a fully offline single file, inline that woff2 as a `data:font/woff2;base64,…`
URL. That is the only way to guarantee identical rendering with no network.

### 2.2 OpenType features — do not skip this

Every text element on the page carries these Inter character variants. **This is the
single most commonly-missed detail.** `cv11` swaps in the single-storey `a`, which is
why the wordmark and headlines read geometric rather than like default Inter. Without
these five features the page will look "close but wrong."

```css
font-feature-settings: "cv03" 1, "cv04" 1, "cv09" 1, "cv11" 1;
-webkit-font-smoothing: antialiased;
-moz-osx-font-smoothing: grayscale;
```

(The live site also declares `"blwf" 1`. That is an Indic below-base-form feature and
is inert for Latin text — include it for byte-fidelity if you like, or omit it. The
four `cvXX` variants are the ones that matter.)

Apply to `html, body` and let it inherit. Do not override it anywhere.

**How to prove it worked** (tested and verified — this is the single reliable check):
render `agy 3469` at 110px, weight 700, once with the features on and once off, and
compare. With the features active you get a **single-storey `a`**, a **straight-leg
`g`**, a **straight-tailed `y`**, a **flat-top `3`**, and an **open `4`, `6` and `9`**.
If the two renders look identical, your font build has the features stripped — swap
the font source before you continue. Cross-check against the live site's own
`$564.30`, `Less Chasing.` and `40 Days` — those glyph shapes are the tell.

### 2.3 Variation axes

Set `font-variation-settings` explicitly per role. The `wght` axis carries the weight
(do **not** rely on `font-weight` alone):

Define these five as CSS custom properties so all boldness is tunable from one place:

| Role | `font-variation-settings` | was |
|---|---|---|
| Wordmark (GENAVISE) | `"opsz" 32, "wght" 900` + 2px stroke | max-weight build — see §5.3 |
| Display / headings / big numbers | `"opsz" 32, "wght" 800` | (site: 700) |
| Stat & pillar labels | `"opsz" 32, "wght" 700` | (site: 600) |
| Nav links, FAQ questions | `"opsz" 24, "wght" 600` | (site: 500) |
| Button labels | `"opsz" 20, "wght" 700` | (site: 600) |
| Long-form paragraphs (17/18 px) | `"opsz" 24, "wght" 450` | (site: 400) |
| 16 px grey body | `"opsz" 24, "wght" 550` | (site: 500) |

Fractional weights (450, 550) are valid and intended — this is a variable font, so
they render as true intermediate weights, not synthesised bold.

### 2.4 Type scale (desktop ≥ 1200 px)

Line-height and letter-spacing in `em` so they scale. **Build the "Size" column.**
The `(site: …)` note records what the live page used before the bolder/larger pass.

| Token | Size | Line-height | Weight | Letter-spacing | Colour | Align |
|---|---|---|---|---|---|---|
| `--wordmark` | **44px** | 48px box | **900** + 2px stroke | **+0.03em** | `#000` | left |
| `--display-hero` | **44px** (site: 40) | 1.0em | **800** | −0.04em | `#000` | center |
| `--display-section` | **44px** (site: 40) | 1.1em | **800** | −0.04em | `#000` | center |
| `--stat-number` | **68px** (site: 64) | 1.0em | **800** | −0.04em | `#000` | center |
| `--stat-label` | 22px | 1.0em | **700** | −0.02em | `#999` | center |
| `--stat-label-tight` | 22px | 1.2em | **700** | −0.02em | `#999` | center |
| `--h2-lead` | **34px** (site: 32) | 1.2em | **800** | −0.03em | `#000` | left |
| `--h2-flush` | **34px** (site: 32) | 1.0em | **800** | −0.03em | `#000` | left |
| `--quote` | **34px** (site: 32) | 1.3em | **800** | −0.03em | `#000` | center |
| `--h2-case` | **32px** (site: 30) | 1.4em | **800** | −0.04em | `#000` | left |
| `--h2-guarantee` | **38px** (site: 36) | 1.2em | **800** | −0.03em | `#000` | center |
| `--pillar-label` | 22px | 1.0em | **700** | −0.02em | `#000` | center |
| `--step-body` | 22px | 1.2em | 450 | −0.02em | `#999` | left |
| `--cta-sub` | 22px | 1.1em | 450 | −0.02em | `#999` | center |
| `--attribution` | 18px | 1.4em | 500 | −0.01em | `#999` | center |
| `--body-guarantee` | **18px** (site: 17) | 1.6em | 450 | −0.01em | `rgba(0,0,0,.7)` | center |
| `--body-case` | **17px** (site: 16) | 1.6em | 450 | −0.02em | `#666` | left |
| `--footer-link` | 16px | 1.2em | 450 | 0 | `#000` | left |
| `--nav-link` | **16px** (site: 15) | 1.0em | **600** | −0.01em | `#999` | center |
| `--body-16` | **16px** (site: 15) | **1.4em** (site 1.3) | **550** | −0.01em | `#777` | left |
| `--faq-q` | **16px** (site: 15) | 1.2em | **600** | −0.01em | `#000` | left |
| `--faq-a` | **16px** (site: 15) | **1.4em** | 450 | −0.01em | `#999` | left |
| `--btn-label` | **15px** (site: 14) | 1.2em | **700** | −0.005em | `#fff` / `#000` | center |
| `--eyebrow-caps` | 14px | 1.2em | **700** | **+0.12em** | `rgba(0,0,0,.45)` | center |
| `--eyebrow-case` | **17px** (site: 16) | 1.6em | 450 | −0.02em | `#666` | left |
| `--fineprint` | 13px | 1.5em | 450 | 0 | `rgba(0,0,0,.4)` | center |

Paragraph spacing (gap between stacked `<p>` inside one text block): **20px** for
13–18 px body copy, **40px** for headline-scale blocks.

---

## 3. Colour palette — complete, nothing omitted

There are no other colours on this page. No brand accent. No blue. No gradient.

```css
--white:        #FFFFFF;  /* page background, every section background */
--black:        #000000;  /* headings, primary button fill, footer links, FAQ questions */
--grey-999:     #999999;  /* muted headline half, stat labels, step body, FAQ answers */
--grey-777:     #777777;  /* the three "Why most agents struggle" paragraphs */
--grey-666:     #666666;  /* case-study body copy + case-study eyebrow */
--grey-eee:     #EEEEEE;  /* secondary button fill, FAQ row fill */
--ink-70:       rgba(0,0,0,0.70);  /* guarantee body copy */
--ink-45:       rgba(0,0,0,0.45);  /* "THE GUARANTEE" eyebrow */
--ink-40:       rgba(0,0,0,0.40);  /* guarantee fine print */
--on-black:     #FFFFFF;  /* label on primary button */
```

---

## 4. Layout system

- `body { background:#fff; margin:0; }`
- One outer wrapper: `display:flex; flex-direction:column; padding:0 20px;`
  → the page gutter is **20px on each side**, always.
- **Gotcha, verified the hard way:** because the wrapper is a *column* flexbox, adding
  `margin:0 auto` to a centred child **cancels cross-axis stretch**, so the child
  shrinks to fit its content and every internal column collapses (the 4-up pillar row
  silently becomes ~576px instead of 960px). Give every centred block
  `width:100%` **alongside** `max-width` and `margin:0 auto`.
- Content is centred with per-block max-widths (this page does **not** use one global
  container width — match these individually):

| Block | Max width | Notes |
|---|---|---|
| Nav row | 1158px | centred |
| Hero text + buttons | 920px | centred, column, `gap:50px` |
| Results stat row | 1155px | 3 equal columns of 385px |
| "Why most agents struggle" | 948px | column, `gap:22px`; paragraphs clamp to 600px |
| Steps (features) grid | 960px | 2-column rows |
| Pillars row (4 icons) | 960px | 4 columns of 240px |
| Case stat row | 960px | 3 columns of 320px |
| Testimonial quote | 1160px | centred |
| Case study block | 948px | heading full width; paragraphs clamp to 640px |
| Guarantee block | 948px | heading full width; paragraphs clamp to 640px; fine print 600px |
| FAQ block | 655px | centred |
| Final CTA | 941px | centred |
| Footer row | 1081px | centred |

---

## 5. Components

### 5.1 Primary button ("Book A Strategy Call")

**The pill hugs its label. It is never a fixed width and never full-bleed.**

```
width: auto; min-width: 0;   /* size to the text + padding, nothing else */
height: 46px;                /* (site: 45px) */
display:inline-flex; align-items:center; justify-content:center;
padding: 0 24px;             /* (site: 0 12px) — the label needs room at 15px/700 */
white-space: nowrap;         /* the label must never wrap */
background: #000; color: #fff;
border-radius: 100px;        /* fully rounded pill */
border: none; box-shadow: none;
font-size: 15px; line-height: 1.2em;
font-variation-settings: "opsz" 20, "wght" 700;
letter-spacing: -0.005em; text-align:center; text-decoration:none;
cursor: pointer; transition: all .2s ease;
```

Resulting widths — these are outputs, not inputs. Do not hard-code them:

| Label | Desktop | Mobile (padding 0 20px) |
|---|---|---|
| `Book A Strategy Call` | 204px | 196px |
| `See How It Works` | 179px | 171px |

For a centred button use `.btn-center{display:flex; width:fit-content; margin:0 auto}`.

> **Do NOT write `.btn{width:100%; max-width:320px}` in the mobile block.** That is the
> obvious-looking mobile pattern and it is wrong here: it stretches the pill across the
> whole screen and destroys the look. On mobile the pill only loses a little padding
> (`padding:0 20px`) — it stays label-width. Same rule at every breakpoint.
Appears **6 times**: nav (top-right), hero, after Step 3, after testimonial,
inside guarantee, in final CTA. Every instance links to `/booking`.
Hover: `opacity:.85` (subtle only — no colour change, no lift, no shadow).

### 5.2 Secondary button ("See How It Works")

Identical geometry (auto width, height 46, radius 100px, 15px/700 label) but
`background:#EEEEEE; color:#000;`. Appears **once**, in the hero, to the right of the
primary button. Links to `#how-it-works`.

### 5.3 Logo / wordmark — maximum-weight build

The word **GENAVISE**, all caps, pure black, flush-left.

**A hard fact about variable fonts first.** Inter's weight axis runs 100–900. **900 is
the ceiling.** Writing `font-variation-settings: "wght" 2000` — or 1200, or 950 — is
silently clamped by the font to 900. It renders pixel-identically to 900, throws no
error, and shows nothing in devtools to tell you it was ignored. **Any weight beyond
900 must come from `-webkit-text-stroke`, not from the axis.**

Build these values:

```css
.logo{                          /* header — renders ink 234 × 35px */
  font-size: 44px;
  font-variation-settings: "opsz" 32, "wght" 900;   /* axis max */
  letter-spacing: 0.03em;       /* opened up so the letters don't weld at this weight */
  line-height: 48px; height: 48px;
  text-transform: uppercase;
  color: #000; display: block;
  -webkit-text-stroke: 2px currentColor;
  paint-order: stroke fill;     /* keeps the G, A and S counters crisp */
}
.footer-logo{                   /* footer — ~117px wide */
  font-size: 22px;
  font-variation-settings: "opsz" 32, "wght" 900;
  letter-spacing: 0.03em;
  line-height: 44px; height: 44px;
  text-transform: uppercase;
  -webkit-text-stroke: 1px currentColor;
  paint-order: stroke fill;
}
```

Stroke scales with size, or small logos turn to mush: **2px** desktop (44px),
**1.7px** tablet (37px), **1px** mobile (23px), **0.9px** ≤360 (20px), **1px** footer.

#### Where this sits relative to the real logo

The live logo asset was measured at high resolution (median stem width ÷ cap height):

| | stem ÷ cap | vs live logo |
|---|---|---|
| Inter Bold 700 | 0.209 | 13% lighter |
| **Live Genavise logo = Inter ExtraBold 800** | **0.240** | — |
| Inter Black 900 | 0.273 | +14% |
| **This build: 900 + 2px stroke** | **0.343** | **+43%** |

So this is a deliberate override, **not** a match to the live site. The authentic logo
is **Inter ExtraBold (800) at 44px, tracking +0.01em, no stroke** — that lands ink of
exactly 220 × 33px, width/cap 6.667, stem/cap 0.242. Revert to it by setting the
wordmark axis to 800, tracking to `.01em`, and deleting both stroke declarations.

#### The ceiling

**2px is the practical maximum.** Measured behaviour past it:

- 2.4px — letters begin welding; distinct letterform count drops from 7 to 5
- 3.0px — `GENAVISE` reads as one connected blob, stem/cap jumps to 0.694 because
  adjacent letters have merged into single runs
- ink coverage saturates around 0.85 — extra stroke fills the counters rather than
  thickening the stems, so it stops looking bolder and starts looking broken

If more weight than this is genuinely needed, **the typeface has to change** — Inter
has nothing left to give. An ultra-heavy display face such as **Anton** or
**Archivo Black** carries far thicker stems natively, with counters designed for that
weight. That would mean the wordmark no longer matches the body font.

Header logo links to `/`. **Note:** the wordmark's tracking is *positive*; body copy
and headings are all negatively tracked. The logo is the exception.

### 5.4 FAQ accordion row

```
width: 655px; height: 40px (collapsed);
background: #EEEEEE; border-radius: 10px;
display:flex; align-items:center; gap:~14px; padding-left: ~20px;
```
- Left icon: a thin `+` glyph, ~14px, `#999`, centred in a ~20px box at 20px inset.
- Question: `--faq-q` (15px / 500 / #000), left aligned.
- Rows are stacked with **7px gap**.
- Expanded state: icon becomes a `−` (minus). Row height animates to fit the answer.
  Answer sits below the question inside the same `#EEEEEE` rounded box, styled
  `--faq-a` (15px / 1.3em / 400 / `#999`), left aligned, same left inset as the
  question, with ~12px space above it and ~16px padding at the bottom of the row.
- Only one row may be open at a time; clicking an open row closes it.
- All rows start **closed**.
- Expand/collapse transition: `height .3s ease-in-out`, icon rotates/crossfades over
  `.2s`.

### 5.5 Pillar icons

Four **48 × 48 px** solid-black line-style glyph icons (SVG, `fill:#000`, no stroke),
in this order left→right: **lightning bolt**, **contact card / person**,
**envelope**, **calendar**. Any clean minimal icon set matching those four concepts is
acceptable; they must be monochrome black, visually light-weight, and identical in
optical size.

---

## 6. Section-by-section build order

Coordinates are the measured top offsets (`y`) at 1536 px viewport width — use them to
verify your vertical rhythm. The generous gaps between sections are intentional.

### §6.1 Sticky header — y 0 → 160

- `position: sticky; top: 0; z-index: 10; background:#FFFFFF;` no blur, no shadow,
  no bottom border. **The header stays white and flat while scrolling** — it does not
  shrink, gain a shadow, or change on scroll.
- Height 160px; content row 1158px wide; `padding-top: 20px`.
- Left: logo, 220 × 48, at 66px from page top.
- Centre-right: four nav links, `--nav-link` (15px / 500 / `#999`), spaced ~26–38px
  apart, baseline at y ≈ 80. Order and targets, left to right:
  `How It Works → #how-it-works`, `Results → #results`, `About Us → /about-us`,
  `FAQs → #faqs`. Hover: colour to `#000`.
- Far right: primary button, at y 68.

### §6.2 Hero — y 160 → 744

Centred column, 920px wide, `gap: 50px`. Vast empty space above the headline — the
headline sits at y 365, i.e. ~205px below the header. Do not remove it.

- **H1, two lines, one element with a line break.** Line 1 black, line 2 `#999`:
  - `More Real Estate Conversations.` — `--display-hero`, `#000`
  - `Less Chasing.` — `--display-hero`, `#999`
  (40px / 1.0em / 700 / −0.04em, centred.)
- Button row at y 495: 350px wide, `gap: 10px`, centred — primary
  `Book A Strategy Call` then secondary `See How It Works`.

### §6.3 Results proof — y 744 → 1592  ·  `id="results"`

Enormous padding: `376px 0`. `scroll-margin-top: 247px` on the anchor.

- Three-column stat row at y 1120, total 1155px, each column 385px, centred text:

| Number (`--stat-number`) | Label (`--stat-label`) |
|---|---|
| `33` | `Buyer Leads Generated` |
| `$17.10` | `Cost Per Lead` |
| `65%+` | `Answered in 5 Minutes` |

  Number at y 1120, label at y 1194 (≈10px gap).
- Below it, a **screenshot proof image**: 894 × 202 px, centred (x 313),
  `border-radius: 20px`, `object-fit: cover`, at y 1256. Content: a Facebook Ads
  Manager table row with `33`, `$17.10`, and `$564.30` circled in red. Source asset
  1568 × 314. If you lack the asset, render a 894 × 202 placeholder with
  `border-radius:20px` and `background:#EEEEEE` — do not substitute stock art.

### §6.4 "Why most agents struggle" — y 1592 → 2445

948px column, `padding: 185px 0 389px`, `gap: 22px`, left-aligned text.

- Heading at y 1815, `--h2-32-lead` (32px / 1.2em / 700 / −0.03em / `#000`):
  `Why most agents struggle`
- Three paragraphs, `--body-15` (15px / 1.3em / 500 / `#777`), clamped to ~600px,
  at y 1876 / 1937 / 2017:

  1. `Most agents don't need more leads. They need control over two things: who comes in, and what happens in the first five minutes after they do.`
  2. `Lists of names that go to voicemail — or renters who do answer — aren't a lead problem. They're a targeting and speed-to-lead problem. Both are fixable.`
  3. `We filter before your phone rings and trigger a callback within 5 minutes — so your calling time lands on real buyers who still remember raising their hand.`

### §6.5 How It Works — three steps — y 2445 → 4287  ·  `id="how-it-works"`

960px grid, `scroll-margin-top: 155px`. Three rows that **alternate sides**, each
~700px tall with large empty space between them. Text column ~320px wide; heading
uses `--h2-32-flush` (32px / 1.0em / 700), body uses `--step-body` (22px / 1.2em /
400 / `#999`), ~62–74px below the heading.

| Row | Text side | Image side | Image size | Radius |
|---|---|---|---|---|
| Step 1 | left, x 360 (heading y 2513) | right, x 760, y 2503 | 480 × 284 | 10px |
| Step 2 | right, x 843 (heading y 3201) | left, x 295, y 3149 | 446 × 309 | 20px |
| Step 3 | left, x 360 (heading y 3922) | right, x 762, y 3870 | 476 × 309 | 20px |

Copy:

1. **`Step 1: We build the offer and ads.`**
   `We craft a compelling real estate offer, write your ad copy, and launch high-converting Facebook campaigns — testing multiple variants to find the winner before scaling spend.`
   Image: Facebook Ads Manager campaign table.
2. **`Step 2: Automated lead nurture.`**
   `Every new lead is called within 5 minutes, automatically — then SMS and email follow-up keeps them warm so no one falls through the cracks.`
   Image: CRM automation workflow canvas with "Update opportunity" nodes.
3. **`Step 3: Appointment setting.`**
   `Ready-to-talk buyers get fast follow-up and land directly on your calendar — so your hours go to showings and closings, not chasing.`
   Image: booking calendar with time slots (09:15 AM, 09:30 AM, 09:45 AM).

All three images are real product screenshots on white, `object-fit: cover`
(Step 3 uses `contain`). Use `#EEEEEE` rounded placeholders at the exact sizes if you
lack the assets.

### §6.6 Mid-page CTA — y 4287

A single centred primary button, `Book A Strategy Call`. Nothing else — no heading.

### §6.7 Four pillars — y 4332 → 4802

960px row, `padding: 190px 0`. Four equal 240px columns at x 280 / 520 / 760 / 1000.
Each column: 48 × 48 icon (y 4522), centred, then label at y 4590 using
`--pillar-label` (22px / 1.0em / 600 / **`#000`**).

| Icon | Label |
|---|---|
| lightning bolt | `Facebook Ads` |
| contact card | `CRM Automation` |
| envelope | `SMS & Email` |
| calendar | `Appointments` |

### §6.8 Campaign stat row — y 4802 → 4999

960px row, `padding: 60px 0 36px`. Three 320px columns at x 280 / 600 / 920.
Numbers at y 4862 (`--stat-number`, 64px / 700), labels at y 4936
(`--stat-label-tight`, 22px / 1.2em / 600 / `#999`).

| Number | Label |
|---|---|
| `$564.30` | `Total Ad Spend` |
| `14 Ads` | `Tested — One Winner Scaled` |
| `40 Days` | `Maryland Buyer Campaign` |

### §6.9 Testimonial — y 4999 → 5821

`padding: 182px 20px`, `gap: 46px`, centred.

- Quote at y 5310, `--quote` (32px / 1.3em / 700 / −0.03em / `#000`), max-width
  1160px, centred, wrapped in **curly** typographic quotes:

  `“The biggest difference was consistency. Before, everything felt reactive — referrals, random inquiries, hoping something would come through. Now there’s an actual system creating conversations and following up automatically.”`

- Attribution at y 5522, `--attribution` (18px / 1.4em / 500 / `#999`), centred:
  `Maryland Residential Real Estate Agent`
- Primary button at y 5608, centred.

No avatar, no photo, no card, no quotation-mark graphic, no star rating.

### §6.10 Case study — y 5821 → 6719

948px column, `padding: 192px 0`, `gap: 22px`, left-aligned.

- Eyebrow at y 6013, `--eyebrow-case` (16px / 1.6em / 400 / `#666`), **not**
  uppercase-transformed — the string is already caps:
  `CASE STUDY · MARYLAND`  ← note the middle dot `·`
- Heading at y 6060, `--h2-case` (30px / 1.4em / 700 / −0.04em / `#000`), max 948px:
  `33 buyer leads. $17.10 each. Every one called within 5 minutes.`
- Three paragraphs, `--body-case` (16px / 1.6em / 400 / `#666`), clamped to **640px**,
  at y 6124 / 6274 / 6399:

  1. `A Maryland residential agent came to us with leads from referrals, Zillow, and a paid agency behind him. The pattern was the same across all of it: call the number, nobody answers — and when somebody did, it was usually a renter. He didn't need more leads. He needed control over who came in and what happened in the first five minutes after they did.`
  2. `Between April 21 and May 30, the campaign generated 33 buyer leads on $564.30 in ad spend — $17.10 per lead, against a typical range of $25–50. That number came from testing: 14 ad variants ran, one won at $15.06 per lead, and roughly 85% of the budget was concentrated behind it once it proved out.`
  3. `The number that mattered most: 65–70% of leads answered the automated 5-minute callback. A person called within five minutes still has their phone in hand and remembers raising it. A person called hours later is a cold call with extra steps. Of the leads who answered, a meaningful share booked a meeting with the agent — we only publish figures we can back with a screenshot.`

  (`$25–50` and `65–70%` use en dashes.)

### §6.11 Guarantee — y 6719 → 7476

948px column, `padding: 165px 0`, `gap: 24px`, centred.

- Eyebrow at y 6884, `--eyebrow-caps`: 14px / 1.2em / 600 / **letter-spacing
  +0.12em** / `text-transform: uppercase` / `rgba(0,0,0,.45)` / centred:
  `THE GUARANTEE`
- Heading at y 6924, `--h2-guarantee` (36px / 1.2em / 700 / −0.03em / `#000`),
  centred:
  `30 qualified buyer leads in 90 days. Or every dollar back.`
- Two paragraphs, `--body-guarantee` (17px / 1.6em / 400 / `rgba(0,0,0,.7)`),
  **centred**, clamped to 640px, at y 6992 / 7097:

  1. `If we don't generate at least 30 qualified buyer leads in your service area in your first 90 days, you get 100% of your service fee back — and you keep the entire system we built. The campaigns, the funnels, the automations. All of it.`
  2. `A lead only counts toward the 30 if it's in your market, in your price band, buying within 12 months, buying for themselves, and reachable at a real number. If we wouldn't call it qualified, it doesn't count.`

- Primary button at y 7203, centred.
- Fine print at y 7272, `--fineprint` (13px / 1.5em / 400 / `rgba(0,0,0,.4)`),
  centred, clamped to 600px:
  `Requires maintaining your agreed minimum ad spend. Buyer campaigns only. We guarantee qualified leads — closings are your craft.`

### §6.12 FAQ — y 7476 → 8020  ·  `id="faqs"`

655px column, `gap: 7px`, `scroll-margin-top: 20px`.

- Section heading at y 7476: `FAQs` — 40px / **1.1em (44px)** / 700 / −0.04em /
  `#000`, horizontally centred in the column.
- Then **nine** accordion rows per §5.4, in this exact order. Answers preserve their
  internal line breaks and bullet lists exactly as written.

1. **`Do I need to know how to run Facebook ads?`**
   `No. We handle the setup, systems, automation, and strategy for you.`
   (blank line)
   `You do not need to learn ad managers, funnels, CRMs, or complicated tech. Our goal is to simplify the process so you can focus on conversations and closings.`

2. **`What if I’ve been burned by agencies before?`**
   `A lot of agents we talk to have.`
   (blank line)
   `Usually the issue is not just the ads themselves — it’s weak follow-up, poor positioning, slow response times, or no real system behind the leads.`
   (blank line)
   `We focus on the full process:`
   `ads, automation, nurture, and appointment flow — not just “running traffic.”`

3. **`Are the leads actually serious buyers and sellers?`**
   `Not every lead becomes a client — and anyone promising that is lying.`
   (blank line)
   `What matters is creating a system that consistently brings in real conversations with people actively thinking about buying or selling, then following up properly so opportunities are not lost.`
   (blank line)
   `The goal is predictable conversations, not random luck.`

4. **`How fast can we launch?`**
   `Most systems can be launched within a few days after onboarding, depending on how quickly we receive access to accounts and assets.`
   (blank line)
   `We keep the setup process simple and streamlined.`

5. **`What happens after someone becomes a lead?`**
   `This is where most agents lose opportunities.`
   (blank line)
   `Once someone submits their information:`
   - `follow-up starts quickly`
   - `nurture messages begin`
   - `leads stay organized`
   - `conversations move toward appointments`
   (blank line)
   `Fast response time matters more than most people realize.`

6. **`Do you work with every real estate agent?`**
   `No.`
   (blank line)
   `We are selective about who we work with because systems only work when there is communication, follow-up, and commitment on both sides.`
   (blank line)
   `We care more about long-term results than onboarding as many clients as possible.`

7. **`I already get referrals. Why would I need this?`**
   `Referrals are great.`
   (blank line)
   `The problem is they are unpredictable.`
   (blank line)
   `A lead generation system helps create a more consistent flow of opportunities instead of relying only on word-of-mouth and market timing.`

8. **`What if I’m not good with technology?`**
   `That’s exactly why most agents hire help.`
   (blank line)
   `The systems are built to simplify your workflow — not make your life more complicated.`
   (blank line)
   `We guide you through setup and keep the process straightforward.`

9. **`What will actually happen on the strategy call?`**
   `We’ll look at:`
   - `your current lead flow`
   - `where opportunities may be getting lost`
   - `your follow-up process`
   - `what’s realistic in your market`
   - `whether this is even a good fit`
   (blank line)
   `Come with three rough numbers: what a lead costs you today, how many answer when you call, and how many hours a week you spend chasing. We’ll put your numbers next to ours and see if the gap is worth closing. If it isn’t, we’ll tell you that too.`
   (blank line)
   `No pressure. No hard pitch. Just clarity.`

> Row geometry check: collapsed rows sit at y 7616, 7661, 7706, 7753, 7798, 7843,
> 7890, 7935, 7980 — nine rows, 40px tall, 7px apart, each 655px wide starting at
> x 433. Build exactly these nine, in this order, all collapsed on load.

### §6.13 Final CTA — y 8020 → 8534

941px centred column, `gap: 44px`.

- Heading at y 8188, `--h2-32-flush` but **centred** (32px / 1.0em / 700 / −0.03em /
  `#000`): `Ready to turn ad traffic into real conversations?`
- Subline at y 8230, `--cta-sub` (22px / 1.1em / 400 / `#999`), centred:
  `Book your free strategy call now.`
- Primary button at y 8336, centred.

### §6.14 Footer — y 8534 → 8771

Row layout, 1081px wide, `padding: 53px 20px 90px`, `gap: 40px`, **no top border and
no divider rule**, background `#FFFFFF`.

- **Column 1** (x 240): logo 110 × 44 at y 8587, then a two-line tagline directly
  beneath, 15px / 1.3em / 400 / `#000`, left aligned, with a hard line break:
  `Lead generation systems`
  `for real estate agents.`
- **Column 2** — `flex: 0 0 124px`: stacked links, `--footer-link`
  (16px / 1.2em / `#000`), ~30px apart: `How It Works` (`#how-it-works`),
  `Results` (`#results`), `About Us` (`/about-us`), `FAQs` (`#faqs`).
- **Column 3** — `flex: 0 0 156px`: `Privacy Policy` (`/privacy-policy`),
  `Terms & Conditions` (`/terms-conditions`), `Disclaimer` (`/disclaimer`) —
  16px / 1.2em / `#000`, ~30px apart, each `white-space: nowrap`.
- **Column 4** — `flex: 0 0 auto; white-space: nowrap`: one line, 16px / `#000`:
  `mehak@genavise.com · Washington D.C.`  ← middle dot, not a hyphen.

Column 1 takes `flex: 1 1 auto; min-width: 0` and absorbs the slack. **At 16px these
two strings will wrap into two lines unless columns 3 and 4 are widened and set to
`nowrap`** — `Terms & Conditions` and the contact line are the two that break. Fix by
sizing the columns, never by shrinking the text back down.

Footer links: hover `opacity:.6`. No social icons. No copyright line. No newsletter form.

---

## 7. Links & anchors — complete map

| Label | Target |
|---|---|
| Logo (header) | `/` |
| `Book A Strategy Call` (all 6) | `/booking` |
| `See How It Works` | `#how-it-works` |
| `How It Works` | `#how-it-works` |
| `Results` | `#results` |
| `FAQs` | `#faqs` |
| `About Us` | `/about-us` |
| `Privacy Policy` | `/privacy-policy` |
| `Terms & Conditions` | `/terms-conditions` |
| `Disclaimer` | `/disclaimer` |

All links open in the same tab (`target="_self"`).
`scroll-behavior: smooth` on `html`.
Anchor offsets: `#results { scroll-margin-top:247px }`,
`#how-it-works { scroll-margin-top:155px }`, `#faqs { scroll-margin-top:20px }`.

---

## 8. Motion

Understated, Apple-like. Nothing bounces, nothing slides sideways, nothing loops.

- **Scroll reveal:** each section's content fades and rises into place once, when it
  enters the viewport. Implement with a single `IntersectionObserver`
  (`threshold: 0.15`, `rootMargin: "0px 0px -10% 0px"`, unobserve after firing):
  `opacity: 0 → 1`, `transform: translateY(20px) → none`,
  `transition: opacity .5s ease-out, transform .5s cubic-bezier(.44,0,.56,1)`.
  Stagger siblings within a group by ~60ms.
- **Buttons:** `transition: all .2s ease`; hover `opacity:.85` only.
- **Nav links:** colour `#999 → #000` over `.2s ease`.
- **FAQ:** height `.3s ease-in-out`; `+`/`−` icon swap `.2s`.
- **Reduced motion:** wrap all of the above in
  `@media (prefers-reduced-motion: reduce)` overrides that set
  `transition: none; animation: none; opacity: 1; transform: none;`.

No parallax. No counter-up animation on the stat numbers. No marquee. No cursor
effects. No hero video.

---

## 9. Accessibility & semantics

- One `<h1>` (the hero headline). Section headings `<h2>`; stat numbers and pillar
  labels `<h3>`. Paragraphs `<p>`. Bullet answers `<ul><li>`.
- Header in `<header>`, sections in `<section>`, footer in `<footer>`, nav links in
  `<nav>`.
- FAQ rows must be real `<button aria-expanded="true|false"
  aria-controls="...">` toggling a `<div role="region">`; keyboard-operable with
  Enter and Space, visible `:focus-visible` outline.
- Images need `alt` text describing the screenshot (e.g.
  `alt="Facebook Ads Manager report showing 33 leads at $17.10 cost per lead"`).
  Decorative icons get `aria-hidden="true"`.
- Contrast note: `#999` on white is ~2.8:1 and fails WCAG AA for small text. This is
  what the original does — reproduce it as-is for fidelity, but if the requester asks
  for an accessible variant, darken the muted grey to `#767676`.

---

## 10. Responsive behaviour

Exact breakpoints from the original:

- **Desktop:** `min-width: 1200px` — all values above.
- **Tablet:** `810px – 1199.98px`
- **Mobile:** `max-width: 809.98px`

Measured responsive type steps (these three tokens are the ones the original actually
re-declares — scale the rest proportionally between them):

| Token | Desktop | Tablet | Mobile |
|---|---|---|---|
| `--wordmark` | 44px (2px stroke) | **37px** (1.7px) | **23px** (1px) |
| `--display-hero` | 44px | **37px** | **30px** |
| `--display-section` | 44px | **37px** | **30px** |
| `--stat-number` | 68px | **54px** | **30px** |
| `--stat-label` | 22px | **19px** | **15px** |
| `--quote` / `--h2-lead` / `--h2-flush` | 34px | **28px** | **23px** |
| `--h2-guarantee` | 38px | **32px** | **25px** |
| `--h2-case` | 32px | **28px** | **23px** |
| `--step-body` | 22px | 19px | **17px** |
| `--body-case` / `--body-guarantee` | 17/18px | unchanged | **16px** |
| Buttons, nav links, FAQ text | unchanged at every size | | |

Keep the same line-heights and the same letter-spacing in `em` at every breakpoint.

Layout at ≤ 809px:

- Gutter drops from 20px to **20px** (unchanged); all max-widths become `100%`.
- Section vertical padding scales to roughly **35%** of desktop (e.g. `376px 0`
  becomes `130px 0`; `192px 0` becomes `68px 0`). Keep it airy, just not absurd.
- Nav links collapse into a hamburger menu; the primary button stays visible in the
  header. Menu panel: white, full-width, links at 18px / 600 / `#000`.
- **Header crowding — this one bites.** The global mobile rule
  `.btn{width:100%; max-width:320px}` will also hit the header button and shove it into
  the wordmark. Scope it out explicitly:
  `.header-cta .btn{width:auto; max-width:none; min-width:0; height:38px; padding:0 15px; font-size:13px}`
  and put `gap:12px` on the header row so the logo and button can never touch.
  At ≤ 360px go one step further: logo 20px, button `padding:0 11px; font-size:12px;
  height:34px`, toggle 28px, row `gap:8px`. Verified collision-free at 320, 390, 768,
  1024, 1280 and 1512px.
- Hero buttons stack vertically and **stay label-width** (centred, `gap: 10px`) — they
  do **not** stretch to the screen width. Only the horizontal padding shrinks, to 20px.
- Every stat row (3-up) stacks to a single column, `gap: 40px`, text centred.
- Pillars (4-up) become a 2 × 2 grid at ≤ 809px, `gap: 40px`.
- All three step rows become single column, **image below the text in every row**
  (drop the alternating pattern), image `width:100%; height:auto`.
- Case study / guarantee paragraph clamps become `100%`.
- FAQ column becomes `100%`; row height auto with `padding: 12px 16px`.
- Footer becomes a single column, left-aligned, `gap: 32px`, in this order: logo +
  tagline, site links, legal links, contact line.

At 810–1199px keep two-column step rows but reduce the images to ~42% width and the
grid to `100%` max-width with 40px gutters.

---

## 11. Acceptance checklist — verify every line before you call it done

Typography
- [ ] Inter Variable loaded **with the `opsz` axis**, not static Inter.
- [ ] Font source is the official Inter build (`InterVariable`), **not** Google Fonts.
- [ ] `font-feature-settings: "cv03" 1,"cv04" 1,"cv09" 1,"cv11" 1` on `body`.
- [ ] Every centred block has `width:100%` as well as `max-width` (see §4 gotcha).
- [ ] Ran the `agy 3469` on/off comparison from §2.2 and the two renders **differ**.
- [ ] Lowercase `a` renders **single-storey**. If it has a curved top hook, cv11 didn't apply — fix before continuing.
- [ ] `font-variation-settings` set per §2.3; headings resolve to `wght 700`.
- [ ] All letter-spacing negative except `THE GUARANTEE` (+0.12em) and the 13px fine print (0).
- [ ] `-webkit-font-smoothing: antialiased`.

Colour
- [ ] Exactly these colours appear: `#fff`, `#000`, `#999`, `#777`, `#666`, `#eee`, and the three `rgba(0,0,0,·)` inks. Nothing else.
- [ ] Hero line 2 (`Less Chasing.`) is `#999`, not black.
- [ ] Pillar labels are `#000`; every other 22px label is `#999`.

Layout
- [ ] Page gutter 20px; per-block max-widths match the §4 table.
- [ ] Header is `sticky`, 160px tall, flat white, `z-index:10`, unchanged on scroll.
- [ ] Buttons are **label-width** (`width:auto; min-width:0`), `height:46px`,
      `border-radius:100px` — true pills, not 8px corners — label never wraps.
- [ ] No button is full-width or 320px-wide at **any** breakpoint, including 320px.
      `Book A Strategy Call` ≈ 196–204px, `See How It Works` ≈ 171–179px.
- [ ] Wordmark is `wght 900` **plus a 2px `-webkit-text-stroke`** — the axis alone caps
      at 900, so a stroke is the only way past it. Do not write `wght 2000`; it clamps.
- [ ] `paint-order: stroke fill` is set, and the G / A / S counters are still open.
- [ ] Header wordmark measures **234 ± 3px wide**, cap height 35px, at 44px.
- [ ] Wordmark tracking is **+0.03em** (positive), unlike everything else on the page.
- [ ] Stroke is scaled per breakpoint — small logos must not turn into blobs.
- [ ] At 320px the logo and header button do not touch and nothing overflows sideways.
- [ ] `Terms & Conditions` and `mehak@genavise.com · Washington D.C.` each sit on **one line**.
- [ ] Six primary buttons total; exactly one secondary button.
- [ ] Step rows alternate left/right on desktop (text-left, text-right, text-left).
- [ ] Nine FAQ rows, `#eee`, 10px radius, 7px gaps, all closed on load.
- [ ] No dividers, borders, shadows, gradients, or cards anywhere on the page.
- [ ] Total desktop page height lands **8600–8800px** at 1512px wide (the reference
      build is 8693px). Much shorter means you compressed the whitespace — restore it.

Content
- [ ] All copy verbatim, curly apostrophes intact (`don’t`, `there’s`, `I’ve`, `We’ll`).
- [ ] Em dashes `—` and en dashes `–` used exactly as written (`$25–50`, `65–70%`).
- [ ] Middle dot `·` in `CASE STUDY · MARYLAND` and `mehak@genavise.com · Washington D.C.`
- [ ] Testimonial wrapped in curly quotes `“ ”`.
- [ ] Numbers exact: `33`, `$17.10`, `65%+`, `$564.30`, `14 Ads`, `40 Days`, `$15.06`, `30`, `90`.

Behaviour
- [ ] Scroll-reveal fires once per section; no re-animation on scroll up.
- [ ] `prefers-reduced-motion` disables all motion.
- [ ] Accordion is keyboard-operable with correct `aria-expanded`.
- [ ] Anchor jumps land correctly under the sticky header (`scroll-margin-top` set).
- [ ] Renders correctly at 1512px, 1280px, 1024px, 768px, and 390px wide.

---

## 12. Asset inventory

| Asset | Displayed | Source | Where |
|---|---|---|---|
| GENAVISE wordmark | 220 × 48 | 512 × 77 PNG | header |
| GENAVISE wordmark | 110 × 44 | 512 × 77 PNG | footer |
| Ads Manager results screenshot (33 / $17.10 / $564.30 circled red) | 894 × 202, radius 20 | 1568 × 314 | §6.3 |
| Ads Manager campaign table | 480 × 284, radius 10 | 1877 × 927 | Step 1 |
| CRM automation workflow canvas | 446 × 309, radius 20 | 1622 × 811 | Step 2 |
| Booking calendar with time slots | 476 × 309, radius 20 | 1187 × 786 | Step 3 |
| 4 pillar icons | 48 × 48 each | inline SVG, `fill:#000` | §6.7 |

If assets are unavailable, use `#EEEEEE` blocks at the **exact** dimensions and radii
above, with the described `alt` text. Never substitute stock photography — photos
would break the design.


---

## 13. Site structure — all six pages

The site is six pages sharing one shell and one stylesheet:

```
index.html              home / landing page          (§1–§12 above)
about-us.html           About Us
booking.html            Book A Strategy Call
privacy-policy.html     Privacy Policy
terms-conditions.html   Terms & Conditions
disclaimer.html         Disclaimer
assets/
  site.css              ONE stylesheet, shared by all six pages
  logo.png              the wordmark, used by every page
  proof-ads-manager.png       894 × 202
  step-1-facebook-ads.png     480 × 284
  step-2-crm-automation.png   446 × 309
  step-3-calendar.png         476 × 309
```

**Use one shared `assets/site.css`, not six inline `<style>` blocks.** Six copies of
the same CSS means six places to change a colour. (§ earlier told you to inline
everything — that applied when the deliverable was a single page. It no longer does.)

There is **no standalone FAQ page** — `/faqs` 404s on the live site. The `FAQs` nav
link is an in-page anchor to the accordion on the home page. From a subpage it must
therefore point at `index.html#faqs`, not `#faqs`.

### 13.1 The shared shell

Every page uses the identical header and footer from §6.1 and §6.14, with **one
difference**: the header's pill button.

| Page | Header pill label | Links to |
|---|---|---|
| `index.html` | `Book A Strategy Call` | `booking.html` |
| all five subpages | `Home` | `index.html` |

Same geometry either way — black pill, `border-radius:100px`, height 46, label-width.
Nav links, logo, sticky behaviour, footer: identical everywhere.

### 13.2 Images

All image slots use the pattern in §12: an `<img>` with `width`/`height` attributes
inside a box whose size is fixed in CSS, so space is reserved before the file loads
and nothing shifts. A small script adds `.no-img` / `.empty` on the `error` event, so a
missing file degrades to a grey block or the text wordmark instead of a broken-image
icon. Screenshots below the fold get `loading="lazy"`.

---

## 14. The subpage prose column

Five of the six pages are a single centred prose column. One set of rules covers all
of them.

```css
.prose{max-width:948px; width:100%; margin:0 auto;
       padding:60px 0 140px; display:flex; flex-direction:column; gap:22px}
```

| Element | Size | Line-height | Weight | Tracking | Colour | Max width |
|---|---|---|---|---|---|---|
| Page title (`h1`) | 32px | 1.4em | 800 | −0.04em | `#000` | 948px |
| Section head (`h2`) | 32px | 1.4em | 800 | −0.04em | `#000` | 948px, `margin-top:24px` |
| Numbered head (`h3`) | 21px | 1.3em | 800 | −0.02em | `#000` | 640px, `margin-top:22px` |
| Body (`p`) | 17px | 1.6em | 450 | −0.02em | `#666` | **640px** |
| Bullet line | 17px | 1.6em | 450 | −0.02em | `#666` | 640px |
| Contact line | 17px | 1.6em | 450 | −0.02em | **`#000`** | 640px |

Body copy clamps to **640px** inside the 948px column — headings run wider than
paragraphs. That contrast is deliberate; do not equalise them.

**Bullets are not `<ul>`.** The live site renders each one as its own paragraph opening
with a literal `•` character. Reproduce that, with a hanging indent so wrapped lines
align under the text rather than under the bullet:

```css
.prose .bullet{padding-left:20px; text-indent:-20px}
```

Inline links in prose: `color:#000; text-decoration:underline; text-underline-offset:3px`,
hover `opacity:.6`. Email addresses become `mailto:` links and phone numbers `tel:`
links — on the live site they are plain text, but making them tappable is the one
deviation worth keeping.

Responsive: heads 28px / body unchanged at tablet; heads 24px, `h3` 19px, body 16px and
all max-widths `100%` at ≤809px, with column padding dropping to `36px 0 72px`.

### 14.1 about-us.html

Prose column. Eyebrow, then four heading-and-body groups, then a CTA button.

- Eyebrow, body style, `#666`: `ABOUT US`
- `h1`: `We build systems. Not just campaigns.`
- Two paragraphs, then `h2` `What we actually do` + two paragraphs,
  `h2` `Who we work with` + one paragraph, `h2` `Our philosophy` + one paragraph.
- Closing primary button `Book A Strategy Call` → `booking.html`.
- Full copy is in the built `about-us.html`; reproduce it verbatim, including
  `don't`, `isn't`, `you're` with curly apostrophes.

### 14.2 booking.html

Not a prose page. Heading, then a scheduler embed.

- `h1`, **34px / 1.3em / 800 / −0.03em / centred**, two lines with a `<br>`:
  `Get More Qualified Buyer & Seller Appointments` /
  `Without Chasing New Contacts`
  (`&` is a real ampersand, not "and".)
- Below it, the scheduler. On the live site this is an iframe **1680 × 798px**,
  roughly full width with ~113px side margins, injected by Framer's embed component
  via `srcdoc` — so the provider is not readable from the page and **you cannot
  reproduce the booking widget itself.** Build the slot and leave it to the owner:

```css
.embed{max-width:1680px; width:100%; margin:44px auto 120px; min-height:798px}
.embed iframe{width:100%; min-height:798px; border:0; display:block}
```

  Ship it with a labelled grey placeholder inside, so the page is presentable before
  the embed is pasted in. Mobile: `min-height:620px`, margins `28px auto 64px`.
- No other content. Footer follows.

### 14.3 privacy-policy.html · terms-conditions.html · disclaimer.html

All three are prose pages with the same anatomy:

1. `h1` page title
2. `Effective Date: 03/24/2026` as a body paragraph — Privacy and Terms only;
   Disclaimer has no effective date
3. An intro paragraph defining the company and its shorthand terms
4. Numbered `h3` sections (`1. Company Information`, `2. Services`, …), each followed
   by body paragraphs and `•` bullet lines
5. A closing contact block: company name in `#000`, then email and phone

Section counts: Privacy Policy **12**, Terms & Conditions **15**, Disclaimer
**unnumbered** (continuous prose plus one bullet list).

Copy accuracy matters more here than anywhere else on the site — this is legal text.
Reproduce it character for character from the built files, including:

- curly quotes around defined terms: `&ldquo;Genavise,&rdquo;` `&ldquo;we,&rdquo;`
- `GENAVISE MARKETING L.L.C.` — always in caps, always with the periods in `L.L.C.`
- `&amp;` in `Terms &amp; Conditions`, `SMS Communications &amp; Consent`,
  `Fees &amp; Transactions`, `Your Rights &amp; Choices`,
  `Cookies &amp; Tracking Technologies`
- `STOP` and `HELP` capitalised
- `thirty (30) days` written both ways, as on the live page
- Terms §12 links the phrase `Privacy Policy` to `privacy-policy.html`

Do not summarise, reorder, modernise or "tighten" any of this text.

---

## 15. Acceptance checklist — whole site

- [ ] Six HTML files plus `assets/site.css` and `assets/logo.png`.
- [ ] Exactly **one** `<header>` and **one** `<footer>` per page. (Splicing a shared
      shell into a body that already contains one is the easy way to ship two of each —
      it happened during this build. Count them.)
- [ ] Header pill says `Book A Strategy Call` on the home page and `Home` on all five
      subpages.
- [ ] Every internal link resolves as a plain file — no leading `/`, so the site works
      opened from disk and on any host.
- [ ] `#how-it-works`, `#results` and `#faqs` are prefixed with `index.html` on
      subpages.
- [ ] No horizontal overflow on any page at 320, 390, 768, 1024, 1280 and 1512px.
- [ ] Logo image resolves on all six pages; text fallback appears if the file is removed.
- [ ] Legal copy matches character for character, curly quotes and `L.L.C.` included.
- [ ] Booking page shows a labelled placeholder, sized 1680 × 798 on desktop, until a
      real embed is pasted in.
