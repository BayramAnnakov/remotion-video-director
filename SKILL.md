---
name: remotion-video-director
description: |
  Interactive guide for creating Remotion videos - from strategic concept to rendered MP4.
  Use when the user wants to create a video, make a Remotion project, build a product demo video,
  generate a launch video, make a recurring content template, create a marketing video,
  or says "I need a video for...", "help me make a video", "video for my product", "remotion video".
  Covers the full creative process: expert deliberation, scenario design, build, and review.
  Works alongside the official remotion-dev/skills for API-level guidance.
version: 1.0.0
allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
  - Glob
  - Grep
  - Agent
  - AskUserQuestion
  - WebSearch
  - WebFetch
  - mcp__nanobanana__generate_image
---

# Remotion Video Director

An interactive skill that guides users from "I need a video" to a rendered MP4, through expert deliberation, scenario design, and battle-tested Remotion patterns.

## When to Use

Trigger this skill when the user:
- Wants to create a video using Remotion
- Needs a product demo, launch video, recurring content template, marketing video, or narrative intro
- Says "I need a video", "make a video for...", "create a Remotion video", "video as code"
- Wants help thinking through what video to make or how to structure it
- Has a Remotion project and wants expert review

## Relationship to Official Remotion Skill

The official `remotion-dev/skills` package teaches Claude the Remotion API (37 rule files covering animations, audio, captions, transitions, etc.). This skill handles the **creative and strategic layer** on top:

- Official skill: "How do I use `interpolate()`?" (technical reference)
- Video Director: "What video should I make and how should it flow?" (creative process)

If the official Remotion skill is installed, leverage it for API-specific details. If not, the `references/remotion-patterns.md` and `references/component-library.md` in this skill provide sufficient Remotion knowledge to build production videos.

---

## Three-Phase Workflow

### Phase 1: Strategic Framing

**Goal**: Understand what the user needs and assemble the right expert perspectives.

#### Step 1 - Discovery

Ask the user:
1. **What is this video for?** (product launch, recurring report, course/workshop intro, technical demo, growth marketing, or something else)
2. **Who will watch it?** (customers, team, investors, social media audience, conference attendees)
3. **What should viewers DO after watching?** (sign up, understand the product, share, attend, buy)
4. **Any constraints?** (duration, brand colors, existing assets, deadline)

Read `references/video-archetypes.md` to identify which archetype best matches. Present the match to the user with a brief explanation of why. If the user's need spans multiple archetypes, propose a hybrid.

#### Step 2 - Expert Panel Assembly

Based on the selected archetype, assemble a panel of 3-4 experts from `references/expert-definitions.md`. Each archetype has a recommended panel, but adjust based on the user's specific context.

**Generate expert perspectives** - this is NOT canned text. For each expert, generate a 3-5 sentence perspective addressing:
- What matters most from their angle
- One specific recommendation
- One risk to watch for

Present the expert perspectives as a brief roundtable discussion. Highlight where experts agree (reinforces the direction) and where they disagree (flags a decision the user needs to make).

#### Step 3 - Creative Brief

Synthesize the expert input into a structured brief:

```
## Creative Brief

**Purpose**: [one sentence]
**Audience**: [who, what they care about]
**Core Message**: [the ONE thing viewers should remember]
**Desired Action**: [what viewers should do next]
**Duration**: [seconds] ([frames] frames at 30fps)
**Emotional Arc**: [e.g., curiosity -> proof -> confidence -> action]

## Scene Sequence
| # | Scene | Duration | Purpose |
|---|-------|----------|---------|
| 1 | ...   | Xs       | ...     |

## Data Model (if applicable)
[What's parameterized vs hardcoded - the reusable template structure]

## Visual Style
- Theme: [dark/light]
- Colors: [primary, accent, secondary]
- Typography: [heading font, body font, mono font]
- Animation feel: [snappy/gentle/bouncy]
```

Present the brief and ask the user to confirm or adjust before proceeding.

---

### Phase 2: Scenario Design

**Goal**: Turn the brief into a detailed scene-by-scene scenario.

#### Step 4 - Scene Design

For each scene in the sequence, specify:
- **Content**: Exact text, data, visuals
- **Components**: Which reusable components to use (from `references/component-library.md`)
- **Animation**: Entry/exit animations, timing in frames
- **Transitions**: How scenes connect (crossfade, cut, slide)

Read `references/remotion-patterns.md` for critical Remotion constraints (monotonic interpolate ranges, useCurrentFrame scope, Unicode escapes, safe zones).

**Frame Budget**: Map out the entire timeline:
```
const TIMING = {
  scene1: { start: 0, dur: 6 },   // Hook
  scene2: { start: 6, dur: 10 },  // ...
  // Total must match composition durationInFrames / fps
};
```

#### Step 5 - Data Architecture

If the video is data-driven (recurring report, multi-variant marketing):
- Design the config object that changes between renders
- Identify what stays constant (visual template) vs what varies (content)
- Show the user the config shape and confirm

Example pattern (from WeeklyIntro):
```typescript
const weekData = {
  weekLabel: "Mar 3 - Mar 9",
  heroNumber: 312,
  heroLabel: "new signups",
  // ... fields that change weekly
};
```

#### Step 6 - User Confirmation

Present the complete scenario. Ask: "Ready to build, or want to adjust anything?"

---

### Phase 3: Build + Review

**Goal**: Create the Remotion project and iterate to a polished result.

#### Step 7 - Scaffold Project

Create the Remotion project structure:
```
<project>/
├── src/
│   ├── Root.tsx
│   ├── Video.tsx           # or <VideoName>.tsx
│   ├── theme/
│   │   ├── colors.ts
│   │   ├── fonts.ts
│   │   └── springs.ts
│   ├── scenes/
│   │   ├── Scene1Hook.tsx
│   │   └── ...
│   ├── components/
│   │   ├── FadeIn.tsx
│   │   └── ...
│   └── data/
│       └── config.ts       # if data-driven
├── public/
│   └── illustrations/      # if using background images
├── package.json
├── tsconfig.json
└── remotion.config.ts
```

Install dependencies: `pnpm install`

#### Step 8 - Build Scenes

Build each scene component following the scenario from Phase 2. Use components from `references/component-library.md`.

**Critical build rules** (from `references/remotion-patterns.md`):
- All animation via `useCurrentFrame()` + `interpolate()`/`spring()` - NEVER CSS transitions
- Always clamp interpolation: `{ extrapolateLeft: "clamp", extrapolateRight: "clamp" }`
- `useCurrentFrame()` is relative to nearest `<Sequence>`, not global timeline
- Keep text in upper 85% of frame (bottom gets cropped in presentations)
- Minimum 24px font for screen-share readability
- Music volume: fade in 2s, cruise at 0.3-0.35, fade out 2s

#### Step 9 - Render and Preview

```bash
# Open Remotion Studio for live preview
pnpm start
# or: npx remotion studio

# Render final MP4
npx remotion render src/Root.tsx <CompositionId> out/<name>.mp4 --codec=h264 --crf=18
```

Open the video for the user to review.

#### Step 10 - Multi-Expert Review

After the user has seen the video, run the review protocol from `references/expert-definitions.md`.

Generate a scorecard:
```
| Dimension              | Priority | Score |
|------------------------|----------|-------|
| Hook clarity           | High     | ?/5   |
| Core message landed    | Critical | ?/5   |
| Pacing / dynamism      | High     | ?/5   |
| Visual readability     | High     | ?/5   |
| Target action clear    | Medium   | ?/5   |
| Audience fit           | High     | ?/5   |
```

Each expert reviews from their angle. Synthesize into:
1. **Strengths** - what works well
2. **Improvements** - specific, actionable changes
3. **Verdict** - ship as-is, needs one more pass, or needs significant rework

Ask the user which improvements to apply, then iterate.

---

## Adaptation Guidelines

### When the user already has a Remotion project
Skip Phase 1-2 scaffolding. Focus on reviewing existing code, suggesting improvements, or adding new scenes/compositions.

### When the user wants a quick video (no deliberation)
Compress Phase 1 to a single question about video type. Skip the full expert panel - pick the top 2 experts and generate a brief scenario. Move to build quickly.

### When the user wants only the concept (no code)
Run Phase 1-2 fully. Output the creative brief and scenario as a document. Skip Phase 3.

### When the user wants multi-variant videos
Use the Growth Marketing archetype. Design the variant matrix (what changes per variant). Build one base video, then show how to parameterize for N variants via config objects.

---

## Quality Checklist (before declaring done)

- [ ] Video renders without errors
- [ ] No text overlapping other text (common bug - check phase transitions)
- [ ] No text in bottom 15% of frame
- [ ] All animations use frame-driven timing (no CSS transitions)
- [ ] Music fades in/out smoothly (if present)
- [ ] Duration matches the creative brief
- [ ] Core message is clear within first 5 seconds
- [ ] CTA or closing is memorable
- [ ] Data model is clean and reusable (if data-driven)
