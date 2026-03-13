# Remotion Video Director

An interactive Claude Code skill that guides you from "I need a video" to a rendered MP4 with music, through expert deliberation, scenario design, and battle-tested Remotion patterns.

<video src="https://github.com/BayramAnnakov/remotion-video-director/releases/download/v2.1.0/promo.mp4" width="100%" autoplay loop muted playsinline></video>

## Install

```bash
npx skills add BayramAnnakov/remotion-video-director
```

## What It Does

Unlike the official `remotion-dev/skills` (which teaches Claude the Remotion API), Video Director handles the **creative and strategic layer**:

| | Official Remotion Skill | Video Director |
|---|---|---|
| Focus | API reference (37 rule files) | Creative strategy + guided build |
| Answers | "How do I use `interpolate()`?" | "What video should I make?" |
| Experts | None | 3-4 experts per video type |
| Narrative | None | Scene structure + emotional arc |
| Data design | None | Reusable template architecture |
| Audio | Basic API docs | Music sourcing, integration, voice-over |
| Style presets | None | 4 creative directions (Jobs, Bloomberg, Narrative, Hacker) |

They're complementary - install both for the best experience.

## Four-Phase Workflow

```
Think ──► Design ──► Build ──► Review
```

**Phase 1 - Think**: Interactive discovery (one question at a time), creative direction choice, expert panel deliberation, creative brief

**Phase 2 - Design**: Scene-by-scene scenario with timing, data model, visual style

**Phase 3 - Build**: Scaffold Remotion project, create scene components, add music, render MP4

**Phase 4 - Review**: Multi-expert scorecard, user feedback loop, iterate until polished

## Creative Direction Presets

| Style | Feel | Best For |
|-------|------|----------|
| **Minimal/Jobs** | Black bg, dramatic pauses, one idea per frame | Product launches, big ideas |
| **Data-Rich/Bloomberg** | Dark navy, animated charts, dense but organized | Meeting recaps, reports |
| **Warm/Narrative** | Warm tones, personal quotes, serif accents | Course intros, community |
| **Terminal/Hacker** | Code theme, typewriter, monospace | Technical demos, dev tools |

## Five Video Archetypes

| Type | Use Case | Expert Panel |
|---|---|---|
| **Product Launch** | SaaS launches, feature announcements | Brand Strategist, Video Director, UX Designer, Growth Marketer |
| **Recurring Data Report** | Weekly teasers, meeting summaries | Data Architect, Content Strategist, Viz Designer |
| **Narrative Intro** | Course/workshop intros, event openers | Narrative Designer, Instructional Designer, Social Proof Curator, Cinematographer |
| **Technical Demo** | Product demos, engineering walkthroughs | Developer Advocate, UX Designer, Motion Designer, Copywriter |
| **Growth Marketing** | A/B test variants, social campaigns | Growth Marketer, Copywriter, Data Analyst, Brand Guardian |

## Audio Integration

- **Music sourcing**: Guided selection from Pixabay (free, royalty-free)
- **Scene-adaptive volume**: Music lowers during data-heavy scenes
- **Voice-over pipeline**: Script generation + ElevenLabs integration
- **Music style matching**: Each creative direction has recommended music genres

## What's New in v2.0

- **Four-phase workflow**: Think, Design, Build, Review (Review is now its own phase)
- **Interactive Phase 1**: Questions asked one at a time with wait-for-answer, not a form dump
- **4 Creative Direction presets**: Minimal/Jobs, Bloomberg, Warm/Narrative, Terminal/Hacker
- **Style inspirations**: Each creative direction includes reference creators and aesthetics
- **Reference input**: Share screenshots or videos you like as style references
- **Music as first-class step**: Guided music sourcing, integration, and scene-adaptive volume
- **Voice-over pipeline**: Script generation + ElevenLabs + music ducking
- **Premium styling patterns**: Font variety, extreme size contrast, negative space, dramatic pauses
- **2 new expert roles**: Cinematographer (visual composition) + Music Director (audio harmony)
- **Scene focus rule**: Each scene = ONE idea. Tangential content gets cut.
- **"Make it premium" shortcut**: Switches to Minimal/Jobs direction automatically

## Example

```
You: "I need a 30-second launch video for my SaaS product"

Video Director:
  Phase 1 - Think:
    1. "What is this video for?" (waits for your answer)
    2. Offers creative direction: Minimal/Jobs, Bloomberg, Warm, Terminal
    3. "Any reference videos you like?" (optional)
    4. Assembles expert panel, generates creative brief
    5. You confirm or adjust the brief

  Phase 2 - Design:
    6. Designs 6-scene scenario with timing and data model

  Phase 3 - Build:
    7. Scaffolds Remotion project, builds scene components
    8. Adds background music with scene-adaptive volume
    9. Renders MP4

  Phase 4 - Review:
    10. Multi-expert review with scorecard
    11. You review, give feedback, iterate until satisfied
```

## Troubleshooting

### macOS: "Killed: 9" when rendering

Remotion downloads native binaries (Chrome Headless Shell, ffprobe, ffmpeg) that macOS quarantines. Clear the quarantine:

```bash
xattr -cr node_modules/.remotion/ 2>/dev/null
xattr -cr node_modules/.pnpm/@remotion+compositor-darwin-arm64*/node_modules/@remotion/compositor-darwin-arm64/ 2>/dev/null
```

### "this file does not contain registerRoot"

Your `Root.tsx` must call `registerRoot()`. Exporting a component alone is not enough:

```tsx
import { registerRoot, Composition } from "remotion";
const Root = () => <Composition ... />;
registerRoot(Root);
```

## Requirements

- Claude Code (or any agent that supports the Skills standard)
- Node.js 18+
- Works with or without the official `remotion-dev/skills` package

## License

MIT
