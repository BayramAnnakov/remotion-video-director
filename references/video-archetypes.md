# Video Archetypes

Five proven video types derived from production Remotion projects. Each archetype includes a recommended expert panel, scene template, timing guide, and data model.

---

## 1. Product Launch

**When to use**: Launching a product, feature, or service. The viewer should understand the value proposition and take action (sign up, try it, buy).

**Reference project**: PHLaunchVideo (50s, 1500 frames, 30fps)

### Expert Panel
- **Brand Strategist**: Value prop hierarchy, hook design, competitive positioning
- **Video Director**: Pacing, scene arc, emotional journey from pain to solution
- **UX Designer**: Demo sequences, browser mockups, result visualization
- **Growth Marketer**: CTA optimization, social proof placement, conversion path

### Scene Template (40-60 seconds)
| # | Scene | Duration | Purpose | Components |
|---|-------|----------|---------|------------|
| 1 | Hook | 3-5s | Shock/intrigue - price, stat, or provocative question | Bold text, strikethrough, spring-animated reveal |
| 2 | Problem/Cost | 5-8s | Show the pain or current cost | Split layout, stacked items with slide-in |
| 3 | Tagline | 2-3s | One-line value proposition | FadeIn, centered |
| 4-6 | Demo Clips | 8s each | Show 2-3 product features in action | BrowserMockup, result cards, badges |
| 7 | Social Proof | 6-8s | Testimonial or user quote | Quote with photo, serif accent |
| 8 | CTA | 5-8s | Clear call to action with pulsing button | Pulsing animation, FadeIn copy |

### Color Strategy
- Light/warm backgrounds work well for B2B SaaS (trustworthy, approachable)
- Dark backgrounds for dev tools, technical products
- Use red for pain/cost, green for savings/solution

### Key Pattern
The hook must land in under 3 seconds. Use immediate visibility (no fade-in) for the shock element, then animate the resolution.

### Data Model
Not typically data-driven. Each launch video is custom. However, the demo clip structure (clip phase + result phase) is reusable across products.

---

## 2. Recurring Data Report

**When to use**: Weekly/monthly content that follows the same visual template but with different data each time. Meetings, teasers, performance reports, newsletters.

**Reference project**: WeeklyIntro (42s, 1260 frames, 30fps)

### Expert Panel
- **Data Architect**: Config schema design, what changes vs what stays
- **Content Strategist**: Recurring format consistency, audience expectations, pacing
- **Visualization Designer**: Charts, counters, donut graphs, data hierarchy
- **Automation Engineer**: CI/CD rendering, template parameterization, batch rendering

### Scene Template (30-45 seconds)
| # | Scene | Duration | Purpose | Components |
|---|-------|----------|---------|------------|
| 1 | Cold Open | 4-6s | Brand identity + edition label | Spring-animated title, horizontal rule |
| 2 | Hero Number | 6-8s | The ONE metric that matters | Counter animation (0 -> N), bar chart |
| 3 | Breakdown | 8-10s | Split/comparison of key segments | Donut charts (SVG strokeDashoffset), split panels |
| 4 | Detail Grid | 6-8s | Supporting metrics | 2x2 card grid with spring animation, pulse effect |
| 5 | Narrative | 6-8s | Quote, insight, or commentary | Cross-fading quotes, typewriter effect |
| 6 | Closer | 4-6s | Logo + summary metrics | FadeIn title, dissolve to logo |

### Color Strategy
- Keep consistent across editions (brand recognition)
- Use accent color for the hero number (makes it pop)
- Muted colors for supporting data

### Key Pattern
**Data-config separation** is the core pattern. The config object sits at the top of the file:

```typescript
const weekData = {
  weekLabel: "Mar 3 - Mar 9",
  heroNumber: 312,
  heroLabel: "new signups",
  compareLabel: "5x baseline",
  segments: [
    { label: "Organic", value: 67, color: "#4AB0A0" },
    { label: "Paid", value: 33, color: "#E86B6B" },
  ],
  cards: [
    { label: "Pipeline", value: "$48K", trend: "+12%" },
    // ...
  ],
  quote: { text: "Best week since launch", author: "CEO" },
};
```

Change this object, re-render, done. No code changes needed.

### Data Model
This is THE data-driven archetype. Design the config shape carefully:
- Group related fields
- Use arrays for variable-length lists (segments, cards, quotes)
- Include a `weekLabel` or `editionLabel` for the cold open
- Consider: can Claude Code generate this config from a doc/spreadsheet/API?

---

## 3. Narrative Intro

**When to use**: Course, workshop, webinar, conference talk introductions. The viewer should understand the value and feel connected to the content/community.

**Reference project**: Workshop Behind-the-Scenes (90s, 2700 frames, 30fps)

### Expert Panel
- **Narrative Designer**: 3-act structure, emotional arc, tension and resolution
- **Instructional Designer**: Learning objectives, expectation setting, audience calibration
- **Social Proof Curator**: Participant quotes, chat simulations, engagement proof
- **Brand Designer**: Visual identity, color psychology, typography hierarchy

### Scene Template (60-120 seconds)
| # | Scene | Duration | Purpose | Components |
|---|-------|----------|---------|------------|
| 1 | Opening Hook | 5-8s | Provocative stat or question | TypingText with cursor |
| 2 | Social Proof | 8-12s | Real participant messages/quotes | ChatBubble wall, mini-message cascade |
| 3 | Tech/Structure | 10-12s | What was built and how | FileTree, CodeBlock, counter |
| 4 | Before/After | 8-10s | Show the transformation | SplitCompare panels |
| 5 | Build Montage | 8-10s | Volume of output produced | Abstract slide grid, counter hero |
| 6 | Personas | 15-20s | Who this is for (2-4 audience types) | PersonaCard with spring animation |
| 7 | Method | 10-15s | How it was made | MethodItem with slide-in |
| 8 | Closing CTA | 5-8s | "Your turn" + memorable line | TypingText, FadeIn, fade to black |

### Color Strategy
- Dark theme (navy/deep teal) for professional/tech courses
- Warm theme for creative/business courses
- Two accent colors: primary for "positive" elements, secondary for "contrast" elements
- Use participant photos/avatars to add warmth

### Key Pattern
The 3-act structure:
1. **Set the stage** (Scenes 1-3): What is this? Who's involved? What was built?
2. **Show the value** (Scenes 4-6): Why does it matter? Who benefits? What changed?
3. **Bridge forward** (Scenes 7-8): How was it done? Now it's your turn.

Personalization is key - use real participant names, quotes, and chat messages.

### Data Model
Semi data-driven. The participant data and quotes change per cohort, but the scene structure stays the same:

```typescript
const courseData = {
  title: "Workshop 1",
  participantCount: 30,
  stats: { contextLines: 247, slides: 9, exercises: 4 },
  participants: [
    { name: "Egor", role: "Product Manager", quote: "..." },
    // ...
  ],
  personas: [
    { title: "Enterprise Transformer", color: "#4AB0A0", description: "...", change: "..." },
    // ...
  ],
};
```

---

## 4. Technical Demo

**When to use**: Demonstrating a technical product, developer tool, API, or engineering workflow. The viewer should understand what it does and want to try it.

**Reference project**: W4DemoVideo (75s, 2250 frames, 30fps)

### Expert Panel
- **Developer Advocate**: What to show vs skip, pacing for technical audience, jargon level
- **UX Researcher**: User journey mapping, feature prioritization for demo flow
- **Motion Designer**: Terminal simulations, code reveals, browser mockups
- **Technical Writer**: Annotations, step clarity, progressive disclosure

### Scene Template (45-90 seconds)
| # | Scene | Duration | Purpose | Components |
|---|-------|----------|---------|------------|
| 1 | Title | 3-5s | Product/feature name + context | FadeIn title |
| 2 | Setup/Prompt | 5-8s | The user's intent or input | TypingText in terminal |
| 3 | Process | 8-12s | The tool working (reading, analyzing) | TerminalWindow with TypewriterText |
| 4 | Code Gen | 10-15s | Code being generated or modified | CodeBlock with line-by-line reveal |
| 5 | Test/Verify | 8-12s | Running tests, API calls, validation | Terminal output, green checkmarks |
| 6 | Result | 10-15s | The final output in context | BrowserMockup, live preview |
| 7 | Summary | 8-10s | Recap what just happened | Bullet list with staggered FadeIn |
| 8 | Close | 3-5s | "Try it" message | FadeIn with gradient |

### Color Strategy
- Dark theme (GitHub dark: #0D1117 or Navy: #0F172A)
- Terminal green (#4ADE80) for success/output
- Cyan (#06B6D4) for highlights
- Monospace fonts (JetBrains Mono, Fira Code) for all code/terminal

### Key Pattern
**The terminal simulation** is the star. The TerminalWindow component with TypewriterText inside creates the illusion of live coding. Pacing matters:
- Commands type at 2-3 chars/frame (fast, confident)
- Output appears line-by-line with 8-frame delay (readable but not slow)
- Results pause for 30+ frames (let viewer absorb)

### Data Model
Usually not data-driven (each demo is unique). But the scene timing object is the reusable pattern:

```typescript
const S = {
  title:   { start: 0,  dur: 5  },
  prompt:  { start: 5,  dur: 6  },
  process: { start: 11, dur: 10 },
  code:    { start: 21, dur: 12 },
  // ...
};
```

---

## 5. Growth Marketing

**When to use**: Creating multiple video variants for A/B testing, social media campaigns, or different audience segments. Same visual template, different messaging.

**Reference project**: CloneMyBestCustomer (7 variants - main, LinkedIn, Steve, slots, etc.)

### Expert Panel
- **Growth Hacker**: Variant strategy, hook testing, distribution channel optimization
- **Copywriter**: Messaging variants, headline testing, emotional triggers, CTA wording
- **Data Analyst**: What to measure, success metrics, sample sizing
- **Brand Guardian**: Consistency across variants, brand voice, visual identity

### Scene Template (15-30 seconds per variant)
| # | Scene | Duration | Purpose | Components |
|---|-------|----------|---------|------------|
| 1 | Hook | 2-4s | Attention-grabbing opening (THIS is what varies most) | Bold text, animation |
| 2 | Promise | 3-5s | What the viewer gets | FadeIn text |
| 3 | Demo/Proof | 5-10s | Show it working | BrowserMockup, process animation |
| 4 | Result | 3-5s | The outcome/deliverable | Result card, metrics |
| 5 | CTA | 3-5s | "Free" / "Try now" / "Sign up" | Pulsing button, end card |

### Color Strategy
- Keep brand colors constant across variants
- Vary: hook text, messaging copy, demo content
- Don't vary: visual structure, animation timing, CTA placement

### Key Pattern
**The variant matrix** defines what changes per video:

```typescript
const variants = {
  main:     { hook: "You know your best customer.", cta: "Clone them free", demo: "general" },
  linkedin: { hook: "Your competitor's best leads?", cta: "Find them now", demo: "linkedin" },
  steve:    { hook: "Meet Steve. He's your ideal customer.", cta: "Find 5 more like Steve", demo: "persona" },
  slots:    { hook: "5 matches. 60 seconds.", cta: "Free", demo: "speed" },
};
```

Build one base video component that accepts a variant config. Render N times with different configs.

### Data Model
Highly data-driven. The variant config is the core:

```typescript
interface Variant {
  id: string;
  hook: string;
  subhead?: string;
  demoType: "general" | "persona" | "speed" | "reasoning";
  ctaText: string;
  ctaUrl?: string;
  targetChannel: "social" | "linkedin" | "email" | "landing";
}
```

### Review Protocol for Variants
Use the multi-perspective scorecard from `expert-definitions.md`, but run it ACROSS variants:
- Which hook is strongest?
- Which demo creates most credibility?
- Which CTA drives most urgency?
- Are variants different enough to learn from A/B testing?

---

## Hybrid Archetypes

Some videos combine elements from multiple archetypes:

| Hybrid | Combines | Example |
|--------|----------|---------|
| **Launch + Demo** | Product Launch hook/CTA + Technical Demo middle | SaaS product launch with live demo |
| **Narrative + Data** | Narrative intro structure + Data Report metrics | Quarterly business review video |
| **Demo + Marketing** | Technical Demo base + Growth Marketing variants | API demo with variants per audience segment |

When a user's need spans archetypes, pick the primary (determines overall arc) and borrow specific scenes from the secondary.
