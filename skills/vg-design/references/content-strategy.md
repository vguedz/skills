# Content Strategy — Design from Content

Do not lay out a page until the content structure is clear. Beautiful UI with hollow content is still slop.

## Before Layout: Derive Page Structure

Answer these in order. Each answer shapes the sections you build.

### 1. Audience

Who is reading this page? Be specific:
- **Role:** "Engineering managers at 50-200 person startups" — not "developers"
- **Context:** What were they doing before landing here? What problem sent them searching?
- **Familiarity:** Do they know the category (competitive evaluation) or are they new (need education)?

### 2. User Job

What does the audience need to accomplish? "They need to evaluate whether this tool replaces their current CI pipeline" is concrete. "They want to learn about the product" is too vague.

### 3. Value Proposition

One sentence. If it takes more than one sentence, it is not clear enough:
> "[Product] helps [audience] [outcome] by [mechanism]."

Example: "Linear helps product teams build better software by making project tracking fast enough to actually use."

### 4. Top 3 Objections

Every audience has reasons to say no. Address them explicitly:
- **Price:** "This is too expensive for our team size."
- **Risk:** "What if it doesn't work with our existing tools?"
- **Complexity:** "We don't have time to learn another tool."
- **Trust:** "Is this company going to be around in 2 years?"

Map each objection to a section. If an objection isn't addressed on the page, the user carries it silently.

### 5. Proof Points

Claims without proof are noise. For each claim, provide at least one:
- **Social proof:** Named customer logos, case studies, testimonials (with real attribution: name + role + company)
- **Data proof:** Specific numbers with context ("47% faster deploys" needs: compared to what, measured how, over what period)
- **Demonstration:** Interactive demo, video walkthrough, real screenshots
- **Third-party validation:** Awards, certifications, compliance badges, analyst reports

### 6. CTA Hierarchy

One primary CTA. Maximum one secondary. Everything else is a link.

- **Primary:** The one action you want the audience to take ("Start free trial")
- **Secondary:** The alternative for not-yet-convinced visitors ("See demo", "View pricing")
- **Placement:** Primary CTA appears in hero, repeated at page bottom. Never more than 3 instances of the same CTA on one page.
- **Label:** Concrete verb + outcome. "Get started" is weak. "Deploy your first app" is concrete. "Try free" is weak. "Start building - no credit card" handles objection inline.

## Section Planning from Content

Once you have audience, value prop, objections, and proof, plan sections in this order:

1. **Hero:** Value prop + primary CTA. One sentence, one primary action. Optional secondary CTA is allowed only when it serves a distinct lower-commitment path.
2. **Social proof (optional, directly below hero):** Logo wall or key testimonial. Short.
3. **Problem agitation:** State the problem your audience actually has. Use their words.
4. **Solution with proof:** How you solve it. Show, don't tell. Screenshots > descriptions.
5. **How it works:** Only if non-obvious. 3 steps max. Verbs, not "Stage 1."
6. **Objection handling:** One section per major objection. Price, trust, integration.
7. **Testimonials / case studies:** Named people, real photos, specific outcomes.
8. **Final CTA:** Restate value prop. Make it impossible to miss.

## Copy Rules (Positive)

Good UI copy is not the absence of slop. It is the presence of specific, useful information.

### Concrete Nouns
"We built a task manager" → "You assign, prioritize, and ship tasks from one inbox."

### User-Relevant Outcomes
"Fast performance" → "Pages load in under 200ms, even with 10,000 tasks."

### Proof Before Claims
"Enterprise-grade security" → "SOC 2 Type II certified. SSO with SAML and OIDC. Audit logs for every action."

### Plain CTA Verbs
"Explore our solutions" → "Start a project." "Learn more" → "See how it works." "Get in touch" → "Book a call."

### Objection Handling Inline
"Start free trial" with "No credit card. Cancel anytime." handles the commitment objection.

### Domain-Specific Language
Use the words your audience uses. A developer tool says "deploy," not "activate your digital experience." A healthcare product says "patients," not "users."

## Copy Self-Audit

Before shipping, re-read every visible string. Flag and rewrite anything that is:
- Grammatically broken or has unclear referents
- Reads like an LLM trying to sound thoughtful (passive-aggressive humility, mock-poetic micro-meta, forced metaphors)
- Uses filler verbs (Elevate, Seamless, Unleash, Revolutionize, Next-Gen)
- Could be replaced with a concrete noun + specific outcome

## Content Density by Page Type

| Page type | Max sections | Max words per section | Visual requirement |
|---|---|---|---|
| Landing (SaaS) | 8 | 25 words body, 8 words headline | Screenshots, demo video, logos |
| Landing (consumer brand) | 6 | 20 words body, 6 words headline | Photography, product shots |
| Portfolio | 5 | 15 words body, 5 words headline | Project images (real work) |
| Dashboard | N/A | Labels only, no paragraphs | Charts, tables, data |
| Blog post | N/A | Prose length | Hero image, inline diagrams |

## Reference Analysis

When a user provides inspiration (URLs, screenshots, brand names), extract concrete traits. Do not copy vibe words.

**Extract:**
- Layout structure (hero shape, section cadence, grid pattern)
- Color strategy (restrained/committed/full/drenched)
- Type hierarchy (sizes, weights, pairings, letter-spacing)
- Motion vocabulary (which elements animate, how fast, what easing)
- Content density (words per section, image-to-text ratio)
- Interactive patterns (hover states, transitions, navigation style)

**Do not copy:**
- Exact colors (extract the strategy, not the hex)
- Brand-specific fonts (extract the class: geometric sans, humanist serif, mono)
- Vibe words as design directives ("it feels expensive" → what makes it feel expensive? Larger type, more whitespace, fewer elements, better photography)

## Pattern Originality Check

Before finalizing, verify the output does not match the vg-design default aesthetic:
- Asymmetric zinc-900 UI with button-in-button CTAs
- Occasional double-bezel cards
- Geist + Geist Mono
- `text-4xl md:text-6xl tracking-tighter` hero

If the output consistently looks like this across different briefs, the skill is overriding the brief. Vary: font choice (within allowed families), hero structure, section rhythm, CTA style, and image treatment. The dials should produce visibly different results.
