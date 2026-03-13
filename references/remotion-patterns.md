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

`Root.tsx` must call `registerRoot()` at module level.

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

### Background Music Volume Curve

```tsx
const totalFrames = 2700; // Match composition
const musicVolume = interpolate(
  frame,
  [0, 60, totalFrames - 60, totalFrames],
  [0, 0.35, 0.35, 0],
  { extrapolateLeft: "clamp", extrapolateRight: "clamp" }
);

<Audio src={staticFile("music.mp3")} volume={musicVolume} />
```

- Fade in: 2 seconds (60 frames at 30fps)
- Cruising volume: 0.30-0.35 (ambient backdrop, not distracting)
- Fade out: 2 seconds
- For narration-heavy videos, reduce cruising to 0.15-0.20

### Voice-over Integration

Pipeline: Script -> ElevenLabs API -> MP3 -> Audio component

```tsx
<Audio src={staticFile("narration.mp3")} volume={1.0} />
<Audio src={staticFile("music.mp3")} volume={(f) =>
  // Lower music during narration
  interpolate(f, [0, FPS, totalFrames - FPS, totalFrames], [0, 0.15, 0.15, 0], clamp)
} />
```

Use `@remotion/captions` for synced subtitles with word-level highlighting.

---

## Safe Zones and Readability

- **Padding**: 50-80px from all edges
- **Text placement**: Keep in upper 85% of frame (bottom gets cropped in presentations/embeds)
- **Minimum font size**: 24px for anything that needs to be readable on Zoom screen shares
- **Center vertically**: Use `alignItems: "center"` for comparison layouts
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
import { Composition } from "remotion";
import { Video } from "./Video";

export const RemotionRoot: React.FC = () => {
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
