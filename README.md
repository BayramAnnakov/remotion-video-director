# Remotion Video Director

An interactive Claude Code skill that guides you from "I need a video" to a rendered MP4, through expert deliberation, scenario design, and battle-tested Remotion patterns.

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

They're complementary - install both for the best experience.

## Three-Phase Workflow

**Phase 1 - Think**: Identify video type, assemble expert panel, generate creative brief

**Phase 2 - Design**: Scene-by-scene scenario with timing, data model, visual style

**Phase 3 - Build**: Scaffold project, create components, render, multi-expert review

## Five Video Archetypes

| Type | Use Case | Expert Panel |
|---|---|---|
| **Product Launch** | SaaS launches, feature announcements | Brand Strategist, Video Director, UX Designer, Growth Marketer |
| **Recurring Data Report** | Weekly teasers, meeting summaries | Data Architect, Content Strategist, Viz Designer |
| **Narrative Intro** | Course/workshop intros, event openers | Narrative Designer, Instructional Designer, Social Proof Curator |
| **Technical Demo** | Product demos, engineering walkthroughs | Developer Advocate, Motion Designer, Tech Writer |
| **Growth Marketing** | A/B test variants, social campaigns | Growth Hacker, Copywriter, Data Analyst, Brand Guardian |

## Example

```
You: "I need a 30-second launch video for my SaaS product"

Video Director:
  1. Identifies: Product Launch archetype
  2. Assembles: Brand Strategist, Video Director, UX Designer, Growth Marketer
  3. Experts deliberate on hook, demo structure, CTA
  4. Proposes 6-scene scenario with timing
  5. You confirm or adjust
  6. Scaffolds Remotion project, builds scenes, renders MP4
  7. Multi-expert review with scorecard
```

## Requirements

- Claude Code (or any agent that supports the Skills standard)
- Node.js 18+
- Works with or without the official `remotion-dev/skills` package

## License

MIT
