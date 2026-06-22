# Asset Quality

Beautiful layout with bad images is broken. Asset quality is not optional.

## Asset Priority Order

1. **Provided brand/product assets** from the repo, user, or existing site. For redesigns, portfolios, product pages, and case studies, these come first.
2. **Real product screenshots or photography** captured from the actual product, venue, work, or object being presented.
3. **Image generation tool** when no suitable real asset exists. Generate at the exact aspect ratio needed for the section.
4. **Verified licensed stock** only when the brief allows it and the license is known.
5. **Picsum or generic placeholders** only for prototypes. Treat them as labeled placeholder assets, never as real photography.

If an essential asset is missing for production work, do not leave unresolved asset comments in shipped code. Pause if the asset is blocking, or ship a visibly labeled prototype placeholder and report it in the final asset inventory.

## Image Quality Criteria

An acceptable image must satisfy ALL of:

### Composition
- Subject is clear and occupies the intended focal area
- No awkward crops through faces, text, or key product details
- Background does not distract from the subject
- Negative space where text overlays will go

### Crop & Aspect Ratio
- Match the container aspect ratio. Never stretch or squish.
- Hero images: 16:9 or 3:2 landscape. 4:3 only if square composition is deliberate.
- Product shots: 1:1 or 4:3. Consistent ratio across same-page product images.
- Portraits/testimonials: 1:1. Face centered, eyes in upper third.
- Screenshots: native resolution. Never upscale. Crop to relevant area.

### Lighting & Color
- Natural, even lighting. No blown highlights or crushed shadows.
- Color temperature consistent with page theme. Warm images on a cool page clash.
- For generated images: specify lighting conditions in the prompt ("soft natural light from window left, shallow depth of field").

### Subject Relevance
- Image content matches section purpose. A hero image of a laptop when the product is cookware is wrong.
- For abstract/decorative images: must complement, not distract from, the content.
- Generated images must look realistic for the context. "Cinematic render of a saucepan" beats "photorealistic 3D render of a saucepan" for a cookware brand.

### Licensing & Attribution
- Picsum: prototype placeholder only. No semantic relevance is guaranteed by seed text.
- Generated images: note the generation source in a code comment.
- Stock photos: verify license allows commercial use without attribution before using.
- Never hotlink images from sources you haven't verified.

## Generated Image Rules

When using an image generation tool:

- **Specify aspect ratio** in the prompt: "16:9 landscape", "1:1 square".
- **Specify lighting:** "natural daylight", "soft studio lighting", "golden hour".
- **Specify composition:** "subject centered with negative space on right", "bird's eye view".
- **Specify style:** "editorial photography", "minimalist product shot on white", "lifestyle photography".
- **Avoid synthetic tells:** "3D render", "cinematic", "octane render", "unreal engine" produce a specific CGI look. Use only when the aesthetic deliberately calls for it.
- **Mood match:** The image mood must match the page tone. A warm, family-oriented cookware brand does not use cold, sterile product shots.

## Screenshot Rules

Real product screenshots beat fake div-based previews. When using real screenshots:

- **Resolution:** Native resolution. Never upscale. Downscale only to fit layout constraints.
- **Cropping:** Crop to the relevant UI area. Do not include browser chrome, OS UI, or desktop backgrounds unless they are part of the demonstration.
- **Content realism:** Screenshots must show realistic data. No "John Doe", no "Acme Corp", no "1234 Fake Street", no lorem ipsum, no placeholder avatars.
- **State variety:** Show populated states, not empty states, unless demonstrating empty-state design.
- **Consistency:** All screenshots in a section share the same UI theme, font, and color scheme. Don't mix light and dark mode screenshots.

## Icon Rules

- **Use icon libraries, never hand-roll SVG icons.** Phosphor > HugeIcons > Radix > Tabler. Lucide on explicit request only.
- **One library per project.** Do not mix Phosphor with Lucide.
- **Standardize stroke width:** 1.5 or 2.0 globally.
- **Icon size:** Consistent within a context. Nav icons, feature icons, and inline icons may have different sizes but must be internally consistent.
- **Icon meaning:** Every icon must convey clear meaning. Decorative-only icons are noise.
- **Generated icons (monograms, logos):** Acceptable as simple geometric marks (one letter in a circle, two-letter ligature, abstract glyph) rendered as inline `<svg>`. Keep them simple. No complex illustrations.

## Image Treatment

- **Do not apply CSS filters** (saturate, hue-rotate, sepia) to photography unless part of a deliberate treatment (e.g., B&W for editorial mood).
- **Borders on images:** None by default. If framing is needed, use 1px hairline matching the section background, not a decorative frame.
- **Overlays for text readability:** When text sits on an image, add a scrim (semi-transparent gradient overlay) between the image and text. Scrim must cover the text area, not the whole image.
- **Lazy loading:** All below-the-fold images use `loading="lazy"`. Hero images use `priority`.

## Minimum Image Requirements Per Page Type

| Page type | Minimum images | Type |
|---|---|---|
| SaaS landing | 3-5 | Hero image, 2 product screenshots, 1-2 supporting |
| Consumer brand landing | 4-6 | Hero photo, 3-4 product/lifestyle, 1 lifestyle |
| Portfolio | 4-8 | Project images (real work), 1 personal/studio |
| Editorial/blog post | 1-2 | Hero image, 1 inline diagram (if applicable) |
| Dashboard | N/A | Charts are images. No decorative images. |
| Settings/forms | 0 | No images needed unless brand header |

A pure-text page with no images is not minimalism. It is incomplete work. Even a restrained editorial brief needs at least 1-2 real images.

## Privacy & Redaction

Before using real screenshots, redact secrets and private data:
- API keys, tokens, auth headers, credentials, recovery codes
- Customer names, emails, phone numbers, addresses, account IDs
- Internal URLs, environment names, repository names, incident IDs
- Production data that the user has not explicitly approved for display

Use realistic demo data after redaction. Never replace private data with obvious filler like "John Doe" or "Acme Corp."

## Asset Inventory

Before declaring a page done, list every visual asset and its source:

```
Hero:          provided asset, /public/brand/hero-pan.jpg
Feature 1:     generated, "product shot of cast iron pan, natural light, 1:1"
Feature 2:     (real screenshot from app, cropped to 1200x800)
Testimonials:  generated portraits, labeled mock
Logo wall:     simpleicons.org CDN (GitHub, Stripe, Vercel, Figma, Linear)
```

If any asset is a placeholder (Picsum, generated without real context, mock data), state it explicitly. Never pass placeholders as real assets.
