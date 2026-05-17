# liftwithstacey.com — Comprehensive SEO Optimization Guideline
*Last updated: May 2026*

---

## Site Profile

- **URL:** https://liftwithstacey.com/
- **Type:** Single-page static HTML, deployed via Netlify from `/netlify_upload/`
- **Audiences:** USA online coaching clients + Prague in-person clients
- **Languages:** English + Spanish (JS-toggled via `data-en`/`data-es` attributes)
- **Hosting:** Netlify (drag-and-drop deploy)

### Already Implemented ✅
- Title, description, keywords meta tags
- Canonical URL + hreflang (en/es)
- Open Graph + Twitter Card tags
- JSON-LD schema: `Person` + 2 `Service` nodes (online USA + Prague in-person)
- sitemap.xml + robots.txt
- favicon.svg
- Lazy loading on images

---

## Phase 1 — Technical Foundation
*Code changes inside the HTML/site files. Highest leverage — fix how Google sees the page.*

---

### 1.1 Create `_headers` File for Caching & Security
**File to create:** `/netlify_upload/_headers`
**Impact:** 🔴 High | **Est. time:** 30 min

Without this file, Netlify serves images/videos with no cache headers and zero security headers. Google's HTTPS/security signals reward sites that pass browser security checks.

```
/*
  X-Frame-Options: SAMEORIGIN
  X-Content-Type-Options: nosniff
  Referrer-Policy: strict-origin-when-cross-origin
  Permissions-Policy: camera=(), microphone=(), geolocation=()
  Strict-Transport-Security: max-age=31536000; includeSubDomains; preload

/*.html
  Cache-Control: public, max-age=0, must-revalidate

/images/*
  Cache-Control: public, max-age=31536000, immutable

/videos/*
  Cache-Control: public, max-age=31536000, immutable

/favicon.svg
  Cache-Control: public, max-age=31536000, immutable
```

After deploying, verify at: https://securityheaders.com

---

### 1.2 Fix JSON-LD Schema — Critical Gaps
**File:** `/netlify_upload/index.html` (the `<script type="application/ld+json">` block)
**Impact:** 🔴 High | **Est. time:** 1 hour

Three gaps in the current schema:
- No `LocalBusiness` node (needed for Prague local + AI citation)
- `Person` node has no `image` property (blocks Google Knowledge Panel)
- No `FAQPage` node (pages with FAQ schema are 3.2x more likely in Google AI Overviews)

**Add to the `@graph` array:**

```json
{
  "@type": ["LocalBusiness", "HealthAndBeautyBusiness"],
  "@id": "https://liftwithstacey.com/#business",
  "name": "Stacey Valny Personal Training",
  "description": "ISSA-certified personal trainer offering in-person sessions in Prague and online coaching for USA clients. Bilingual English and Spanish.",
  "url": "https://liftwithstacey.com/",
  "address": {
    "@type": "PostalAddress",
    "addressLocality": "Prague",
    "addressCountry": "CZ"
  },
  "areaServed": [
    { "@type": "City", "name": "Prague", "addressCountry": "CZ" },
    { "@type": "Country", "name": "United States" }
  ],
  "priceRange": "$$",
  "availableLanguage": [
    { "@type": "Language", "name": "English" },
    { "@type": "Language", "name": "Spanish" }
  ],
  "sameAs": [
    "https://instagram.com/liftwithstacey",
    "https://tiktok.com/@liftwithstacey"
  ],
  "founder": { "@id": "https://liftwithstacey.com/#trainer" }
}
```

**Add `image` to the existing `Person` node:**
```json
"image": {
  "@type": "ImageObject",
  "url": "https://liftwithstacey.com/images/web/stacey-valny-personal-trainer-prague.jpg",
  "width": 1200,
  "height": 1500
}
```

**Add a `FAQPage` node** matching your FAQ section:
```json
{
  "@type": "FAQPage",
  "@id": "https://liftwithstacey.com/#faq",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Do you offer online personal training?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes — online virtual personal training is available to clients throughout the United States. Sessions are conducted via video call in English or Spanish."
      }
    },
    {
      "@type": "Question",
      "name": "Do you offer in-person training in Prague?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes, in-person personal training sessions are available in Prague, Czech Republic."
      }
    },
    {
      "@type": "Question",
      "name": "Do you coach in Spanish?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes, coaching is available in both English and Spanish."
      }
    },
    {
      "@type": "Question",
      "name": "Do you offer nutrition coaching?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes — nutrition coaching is available as a standalone service or combined with personal training."
      }
    },
    {
      "@type": "Question",
      "name": "Do I need gym equipment for online training?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "No — programs are tailored to your available equipment, whether that's a full gym, dumbbells at home, or bodyweight only."
      }
    },
    {
      "@type": "Question",
      "name": "What time zones do you work with for online clients?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "All US time zones are accommodated. Sessions are scheduled around your availability."
      }
    }
  ]
}
```

Validate after changes at:
- https://validator.schema.org
- https://search.google.com/test/rich-results

---

### 1.3 Hero Video — Add Poster Image + Preload LCP
**File:** `/netlify_upload/index.html`
**Impact:** 🔴 High | **Est. time:** 45 min

The hero uses two `<video>` elements (~11MB each). Without a `poster` attribute, the browser shows a black frame while the video loads — this is the LCP element and it is undefined. Google cannot index a black frame.

**Step 1 — Create a WebP poster image:**
Export a 1920×1080 crop of your trainer portrait as `hero-poster.webp` and place in `/netlify_upload/images/web/`.

**Step 2 — Update the video element:**
```html
<video
  class="hero-video hero-video-a active"
  muted playsinline autoplay loop
  preload="none"
  poster="images/web/hero-poster.webp"
  aria-hidden="true">
  <source src="videos/web/hero5.mp4" type="video/mp4">
</video>
```

**Step 3 — Add `<link rel="preload">` in `<head>`** (right after the canonical tag):
```html
<link rel="preload" as="image" href="images/web/hero-poster.webp" fetchpriority="high">
```

---

### 1.4 Compress Hero Videos
**Impact:** 🔴 High | **Est. time:** 1 hour

Current hero videos are ~11MB each — the single biggest performance liability. Use HandBrake (free) to re-export:
- Codec: H.264
- Resolution: 1280×720 (720p is enough for a background loop)
- Target size: under 3MB each
- Audio: remove (muted anyway)

Replace the existing files in `/netlify_upload/videos/web/`.

---

### 1.5 Convert All Images to WebP
**Impact:** 🔴 High | **Est. time:** 2 hours

WebP is 25–35% smaller than JPEG at equivalent quality. Total image payload savings: ~3–5MB.

**Install cwebp:**
```bash
brew install webp
```

**Convert all images:**
```bash
cd /Users/staceyvalny/PT_website/netlify_upload/images/web
for f in *.jpg; do cwebp -q 82 "$f" -o "${f%.jpg}.webp"; done
```

**Replace `<img>` tags with `<picture>` elements for fallback:**
```html
<picture>
  <source srcset="images/web/stacey-valny-personal-trainer-prague.webp" type="image/webp">
  <img
    src="images/web/stacey-valny-personal-trainer-prague.jpg"
    alt="Stacey Valny, ISSA-certified personal trainer, coaching in Prague"
    width="800" height="1000"
    loading="lazy">
</picture>
```

Do NOT use `loading="lazy"` on the above-the-fold hero poster image.

---

### 1.6 Rename Image Files to SEO-Friendly Names
**Impact:** 🟡 Medium | **Est. time:** 30 min

Google Images uses filenames as a relevance signal. Do this BEFORE converting to WebP.

| Current filename | Rename to |
|---|---|
| `IMG_1758.jpg` | `stacey-valny-personal-trainer-prague.jpg` |
| `IMG_2558.jpg` | `stacey-valny-issa-certified-trainer.jpg` |
| `IMG_2594.jpg` | `personal-trainer-lifting-form-technique.jpg` |
| `IMG_2598.jpg` | `online-strength-training-program-results.jpg` |
| `IMG_2623.jpg` | `personal-trainer-strength-training-session.jpg` |
| `IMG_4315.jpg` | `real-life-strength-training-coach.jpg` |
| `IMG_5632.jpg` | `personal-trainer-bilingual-coaching.jpg` |

Update all `src` attributes in the HTML after renaming.

---

### 1.7 Add `width` and `height` to All `<img>` Tags
**Impact:** 🔴 High | **Est. time:** 30 min

Missing `width`/`height` attributes cause Cumulative Layout Shift (CLS). CLS above 0.1 is a Google ranking demotion. For every `<img>` tag, add the intrinsic pixel dimensions of the source image.

---

### 1.8 Improve Alt Text on All Images
**Impact:** 🟡 Medium | **Est. time:** 30 min

Alt text is a primary source for AI Overview citations — descriptive alt text increases citation likelihood 3–6x. Target: 60–90 characters, describe subject + context + relevant keyword.

| Image | Current alt | Improved alt |
|---|---|---|
| Card 01 | "Strength training fundamentals" | "Stacey Valny coaching a strength training fundamentals session in Prague" |
| Card 02 | "Perfect form and technique" | "Personal trainer demonstrating perfect barbell lifting form and technique" |
| Card 03 | "Train for real life" | "Stacey Valny helping a client train for real-life strength and fitness" |
| Card 04 | "Consistent training for results" | "Consistent online strength training program delivering measurable results" |
| Portrait | "Personal trainer portrait" | "Stacey Valny, ISSA-certified personal trainer offering online coaching in English and Spanish" |

Use `alt=""` only for purely decorative elements (borders, dividers).

---

### 1.9 Fix Language Toggle to Update `<html lang>`
**Impact:** 🟡 Medium | **Est. time:** 15 min

Currently `<html lang="en">` is hardcoded even when the user switches to Spanish. Google reads the `lang` attribute to determine page language for Spanish-language queries.

In the JS language toggle, add:
```javascript
// When switching to Spanish:
document.documentElement.lang = 'es';
// When switching to English:
document.documentElement.lang = 'en';
```

---

### 1.10 Expand FAQ Section Content
**Impact:** 🔴 High | **Est. time:** 1–2 hours

FAQ content is the highest-ROI content investment for a single-page site. Each answer should be a complete standalone paragraph (50–100 words), not a sentence fragment.

**Add these FAQ items** (and matching schema entries per step 1.2):
- "How much does online personal training cost?"
- "Do I need gym equipment for online training?"
- "What is the difference between your online and in-person packages?"
- "Can you train complete beginners who have never lifted before?"
- "Do you offer a free trial session?"
- "What time zones do you work with for online clients?"
- "Are you certified to coach nutrition?"
- "How does the founding member rate work?"

---

### 1.11 Update Title + Meta Description to Include Prague
**Impact:** 🟡 Medium | **Est. time:** 10 min

**Current title:** `Stacey Valny | Online Personal Trainer | English & Spanish Coaching`
This completely drops "Prague" — invisible to in-person local searches.

**Recommended title:** `Stacey Valny | Online & Prague Personal Trainer | English & Spanish` (64 chars)

**Current description:** *(already strong — add Prague explicitly)*
**Recommended:** `ISSA-certified personal trainer | Online coaching for USA clients + in-person in Prague. Bilingual English & Spanish. Evidence-based programs. Free first session — book today.`

---

### 1.12 Add AggregateRating Schema Once Reviews Exist
**Impact:** 🔴 High (future) | **Est. time:** 30 min when ready

Star ratings in search results boost CTR by 15–30%. Once you have 3+ reviews, add to the `LocalBusiness` schema node:
```json
"aggregateRating": {
  "@type": "AggregateRating",
  "ratingValue": "5.0",
  "reviewCount": "5",
  "bestRating": "5"
}
```

---

### 1.13 Add Apple Touch Icon PNG
**Impact:** 🟢 Low | **Est. time:** 30 min

Safari on iOS ignores SVG favicons for home screen bookmarks, falling back to a generic icon.

Create a 180×180 PNG version of the favicon. Add to `<head>`:
```html
<link rel="apple-touch-icon" sizes="180x180" href="/apple-touch-icon.png">
```

---

## Phase 2 — On-Page Content Optimization

---

### 2.1 Add Testimonials Section
**Impact:** 🔴 High

Currently zero social proof on the page. Even 3 short client quotes with first names and goals significantly improve trust signals and conversion. Use `blockquote` elements + `Review` schema linked to the `LocalBusiness` node.

Add a "Leave a Google Review" CTA button linking to your Google Business Profile review URL.

---

### 2.2 Add Czech Keyword for Local Discovery
**Impact:** 🟡 Medium

Add the phrase **"osobní trenér Praha"** (Czech for "personal trainer Prague") somewhere in the visible body copy. This helps Seznam.cz (Czech search engine, ~10–15% market share in Prague) associate the page with Czech local queries. Even one occurrence in a footer or contact section is enough.

---

### 2.3 Add Testimonials Section (with schema)
See 2.1 above — each client quote should also be added as a `Review` object in the JSON-LD `LocalBusiness` node for rich result eligibility.

---

## Phase 3 — Off-Page Authority Building
*External actions — no code changes required.*

---

### 3.1 Google Business Profile — HIGHEST PRIORITY OFF-PAGE ACTION
**Impact:** 🔴 High | **Est. time:** 2 hours setup + ongoing

Fully completed GBP profiles receive 70% more visits and appear 18x more in search results. This is free and is the single biggest local SEO lever available.

**Setup steps:**
1. Go to business.google.com
2. Business name: "Stacey Valny Personal Training"
3. Primary category: "Personal Trainer"
4. Secondary categories: "Health Consultant" or "Fitness Center"
5. Service area: United States (for online) + Prague (for in-person)
6. Website: https://liftwithstacey.com/
7. Description: 750 chars max — use "personal trainer Prague" AND "online personal trainer USA" in first sentence
8. **Photos:** Upload 10+ immediately — portrait, ISSA certificate, training action shots. Name files descriptively before uploading. Add new photos weekly.
9. **Services:** List each with description + price range
10. **Posts:** Publish 2+ GBP posts per week — tips, client results, booking reminders
11. **Q&A:** Seed 5–10 Q&A yourself (you can ask and answer your own)
12. Get the review link from the dashboard and start sending to clients

---

### 3.2 Google Search Console — Set Up Immediately
**Impact:** 🔴 High | **Est. time:** 30 min setup

The only tool that shows you real data from Google: queries, impressions, indexing errors, schema issues, Core Web Vitals field data.

**Setup steps:**
1. Go to search.google.com/search-console
2. Add property for `https://liftwithstacey.com/`
3. Verify via HTML file (upload to netlify_upload and deploy) or DNS TXT record
4. Submit sitemap: `https://liftwithstacey.com/sitemap.xml`
5. URL Inspection → `https://liftwithstacey.com/` → Request Indexing

**Weekly tasks (30 min/week):**
- Performance → check new query impressions + clicks
- Coverage → look for Excluded/Error URLs
- Enhancements → check for schema errors
- Core Web Vitals → monitor LCP, CLS, INP field scores

---

### 3.3 Claim ISSA Trainer Directory Profile
**Impact:** 🔴 High | **Est. time:** 20 min

Your certification body has a public trainer directory. This is a high-authority, directly relevant backlink that 95% of ISSA trainers never claim.

Log into your ISSA account → complete your public profile → add `https://liftwithstacey.com/` to your website field.

---

### 3.4 Firmy.cz Listing (Czech Market)
**Impact:** 🔴 High for Prague | **Est. time:** 30 min

Firmy.cz is Seznam's business directory — the Czech equivalent of Google Business Profile. Essential for in-person Prague client discovery.

Register at: https://www.firmy.cz (use same NAP format as GBP)

---

### 3.5 Bing Places + Apple Maps Connect
**Impact:** 🟡 Medium | **Est. time:** 30 min each

- **Bing Places:** https://www.bingplaces.com — captures ~6% of US searches
- **Apple Maps Connect:** https://mapsconnect.apple.com — increasingly important as Apple Intelligence routes queries to Maps

---

### 3.6 Collect Google Reviews (Ongoing)
**Impact:** 🔴 High | Ongoing

Review velocity and response rate are confirmed local ranking factors. High ratings are the #1 conversion signal.

- After every session/milestone, send the direct review link
- Respond to every review within 48 hours
- Target: 1 new review/week initially, then 2+/month
- Also request reviews on Yelp + Facebook for citation diversity

---

### 3.7 Instagram SEO Optimization
**Impact:** 🔴 High | Ongoing

Since July 2025, all public content from professional Instagram accounts indexes in Google search results. This is a major leverage point.

- Switch to Professional Account if not already
- Write the first caption sentence as a meta description: "ISSA-certified personal trainer showing how to do a Romanian deadlift for beginners"
- Use target keywords in on-screen text overlays in Reels
- Add location tags to all Prague posts
- Pin your most keyword-rich posts to your profile top
- Bio: "Personal Trainer | Prague + Online | 🇺🇸🇨🇿 | EN/ES"

---

### 3.8 TikTok SEO
**Impact:** 🟡 Medium-High | Ongoing

74% of Gen Z uses TikTok for search. Fitness content has the highest organic reach of any niche.

- Use keyword in first line of every caption: "Personal trainer tip: how to deadlift safely #personaltrainer #strengthtraining"
- Speak keywords in the voiceover — TikTok transcribes audio and indexes it
- Mix broad (#personaltrainer) and niche (#bilingualtrainer #praguefitness) hashtags
- Link to liftwithstacey.com in your bio

---

### 3.9 Link Building
**Impact:** 🔴 High | Ongoing

Prioritized by effort-to-impact:

1. **ISSA directory** (see 3.3) — 20 min, high-authority backlink
2. **Prague expat communities** — Expats.cz, InterNations Prague, PragueExpats.com. Reach out to editors about listing or featuring an English-speaking trainer.
3. **Prague partner businesses** — yoga studios, physiotherapists, nutritionists, sports massage, crossfit boxes. Referral exchange → "partners" page listing = local backlink
4. **Guest posts** on fitness blogs (DR30+) — write one 800–1200 word article, include byline link
5. **Prague English-language press** — Prague Morning, Prague City Line. The angle: bilingual ISSA-certified trainer serving expats and remote USA clients. Genuinely newsworthy.
6. **Resource pages** — search `site:expats.cz "personal trainer"` and request to be added

---

### 3.10 NAP Citation Consistency
**Impact:** 🟡 Medium

NAP = Name, Address, Phone. Use exactly the same format everywhere:
- Name: **Stacey Valny Personal Training**
- Phone: **+420 602 135 870**
- Address: **Prague, Czech Republic**

Submit consistently to: GBP, Bing Places, Apple Maps, Yelp, Facebook, LinkedIn, Firmy.cz, Zlatestranky.cz, Thumbtack (USA), Bark.com (USA)

---

## Phase 4 — Ongoing Monitoring

| Frequency | Task |
|---|---|
| **Weekly** | GSC: check queries, clicks, impressions, errors |
| **Weekly** | GBP: publish 2 posts, check Insights (discovery vs. brand searches) |
| **Weekly** | Respond to any new Google Reviews |
| **Monthly** | PageSpeed Insights (pagespeed.web.dev) — target LCP <2.5s, CLS <0.1 |
| **Monthly** | Update sitemap `<lastmod>` date after any content changes |
| **Quarterly** | GSC: find "striking distance" queries (positions 4–15) and add content targeting them |
| **Quarterly** | Search your target keywords — note competitors ranking above you and what content they have |

---

## Priority Summary Table

| # | Action | Impact | Code? | Time |
|---|---|---|---|---|
| 1 | Create `_headers` file | 🔴 High | Yes | 30 min |
| 2 | Fix JSON-LD (LocalBusiness + FAQPage + Person image) | 🔴 High | Yes | 1 hr |
| 3 | Set up Google Business Profile | 🔴 High | No | 2 hrs |
| 4 | Hero video poster + preload | 🔴 High | Yes | 45 min |
| 5 | Compress hero videos (11MB → <3MB) | 🔴 High | Assets | 1 hr |
| 6 | Convert images to WebP | 🔴 High | Yes + assets | 2 hrs |
| 7 | Add width/height to all img tags | 🔴 High | Yes | 30 min |
| 8 | Expand FAQ section | 🔴 High | Yes | 1–2 hrs |
| 9 | Set up Google Search Console | 🔴 High | No | 30 min |
| 10 | Claim ISSA directory profile | 🔴 High | No | 20 min |
| 11 | Firmy.cz listing | 🔴 High (CZ) | No | 30 min |
| 12 | Collect Google Reviews (ongoing) | 🔴 High | No | Ongoing |
| 13 | Instagram SEO (ongoing) | 🔴 High | No | Ongoing |
| 14 | Rename image files | 🟡 Medium | Yes | 30 min |
| 15 | Improve alt text | 🟡 Medium | Yes | 30 min |
| 16 | Update title + meta description (add Prague) | 🟡 Medium | Yes | 10 min |
| 17 | Fix html lang toggle in JS | 🟡 Medium | Yes | 15 min |
| 18 | Add testimonials section | 🔴 High | Yes | 1–2 hrs |
| 19 | Bing Places + Apple Maps | 🟡 Medium | No | 30 min each |
| 20 | Prague expat backlinks | 🔴 High (CZ) | No | 2 hrs |
| 21 | Guest posts on fitness blogs | 🟡 Medium | No | Ongoing |
| 22 | GA4 setup + conversion events | 🟡 Medium | Yes | 1 hr |
| 23 | Add apple-touch-icon PNG | 🟢 Low | Yes | 30 min |
| 24 | Add AggregateRating schema (when reviews exist) | 🔴 High (future) | Yes | 30 min |

---

## Key Audit Findings at Time of Writing

| Finding | Status |
|---|---|
| Hero videos are ~11MB each | 🚨 Biggest performance issue |
| No Google Business Profile | 🚨 Highest-impact free action not yet done |
| No `_headers` file | 🚨 Zero caching or security headers |
| `LocalBusiness` + `FAQPage` schema missing from deployed version | ⚠️ Needs fix |
| All images are JPEG (no WebP) | ⚠️ 3–5MB savings available |
| `<html lang>` is hardcoded to "en" | ⚠️ Affects Spanish query ranking |
| No testimonials section | ⚠️ Zero social proof on page |
| Image files have generic camera-roll names | ⚠️ SEO opportunity missed |
