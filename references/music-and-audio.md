# Music & Audio Guide

Audio transforms videos from "animated slideshows" into compelling content. Music is the #1 factor that changes viewer perception of quality.

---

## Music Sourcing

### Recommended Sources

| Source | Cost | License | Best For |
|--------|------|---------|----------|
| **Pixabay Music** (pixabay.com/music/) | Free | Royalty-free, no attribution | All use cases - recommended default |
| Artlist | $10/mo | Unlimited commercial | Professional/client work |
| Epidemic Sound | $13/mo | Unlimited commercial | High-volume content |
| Suno (AI) | Free tier | Generated | Experimental - quality varies |

**Do NOT rely on AI-generated ambient audio** (e.g., asking Claude to "create music"). The results are consistently too quiet, repetitive, and low-quality. Use a real music track from Pixabay.

### Music Selection by Creative Direction

| Creative Direction | Music Style | Search Terms |
|-------------------|-------------|-------------|
| **Minimal/Jobs** | Sparse piano, ambient pads, silence with beat drops | "minimal piano", "ambient corporate", "cinematic simple" |
| **Data-Rich/Bloomberg** | Subtle electronic, light synth | "electronic ambient", "tech corporate", "data visualization" |
| **Warm/Narrative** | Acoustic guitar, warm strings, soft indie | "acoustic warm", "inspiring indie", "storytelling" |
| **Terminal/Hacker** | Lo-fi electronic, synthwave, chiptune | "lo-fi coding", "synthwave", "cyberpunk ambient" |

### Track Selection Criteria

1. **Duration**: Pick a track close to your video length. If longer, use the ending section for a natural conclusion
2. **Energy arc**: Match the track's energy to your video's emotional arc (build → climax → resolve)
3. **No vocals**: Instrumental only - vocals compete with on-screen text
4. **Preview at 0.3 volume**: The track should still sound good quiet, since it plays behind visuals

---

## Integration Patterns

### Basic Background Music

```tsx
import { Audio, staticFile } from "remotion";

// In your Video component:
const frame = useCurrentFrame();
const { durationInFrames } = useVideoConfig();

const musicVolume = interpolate(
  frame,
  [0, 60, durationInFrames - 60, durationInFrames],
  [0, 0.30, 0.30, 0],
  { extrapolateLeft: "clamp", extrapolateRight: "clamp" }
);

<Audio src={staticFile("music.mp3")} volume={musicVolume} />
```

### Scene-Adaptive Volume

Lower music during data-heavy or text-heavy scenes so viewers can focus on reading:

```tsx
const musicVolume = (f: number) => {
  const fadeIn = interpolate(f, [0, 60], [0, 1], clamp);
  const fadeOut = interpolate(f, [totalFrames - 60, totalFrames], [1, 0], clamp);
  const envelope = Math.min(fadeIn, fadeOut);

  // Lower during data-heavy scenes
  const isDataScene = f >= S.data.start * FPS && f < (S.data.start + S.data.dur) * FPS;
  const cruising = isDataScene ? 0.15 : 0.30;

  return envelope * cruising;
};

<Audio src={staticFile("music.mp3")} volume={musicVolume} />
```

### Music Timing: Matching Track to Video

**Problem**: Your track is 3 minutes but your video is 40 seconds.

**Solutions**:
1. **Use the ending**: Start playback at `trackDuration - videoDuration` using `startFrom` prop
2. **Trim externally**: Use ffmpeg to extract the best 40-second segment:
   ```bash
   ffmpeg -i full-track.mp3 -ss 00:00:30 -t 00:00:40 -c copy music.mp3
   ```
3. **Use `startFrom` in Remotion**:
   ```tsx
   <Audio
     src={staticFile("music.mp3")}
     startFrom={90 * 30}  // Start from 90 seconds into the track (in frames)
     volume={musicVolume}
   />
   ```

---

## Voice-Over Pipeline

### Step 1: Generate Script

Extract narration text from scene content. Each scene gets 1-2 sentences:

```
Scene 1 (Hook, 4s): "Five experts. Forty seconds. One MP4."
Scene 2 (Gap, 6s): "The API tells you how. But who helps you think?"
...
```

**Pacing rule**: ~3 words per second for comfortable narration. A 40-second video = ~120 words max.

### Step 2: Generate Audio

**Option A - ElevenLabs** (recommended for quality):
```bash
# Using ElevenLabs API
curl -X POST "https://api.elevenlabs.io/v1/text-to-speech/<voice_id>" \
  -H "xi-api-key: $ELEVENLABS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"text": "Your narration text", "model_id": "eleven_multilingual_v2"}' \
  --output narration.mp3
```

**Option B - User records**: Ask user to record using any voice memo app, save as MP3.

### Step 3: Integrate with Music Ducking

When voice-over is present, music volume drops to 0.10-0.15:

```tsx
const hasNarration = true;

<Audio src={staticFile("narration.mp3")} volume={1.0} />
<Audio
  src={staticFile("music.mp3")}
  volume={(f) => {
    const fadeIn = interpolate(f, [0, 30], [0, 1], clamp);
    const fadeOut = interpolate(f, [totalFrames - 30, totalFrames], [1, 0], clamp);
    const envelope = Math.min(fadeIn, fadeOut);
    const cruising = hasNarration ? 0.12 : 0.30;
    return envelope * cruising;
  }}
/>
```

### Step 4: Subtitles (Optional)

Use `@remotion/captions` for word-level highlighting:

```bash
npm install @remotion/captions
```

Generate a transcript JSON from ElevenLabs or Whisper, then render synchronized captions.

---

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| AI-generated ambient audio | Use real tracks from Pixabay |
| Music too quiet (volume < 0.15) | Cruise at 0.25-0.35 for background, 0.10-0.15 only during narration |
| Music doesn't fade out | Always add 2-second fade-out before video ends |
| Track longer than video, cuts abruptly | Use `startFrom` to play the ending section, or trim with ffmpeg |
| Same volume throughout | Lower during data-heavy scenes, raise during emotional moments |
| Music with vocals | Use instrumental-only tracks |
| Forgetting `--muted` flag during dev | Render with `--muted` for faster iteration, add audio for final render |

---

## Audio Dependencies

Add to `package.json` if using audio:

```json
{
  "dependencies": {
    "@remotion/media-utils": "4.0.261"
  }
}
```

Place audio files in `public/` directory and reference with `staticFile()`.
