# Expert Definitions & Review Protocol

## Expert Roles

Each expert has a specific lens they bring to video creation. When assembling a panel, pick 3-4 experts that match the video's archetype and context.

### Brand Strategist
**Lens**: Does this video reinforce the brand's positioning?
**Asks**:
- Is the value proposition clear in the first 5 seconds?
- Does the visual language match the brand identity?
- Is competitive differentiation visible (not just features, but positioning)?
**Review criteria**: Hook clarity, message consistency, brand alignment

### Video Director
**Lens**: Does the pacing and structure keep viewers engaged?
**Asks**:
- Is there dead time (frames with no new information)?
- Does the emotional arc build toward the CTA?
- Are transitions smooth or jarring?
**Review criteria**: Pacing, scene flow, emotional journey, timing

### UX Designer
**Lens**: Are the product demos and interactions clear?
**Asks**:
- Can viewers understand the product from the demo alone?
- Are browser mockups and UI elements readable at video resolution?
- Is the interaction flow logical (input -> process -> output)?
**Review criteria**: Demo clarity, visual hierarchy, readability

### Growth Marketer
**Lens**: Will this video convert viewers into users?
**Asks**:
- Is the CTA strong and specific?
- Does social proof appear before the ask?
- Is the video optimized for the distribution channel (length, aspect ratio, captions)?
**Review criteria**: CTA strength, conversion path, channel fit

### Data Architect
**Lens**: Is this video template efficiently reusable?
**Asks**:
- Is the config object clean and minimal?
- Can a non-developer update the data and re-render?
- Are there unnecessary hardcoded values that should be in config?
**Review criteria**: Config cleanliness, separation of data/presentation, automation readiness

### Content Strategist
**Lens**: Does the recurring format build audience habit?
**Asks**:
- Is the structure consistent enough to be recognized?
- Does each edition deliver enough novelty within the format?
- Is the opening and closing ritualized (brand recall)?
**Review criteria**: Format consistency, novelty balance, ritualization

### Visualization Designer
**Lens**: Do charts and data displays communicate effectively?
**Asks**:
- Is the hero number prominent enough?
- Do chart animations aid comprehension or just look cool?
- Is the data hierarchy clear (what matters most)?
**Review criteria**: Data clarity, animation purpose, visual hierarchy

### Narrative Designer
**Lens**: Does the story work?
**Asks**:
- Is there a clear 3-act structure (setup, value, bridge)?
- Does the narrative earn the viewer's attention at each beat?
- Is the closing memorable?
**Review criteria**: Story arc, emotional beats, memorability

### Instructional Designer
**Lens**: Does this set the right expectations for learning?
**Asks**:
- Is the audience clearly identified?
- Are learning outcomes implied or stated?
- Does the video motivate participation?
**Review criteria**: Audience clarity, expectation setting, motivation

### Social Proof Curator
**Lens**: Is the evidence of value convincing?
**Asks**:
- Are participant quotes specific (not generic praise)?
- Do chat messages feel authentic?
- Is the proof relevant to the target audience?
**Review criteria**: Authenticity, specificity, relevance

### Developer Advocate
**Lens**: Will developers trust and understand this demo?
**Asks**:
- Is the pacing right for a technical audience (not too fast, not patronizing)?
- Are the right features highlighted (not just flashy ones)?
- Does the demo show real usage, not a contrived example?
**Review criteria**: Technical accuracy, pacing, authenticity

### Motion Designer
**Lens**: Do animations serve the content?
**Asks**:
- Are animations consistent (same easing, same timing for similar elements)?
- Do transitions aid comprehension or just add visual noise?
- Is the animation vocabulary restrained (3-4 patterns max)?
**Review criteria**: Consistency, purposefulness, restraint

### Copywriter
**Lens**: Does every word earn its place?
**Asks**:
- Is the hook copy specific and punchy?
- Are there wasted words that slow the pace?
- Does the CTA use active, urgent language?
**Review criteria**: Hook strength, economy of language, CTA urgency

### Data Analyst
**Lens**: Will the variants produce learnable results?
**Asks**:
- Are variants different enough to test distinct hypotheses?
- Is one variable changing at a time (or is it too many variables)?
- Can results be measured with available analytics?
**Review criteria**: Hypothesis clarity, variable isolation, measurability

### Brand Guardian
**Lens**: Are all variants on-brand?
**Asks**:
- Do all variants maintain visual consistency?
- Is brand voice preserved across different messaging?
- Are there any variants that could damage brand perception?
**Review criteria**: Visual consistency, voice consistency, brand safety

### Cinematographer
**Lens**: Does the visual composition create emotional impact?
**Asks**:
- Is there enough negative space? (Cramped frames feel cheap)
- Are key elements centered and visually balanced?
- Is the color palette cohesive and consistent across scenes?
- Does the typography hierarchy create clear visual weight?
- Are transitions smooth or jarring?
**Review criteria**: Composition, color harmony, visual weight balance, premium feel

### Music Director
**Lens**: Does the audio enhance the emotional arc?
**Asks**:
- Does the music style match the creative direction?
- Is volume balanced - not too loud to distract, not too quiet to notice?
- Does the music lower during data-heavy or text-heavy scenes?
- Does the ending feel complete (not abruptly cut)?
- Is there silence where silence would be more powerful?
**Review criteria**: Music-mood alignment, volume dynamics, emotional arc support

---

## Recommended Panels by Archetype

| Archetype | Panel |
|-----------|-------|
| Product Launch | Brand Strategist, Video Director, UX Designer, Growth Marketer |
| Recurring Data Report | Data Architect, Content Strategist, Visualization Designer, Automation Engineer |
| Narrative Intro | Narrative Designer, Instructional Designer, Social Proof Curator, Brand Designer |
| Technical Demo | Developer Advocate, UX Designer, Motion Designer, Copywriter |
| Growth Marketing | Growth Hacker, Copywriter, Data Analyst, Brand Guardian |

---

## Multi-Expert Review Protocol

### When to Run

Run the review after the first render is complete and the user has seen the video. The review helps identify improvements before the video is finalized.

### Scorecard Template

Generate a scorecard tailored to the video type. Universal dimensions:

| Dimension | Priority | Score | Notes |
|-----------|----------|-------|-------|
| Hook clarity (first 3-5s) | Critical | /5 | Does the opening grab attention? |
| Core message landed | Critical | /5 | Would a viewer remember the ONE key point? |
| Pacing / dynamism | High | /5 | Any dead time? Too fast/slow? |
| Visual readability | High | /5 | Text size, contrast, safe zones? |
| Target action clear | High | /5 | Does the viewer know what to do next? |
| Audience fit | Medium | /5 | Right tone/level for the intended viewer? |

Add 2-3 type-specific dimensions based on the archetype (e.g., "Config reusability" for data reports, "Demo credibility" for tech demos).

### Review Output Structure

For each expert on the panel, generate a 3-5 sentence review:

```
### [Expert Role] Perspective

[What works well from this angle.]
[What could be improved - specific, actionable.]
[One specific recommendation with frame/scene reference.]
```

Then synthesize:

```
### Synthesis

**Strengths**: [2-3 bullet points where experts agree]
**Improvements**: [2-3 specific changes, prioritized]
**Verdict**: [Ship as-is / Needs one more pass / Needs significant rework]
```

### Cross-Variant Review (Growth Marketing only)

When reviewing multiple variants, add a comparison table:

| Dimension | Variant A | Variant B | Variant C |
|-----------|-----------|-----------|-----------|
| Hook strength | ... | ... | ... |
| Differentiation | ... | ... | ... |
| CTA urgency | ... | ... | ... |

Recommend which variant to lead with and why.
