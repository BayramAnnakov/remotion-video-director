# Battle-Tested Remotion Patterns

Hard-won lessons from production Remotion projects. Follow every one of these.

---

## Critical Rules

### 1. All animation MUST use `useCurrentFrame()`

CSS transitions, CSS animations, and Tailwind `animate-*` classes are **FORBIDDEN** in Remotion. They don't synchronize with frame-based rendering - the video will look correct in the browser but produce garbage when rendered.

```tsx
// WRONG - will not render correctly
<div style={{ transition: "opacity 0.5s" }}>

// CORRECT - frame-driven
const frame = useCurrentFrame();
const opacity = interpolate(frame, [0, 15], [0, 1], {
  extrapolateLeft: "clamp",
  extrapolateRight: "clamp",
});
<div style={{ opacity }}>
```

### 2. `interpolate()` requires strictly monotonically increasing input ranges

**WILL CRASH:**
```tsx
interpolate(frame, [0, 15, 15, 30], [0, 1, 1, 0]); // Duplicate 15!
interpolate(frame, [400, 420, 750, 750], [0, 1, 1, 0]); // Duplicate 750!
```

**FIX:** Bump duplicates by 1:
```tsx
interpolate(frame, [0, 15, 16, 30], [0, 1, 1, 0]);
// Or for fade-in only:
interpolate(frame, [400, 420], [0, 1], clamp);
```

### 3. `useCurrentFrame()` is relative to the nearest `<Sequence>`

When using `<Sequence from={300}>`, `useCurrentFrame()` inside that Sequence returns 0 at global frame 300.

When using opacity-based phase visibility (not Sequence), `useCurrentFrame()` returns the scene-level frame. Components with `startFrame` must use absolute frame numbers matching when the phase becomes visible.

### 4. Always define the clamp shorthand

Put this at the top of every scene file:
```tsx
const clamp = {
  extrapolateLeft: "clamp" as const,
  extrapolateRight: "clamp" as const,
};
```

Without clamping, `interpolate()` extrapolates beyond the defined range, producing unexpected negative values or values > 1.

### 5. Unicode escapes in JSX

**WILL FAIL TO COMPILE:**
```tsx
<div>❌ Without context</div>          // Unicode literal may fail
```

**FIX:** Use expression syntax:
```tsx
<div>{"\u274C"} Without context</div>  // 4-digit escape in expression
```

### 6. Overlapping Sequences cause text overlay

Two `<Sequence>` components visible at the same time both render simultaneously. This creates visual chaos with text from different scenes overlapping.

**FIX:** Use non-overlapping phases via opacity-based transitions:
```tsx
const phase1Opacity = interpolate(frame, [0, 10, 140, 170], [0, 1, 1, 0], clamp);
const phase2Opacity = interpolate(frame, [170, 185, 370, 400], [0, 1, 1, 0], clamp);

<AbsoluteFill style={{ opacity: phase1Opacity }}>...</AbsoluteFill>
<AbsoluteFill style={{ opacity: phase2Opacity }}>...</AbsoluteFill>
```

### 7. Render command needs entry point

**WRONG:**
```bash
npx remotion render Video out.mp4
```

**CORRECT:**
```bash
npx remotion render src/Root.tsx Video out.mp4
```

`Root.tsx` **must call `registerRoot()`** - the CLI requires it. Exporting a component alone is NOT enough and will error with "this file does not contain registerRoot".

### 8. macOS quarantine kills Remotion binaries (SIGKILL)

On macOS, downloaded binaries (Chrome Headless Shell, ffprobe, ffmpeg) get a `com.apple.provenance` quarantine attribute that causes the OS to kill them with SIGKILL (signal 9). Symptoms: "Killed: 9" with no other output, or "Browser process exited with signal SIGKILL", or ffprobe "Command was killed with SIGKILL".

**FIX:** Clear quarantine on all Remotion native binaries after install:
```bash
xattr -cr node_modules/.remotion/ 2>/dev/null
xattr -cr node_modules/.pnpm/@remotion+compositor-darwin-arm64*/node_modules/@remotion/compositor-darwin-arm64/ 2>/dev/null
```

If `xattr -cr` doesn't work, clear individually:
```bash
xattr -d com.apple.provenance node_modules/.remotion/chrome-headless-shell/mac-arm64/chrome-headless-shell-mac-arm64/chrome-headless-shell
xattr -d com.apple.provenance node_modules/.pnpm/@remotion+compositor-darwin-arm64@*/node_modules/@remotion/compositor-darwin-arm64/ffprobe
xattr -d com.apple.provenance node_modules/.pnpm/@remotion+compositor-darwin-arm64@*/node_modules/@remotion/compositor-darwin-arm64/ffmpeg
xattr -d com.apple.provenance node_modules/.pnpm/@remotion+compositor-darwin-arm64@*/node_modules/@remotion/compositor-darwin-arm64/remotion
```

### 9. npx may fail to run Remotion CLI on macOS

If `npx remotion` or `pnpm exec remotion` gets killed immediately, run the bin script directly via `sh`:
```bash
sh node_modules/.bin/remotion render src/Root.tsx <CompositionId> out/video.mp4 --codec=h264 --crf=18
```

---

## Animation Patterns

### Spring Configs

Three battle-tested configs for different feels:

```typescript
export const SPRING_CONFIGS = {
  BOUNCE:  { damping: 12, mass: 0.5, stiffness: 200 }, // Cards, badges - snappy impact
  GENTLE:  { damping: 20, mass: 1,   stiffness: 100 }, // Text fade-in - smooth, subtle
  SNAPPY:  { damping: 15, mass: 0.8, stiffness: 300 }, // Text reveals - rapid response
};
```

Usage:
```tsx
const progress = spring({
  frame: frame - delay,
  fps,
  config: SPRING_CONFIGS.BOUNCE,
});
```

### Staggered Reveals

For lists, grids, or cascading elements:
```tsx
{items.map((item, i) => {
  const itemDelay = baseDelay + i * 8; // 8 frames between each item
  const opacity = interpolate(frame - itemDelay, [0, 12], [0, 1], clamp);
  return <div key={i} style={{ opacity }}>{item}</div>;
})}
```

### Counter Animation

Animated counting with overshoot protection:
```tsx
const counterValue = Math.min(
  targetValue,
  Math.floor(interpolate(frame, [startFrame, startFrame + 120], [0, targetValue], clamp))
);
```

### Typewriter Effect

Character-by-character reveal:
```tsx
const elapsed = Math.max(0, frame - startFrame);
const charsToShow = Math.min(Math.floor(elapsed * speed), text.length);
const visibleText = text.slice(0, charsToShow);
```

Speed guide: 1.5-2.5 chars/frame for readable typing. Faster (3+) for commands, slower (1.0) for dramatic text.

### Cross-Dissolve Envelope

Wrap each scene in a fade-in/fade-out:
```tsx
const envelopeFrames = 8;
const opacity = interpolate(
  frame,
  [0, envelopeFrames, durationInFrames - envelopeFrames, durationInFrames],
  [0, 1, 1, 0],
  clamp
);
```

### Pulsing Effect

For CTAs or attention-drawing elements:
```tsx
const pulse = Math.sin((frame % 40) / 40 * Math.PI * 2) * 0.1 + 1; // Scale 0.9-1.1
<div style={{ transform: `scale(${pulse})` }}>
```

---

## Timing Rules of Thumb

| Element | Recommended Timing |
|---------|-------------------|
| TypingText speed | 1.5-2.5 chars/frame |
| Chat bubble appearance | 12 frames fade-in |
| FadeIn component | 30 frames (1 second) |
| File tree expandDelay | 8 frames between items |
| Code block lineDelay | 8-10 frames between lines |
| Phase transition gap | 15-30 frames between phases |
| Counter animation | 90-120 frames (3-4 seconds) |
| Scene-ending hold | 30-60 frames before next scene |
| Cross-dissolve | 8-12 frames |

### Frame Budget Pattern

Define timing at the top of the main component:
```typescript
const FPS = 30;
const S = {
  hook:    { start: 0,  dur: 5  },
  problem: { start: 5,  dur: 8  },
  demo:    { start: 13, dur: 15 },
  proof:   { start: 28, dur: 7  },
  cta:     { start: 35, dur: 5  },
};
// Total: 40 seconds = 1200 frames
```

In Sequence wiring:
```tsx
<Sequence from={S.hook.start * FPS} durationInFrames={S.hook.dur * FPS} name="Hook">
  <SceneHook />
</Sequence>
```

---

## Audio Integration

See `references/music-and-audio.md` for the full audio guide (sourcing, scene-adaptive volume, voice-over pipeline).

**Quick reference volumes**:
- Cruising: 0.30 (background ambient)
- Data-heavy scenes: 0.15 (let viewers read)
- During narration: 0.10-0.12 (music ducking)
- Fade in/out: 2 seconds (60 frames at 30fps)

```tsx
const musicVolume = interpolate(
  frame,
  [0, 60, totalFrames - 60, totalFrames],
  [0, 0.30, 0.30, 0],
  { extrapolateLeft: "clamp", extrapolateRight: "clamp" }
);

<Audio src={staticFile("music.mp3")} volume={musicVolume} />
```

---

## Premium Styling Patterns

These patterns separate "animated slideshow" from "looks expensive". Apply based on the chosen Creative Direction.

### Font Variety

Do NOT use Inter for everything. Mix fonts for visual hierarchy:

```typescript
import { loadFont as loadInter } from "@remotion/google-fonts/Inter";
import { loadFont as loadJetBrainsMono } from "@remotion/google-fonts/JetBrainsMono";
import { loadFont as loadPlayfairDisplay } from "@remotion/google-fonts/PlayfairDisplay";

const { fontFamily: inter } = loadInter();        // Body text, labels
const { fontFamily: mono } = loadJetBrainsMono();  // Code, numbers, data
const { fontFamily: serif } = loadPlayfairDisplay(); // Quotes, emotional text
```

For Minimal/Jobs style, use Impact (system font) or a bold sans-serif for headlines:
```tsx
fontFamily: "Impact, 'Arial Black', sans-serif"
```

### Extreme Size Contrast

The #1 trick for premium feel. Headlines should be 3-4x the size of body text:

| Element | Standard | Premium |
|---------|----------|---------|
| Headline | 48px | 72-96px |
| Body text | 24px | 20-24px |
| Labels/captions | 18px | 14-18px |
| Hero numbers | 64px | 96-120px |

### Negative Space

Premium = restraint. More padding, fewer elements, more room to breathe.

| Style | Padding | Max elements per frame |
|-------|---------|----------------------|
| Standard | 60-80px | 4-6 |
| Minimal/Jobs | 100-140px | 1-2 |
| Data-Rich | 40-60px | 5-8 (organized in grid) |

### One Idea Per Frame

If you need more than 15 words on a frame, split into two scenes. For Minimal/Jobs, limit to 7 words.

**Bad**: Scene with title + subtitle + 3 bullet points + chart
**Good**: Scene 1: title + subtitle. Scene 2: chart with one label.

### Animation on Entrance

Every element must animate in. No element should appear static on a cut. Even a simple 8-frame opacity fade is better than instant appearance.

```tsx
// Minimum: every element gets at least this
const opacity = interpolate(frame - delay, [0, 8], [0, 1], clamp);
```

### Smooth Transitions

Cross-dissolve between EVERY scene. Hard cuts feel cheap (exception: Terminal/Hacker style where hard cuts fit).

```tsx
// Wrap every scene in CrossDissolve
<Sequence from={S.hook.start * FPS} durationInFrames={S.hook.dur * FPS}>
  <CrossDissolve envelopeFrames={10}>
    <SceneHook />
  </CrossDissolve>
</Sequence>
```

### Dramatic Pauses (Minimal/Jobs only)

After revealing a key statement, hold for 60+ frames (2 seconds) before the next element. Let the idea land.

```tsx
// Show headline at frame 0, hold until frame 90 before showing next element
const headlineOpacity = interpolate(frame, [0, 15], [0, 1], clamp);
const subtitleOpacity = interpolate(frame, [90, 105], [0, 1], clamp);
```

---

## Safe Zones and Readability

- **Padding**: 80-120px from all edges (more for Minimal/Jobs)
- **Text placement**: Keep in upper 85% of frame (bottom gets cropped in presentations/embeds)
- **Center key questions**: Important questions or headlines should be vertically centered (`alignItems: "center"`, `justifyContent: "center"`), not pushed to edges
- **Minimum font size**: 24px for anything that needs to be readable on Zoom screen shares
- **Max text per frame**: 15-20 words for body slides, 7 words for headline slides (Minimal/Jobs)
- **Data simplification**: On data-heavy slides, show ONE hero metric with 2-3 supporting numbers. Not a full dashboard.
- **Contrast**: Light text on dark backgrounds needs to be #F0F0F0 or brighter (not gray)

---

## Project Setup

### Standard package.json

```json
{
  "scripts": {
    "start": "npx remotion studio",
    "build": "npx remotion render src/Root.tsx Video out/video.mp4 --codec=h264 --crf=18",
    "preview": "npx remotion preview"
  },
  "dependencies": {
    "@remotion/cli": "4.0.261",
    "@remotion/google-fonts": "4.0.261",
    "react": "^18.3.1",
    "react-dom": "^18.3.1",
    "remotion": "4.0.261"
  },
  "devDependencies": {
    "@types/react": "^18.3.12",
    "typescript": "^5.7.0"
  }
}
```

### Standard Root.tsx

```tsx
import React from "react";
import { registerRoot, Composition } from "remotion";
import { Video } from "./Video";

const RemotionRoot: React.FC = () => {
  return (
    <Composition
      id="Video"
      component={Video}
      durationInFrames={2700}  // fps * seconds
      fps={30}
      width={1920}
      height={1080}
    />
  );
};

registerRoot(RemotionRoot);
```

### Standard tsconfig.json

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "commonjs",
    "jsx": "react-jsx",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true
  },
  "include": ["src"]
}
```

---

## Troubleshooting

| Symptom | Cause | Fix |
|---------|-------|-----|
| Blank frames / flickering | Overlapping Sequences or missing clamp | Use opacity phases, add clamp to all interpolate() |
| Text appears all at once | startFrame doesn't match phase visibility | Align startFrame with when the phase opacity > 0 |
| Cards have uneven heights | Missing alignItems: stretch | Add `alignItems: "stretch"` to flex parent |
| Counter overshoots | Missing Math.min() | Wrap in `Math.min(targetValue, ...)` |
| "Entry point not found" | Missing Root.tsx path | Include `src/Root.tsx` in render command |
| Music too loud | Cruising volume too high | Reduce from 0.35 to 0.20 |
| Build error: duplicate frame in interpolate | Repeated value in input range | Bump duplicates by 1 (e.g., 15 -> 16) |
