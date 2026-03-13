# Reusable Component Library

Production-tested components extracted from real Remotion projects. Copy these directly into new projects.

---

## Core Components

### FadeIn

Spring-animated fade with directional slide. The workhorse of entry animations.

```tsx
import React from "react";
import { useCurrentFrame, useVideoConfig, interpolate, spring } from "remotion";

const GENTLE = { damping: 20, mass: 1, stiffness: 100 };

interface FadeInProps {
  delay?: number;
  direction?: "up" | "down" | "left" | "right";
  children: React.ReactNode;
  style?: React.CSSProperties;
}

export const FadeIn: React.FC<FadeInProps> = ({
  delay = 0, direction = "up", children, style,
}) => {
  const frame = useCurrentFrame();
  const { fps } = useVideoConfig();
  const progress = spring({ frame: frame - delay, fps, config: GENTLE });
  const opacity = interpolate(frame - delay, [0, 30], [0, 1], {
    extrapolateLeft: "clamp", extrapolateRight: "clamp",
  });
  const offset = 40;
  const map = {
    up:    { x: 0, y: offset * (1 - progress) },
    down:  { x: 0, y: -offset * (1 - progress) },
    left:  { x: offset * (1 - progress), y: 0 },
    right: { x: -offset * (1 - progress), y: 0 },
  };
  const { x, y } = map[direction];
  return (
    <div style={{ opacity, transform: `translate(${x}px, ${y}px)`, ...style }}>
      {children}
    </div>
  );
};
```

### TypingText

Character-by-character typewriter with blinking cursor.

```tsx
import React from "react";
import { useCurrentFrame } from "remotion";

interface TypingTextProps {
  text: string;
  startFrame?: number;
  speed?: number;           // chars per frame (default 1.5)
  fontSize?: number;
  fontFamily?: string;
  color?: string;
  cursorColor?: string;
  showCursor?: boolean;
}

export const TypingText: React.FC<TypingTextProps> = ({
  text, startFrame = 0, speed = 1.5, fontSize = 28,
  fontFamily = "monospace", color = "#F0F0F0",
  cursorColor = "#4AB0A0", showCursor = true,
}) => {
  const frame = useCurrentFrame();
  const elapsed = Math.max(0, frame - startFrame);
  const charsToShow = Math.min(Math.floor(elapsed * speed), text.length);
  const visibleText = text.slice(0, charsToShow);
  const isComplete = charsToShow >= text.length;
  const cursorOpacity = isComplete ? (frame % 30 < 15 ? 1 : 0) : 1;

  return (
    <div style={{ fontFamily, fontSize, color, lineHeight: 1.5, whiteSpace: "pre-wrap" }}>
      {visibleText}
      {showCursor && (
        <span style={{ opacity: cursorOpacity, color: cursorColor, fontWeight: "bold" }}>|</span>
      )}
    </div>
  );
};
```

### CodeBlock

Terminal-style code display with line-by-line reveal and macOS dots.

```tsx
import React from "react";
import { useCurrentFrame, interpolate } from "remotion";

interface CodeBlockProps {
  lines: string[];
  startFrame?: number;
  lineDelay?: number;       // frames between each line (default 8)
  fontSize?: number;
  width?: number | string;
  bgColor?: string;
  textColor?: string;
}

export const CodeBlock: React.FC<CodeBlockProps> = ({
  lines, startFrame = 0, lineDelay = 8, fontSize = 20,
  width = "auto", bgColor = "#2B2B3B", textColor = "#E0E0E8",
}) => {
  const frame = useCurrentFrame();
  const elapsed = Math.max(0, frame - startFrame);
  const clamp = { extrapolateLeft: "clamp" as const, extrapolateRight: "clamp" as const };

  return (
    <div style={{ backgroundColor: bgColor, borderRadius: 12, padding: "24px 32px", width, overflow: "hidden" }}>
      <div style={{ display: "flex", gap: 8, marginBottom: 16 }}>
        <div style={{ width: 12, height: 12, borderRadius: "50%", backgroundColor: "#FF5F56" }} />
        <div style={{ width: 12, height: 12, borderRadius: "50%", backgroundColor: "#FFBD2E" }} />
        <div style={{ width: 12, height: 12, borderRadius: "50%", backgroundColor: "#27C93F" }} />
      </div>
      {lines.map((line, i) => {
        const lineStart = i * lineDelay;
        const opacity = interpolate(elapsed - lineStart, [0, 10], [0, 1], clamp);
        return (
          <div key={i} style={{
            fontFamily: "monospace", fontSize, color: textColor,
            lineHeight: 1.6, opacity, whiteSpace: "pre",
          }}>
            {line}
          </div>
        );
      })}
    </div>
  );
};
```

### CrossDissolve

Scene-level fade envelope for smooth transitions.

```tsx
import React from "react";
import { useCurrentFrame, useVideoConfig, interpolate, AbsoluteFill } from "remotion";

interface CrossDissolveProps {
  children: React.ReactNode;
  envelopeFrames?: number;  // default 8
}

export const CrossDissolve: React.FC<CrossDissolveProps> = ({
  children, envelopeFrames = 8,
}) => {
  const frame = useCurrentFrame();
  const { durationInFrames } = useVideoConfig();
  const opacity = interpolate(
    frame,
    [0, envelopeFrames, durationInFrames - envelopeFrames, durationInFrames],
    [0, 1, 1, 0],
    { extrapolateLeft: "clamp", extrapolateRight: "clamp" }
  );
  return <AbsoluteFill style={{ opacity }}>{children}</AbsoluteFill>;
};
```

---

## Display Components

### TerminalWindow

Chrome-style terminal frame with title bar.

```tsx
import React from "react";

interface TerminalWindowProps {
  title?: string;
  children: React.ReactNode;
  width?: number | string;
  bgColor?: string;
}

export const TerminalWindow: React.FC<TerminalWindowProps> = ({
  title = "Terminal", children, width = "100%", bgColor = "#0D1117",
}) => {
  return (
    <div style={{
      width, borderRadius: 12, overflow: "hidden",
      border: "1px solid #30363D", backgroundColor: bgColor,
    }}>
      <div style={{
        display: "flex", alignItems: "center", padding: "10px 16px",
        backgroundColor: "#161B22", borderBottom: "1px solid #30363D",
      }}>
        <div style={{ display: "flex", gap: 8 }}>
          <div style={{ width: 12, height: 12, borderRadius: "50%", backgroundColor: "#FF5F56" }} />
          <div style={{ width: 12, height: 12, borderRadius: "50%", backgroundColor: "#FFBD2E" }} />
          <div style={{ width: 12, height: 12, borderRadius: "50%", backgroundColor: "#27C93F" }} />
        </div>
        <div style={{
          flex: 1, textAlign: "center", fontFamily: "monospace",
          fontSize: 13, color: "#8B949E",
        }}>
          {title}
        </div>
      </div>
      <div style={{ padding: "20px 24px" }}>
        {children}
      </div>
    </div>
  );
};
```

### BrowserMockup

Browser chrome with URL bar for product demos.

```tsx
import React from "react";

interface BrowserMockupProps {
  url?: string;
  children: React.ReactNode;
  width?: number | string;
}

export const BrowserMockup: React.FC<BrowserMockupProps> = ({
  url = "https://example.com", children, width = "100%",
}) => {
  return (
    <div style={{
      width, borderRadius: 12, overflow: "hidden",
      border: "1px solid #30363D", backgroundColor: "#FFFFFF",
      boxShadow: "0 8px 32px rgba(0,0,0,0.3)",
    }}>
      <div style={{
        display: "flex", alignItems: "center", padding: "8px 16px",
        backgroundColor: "#F6F8FA", borderBottom: "1px solid #D1D5DB", gap: 12,
      }}>
        <div style={{ display: "flex", gap: 6 }}>
          <div style={{ width: 12, height: 12, borderRadius: "50%", backgroundColor: "#FF5F56" }} />
          <div style={{ width: 12, height: 12, borderRadius: "50%", backgroundColor: "#FFBD2E" }} />
          <div style={{ width: 12, height: 12, borderRadius: "50%", backgroundColor: "#27C93F" }} />
        </div>
        <div style={{
          flex: 1, backgroundColor: "#FFFFFF", borderRadius: 6,
          padding: "4px 12px", fontSize: 13, color: "#6B7280",
          fontFamily: "monospace", border: "1px solid #E5E7EB",
        }}>
          {url}
        </div>
      </div>
      <div style={{ position: "relative" }}>
        {children}
      </div>
    </div>
  );
};
```

### PersonaCard

Persona presentation card with spring animation and color-coded border.

```tsx
import React from "react";
import { useCurrentFrame, useVideoConfig, spring, interpolate } from "remotion";

const BOUNCE = { damping: 12, mass: 0.5, stiffness: 200 };
const clamp = { extrapolateLeft: "clamp" as const, extrapolateRight: "clamp" as const };

interface PersonaCardProps {
  title: string;
  description: string;
  quote?: string;
  change?: string;
  color: string;
  delay: number;
  bgColor?: string;
}

export const PersonaCard: React.FC<PersonaCardProps> = ({
  title, description, quote, change, color, delay, bgColor = "#0F3460",
}) => {
  const frame = useCurrentFrame();
  const { fps } = useVideoConfig();
  const scale = spring({ frame: frame - delay, fps, config: BOUNCE });
  const descOpacity = interpolate(frame - delay, [15, 30], [0, 1], clamp);
  const quoteOpacity = interpolate(frame - delay, [25, 40], [0, 1], clamp);
  const changeOpacity = interpolate(frame - delay, [35, 50], [0, 1], clamp);

  return (
    <div style={{
      flex: 1, backgroundColor: bgColor, borderRadius: 16,
      padding: "28px 24px", borderLeft: `4px solid ${color}`,
      transform: `scale(${scale})`, transformOrigin: "top center",
      display: "flex", flexDirection: "column", gap: 12,
    }}>
      <div style={{ fontSize: 24, fontWeight: 700, color }}>{title}</div>
      <div style={{ fontSize: 18, color: "#A0A0B0", lineHeight: 1.4, opacity: descOpacity }}>
        {description}
      </div>
      {quote && (
        <div style={{
          fontSize: 16, color: "#F0F0F0", fontStyle: "italic",
          lineHeight: 1.4, opacity: quoteOpacity, marginTop: 8,
          borderLeft: `2px solid ${color}40`, paddingLeft: 12,
        }}>
          "{quote}"
        </div>
      )}
      {change && (
        <div style={{
          fontSize: 16, color, fontWeight: 600,
          opacity: changeOpacity, marginTop: "auto",
        }}>
          {"\u2192"} {change}
        </div>
      )}
    </div>
  );
};
```

### ChatBubble

Telegram/Slack-style chat message with avatar.

```tsx
import React from "react";
import { useCurrentFrame, interpolate, Img, staticFile } from "remotion";

const clamp = { extrapolateLeft: "clamp" as const, extrapolateRight: "clamp" as const };

interface ChatBubbleProps {
  name: string;
  text: string;
  color: string;
  delay: number;
  isRight?: boolean;
  avatar?: string;       // Filename in public/ folder
  bgColor?: string;
}

export const ChatBubble: React.FC<ChatBubbleProps> = ({
  name, text, color, delay, isRight, avatar, bgColor = "#0F3460",
}) => {
  const frame = useCurrentFrame();
  const opacity = interpolate(frame - delay, [0, 12], [0, 1], clamp);
  const translateY = interpolate(frame - delay, [0, 12], [20, 0], clamp);

  const avatarEl = avatar ? (
    <div style={{
      flexShrink: 0, width: 44, height: 44, borderRadius: "50%",
      overflow: "hidden", border: `2px solid ${color}`,
    }}>
      <Img src={staticFile(avatar)} style={{ width: "100%", height: "100%", objectFit: "cover" }} />
    </div>
  ) : null;

  return (
    <div style={{
      opacity, transform: `translateY(${translateY}px)`,
      display: "flex", alignItems: "flex-start", gap: 10,
      maxWidth: "80%", alignSelf: isRight ? "flex-end" : "flex-start",
    }}>
      {!isRight && avatarEl}
      <div style={{ display: "flex", flexDirection: "column", alignItems: isRight ? "flex-end" : "flex-start" }}>
        <div style={{ fontSize: 16, color, fontWeight: 700, marginBottom: 4 }}>{name}</div>
        <div style={{
          backgroundColor: bgColor, padding: "12px 18px",
          borderRadius: isRight ? "16px 16px 4px 16px" : "16px 16px 16px 4px",
          borderLeft: isRight ? "none" : `3px solid ${color}`,
          borderRight: isRight ? `3px solid ${color}` : "none",
        }}>
          <div style={{ fontSize: 20, color: "#F0F0F0", lineHeight: 1.4 }}>{text}</div>
        </div>
      </div>
      {isRight && avatarEl}
    </div>
  );
};
```

### FileTree

Animated recursive folder structure.

```tsx
import React from "react";
import { useCurrentFrame, interpolate } from "remotion";

const clamp = { extrapolateLeft: "clamp" as const, extrapolateRight: "clamp" as const };

export interface TreeNode {
  name: string;
  type: "folder" | "file";
  children?: TreeNode[];
}

function countNodes(node: TreeNode): number {
  let count = 1;
  if (node.children) {
    for (const child of node.children) count += countNodes(child);
  }
  return count;
}

const TreeItem: React.FC<{
  node: TreeNode; depth: number; index: number;
  startFrame: number; expandDelay: number;
  folderColor?: string; fileColor?: string;
}> = ({ node, depth, index, startFrame, expandDelay, folderColor = "#4AB0A0", fileColor = "#A0A0B0" }) => {
  const frame = useCurrentFrame();
  const itemStart = startFrame + index * expandDelay;
  const opacity = interpolate(frame - itemStart, [0, 12], [0, 1], clamp);
  const translateX = interpolate(frame - itemStart, [0, 12], [20, 0], clamp);

  let childIndex = index + 1;
  return (
    <div style={{ opacity, transform: `translateX(${translateX}px)` }}>
      <div style={{
        paddingLeft: depth * 24, fontFamily: "monospace", fontSize: 20,
        color: node.type === "folder" ? folderColor : fileColor, lineHeight: 2,
      }}>
        {node.type === "folder" ? "\uD83D\uDCC1" : "\uD83D\uDCC4"} {node.name}
      </div>
      {node.children?.map((child) => {
        const el = (
          <TreeItem key={child.name} node={child} depth={depth + 1} index={childIndex}
            startFrame={startFrame} expandDelay={expandDelay}
            folderColor={folderColor} fileColor={fileColor} />
        );
        childIndex += countNodes(child);
        return el;
      })}
    </div>
  );
};

interface FileTreeProps {
  tree: TreeNode;
  startFrame?: number;
  expandDelay?: number;
}

export const FileTree: React.FC<FileTreeProps> = ({ tree, startFrame = 0, expandDelay = 8 }) => {
  return (
    <div style={{ padding: 16 }}>
      <TreeItem node={tree} depth={0} index={0} startFrame={startFrame} expandDelay={expandDelay} />
    </div>
  );
};
```

---

## Theme Templates

### Dark Theme (Tech / Engineering)

```typescript
export const COLORS = {
  bg: "#1A1A2E",
  bgLight: "#16213E",
  bgPanel: "#0F3460",
  teal: "#4AB0A0",
  coral: "#E86B6B",
  cream: "#FAF7F2",
  white: "#FFFFFF",
  textPrimary: "#F0F0F0",
  textSecondary: "#A0A0B0",
  textMuted: "#606080",
  codeBg: "#2B2B3B",
  codeText: "#E0E0E8",
  cursor: "#4AB0A0",
};
```

### Light Theme (B2B / Marketing)

```typescript
export const COLORS = {
  bg: "#F8F6F3",
  bgLight: "#FFFFFF",
  bgPanel: "#F0EDE8",
  accent: "#2563EB",
  green: "#10B981",
  red: "#EF4444",
  dark: "#1A1A2E",
  textPrimary: "#1A1A2E",
  textSecondary: "#6B7280",
  textMuted: "#9CA3AF",
  codeBg: "#1E293B",
  codeText: "#E0E0E8",
};
```

### Font Setup

```typescript
import { loadFont as loadInter } from "@remotion/google-fonts/Inter";
import { loadFont as loadJetBrainsMono } from "@remotion/google-fonts/JetBrainsMono";

const { fontFamily: interFamily } = loadInter();
const { fontFamily: monoFamily } = loadJetBrainsMono();

export const FONTS = {
  heading: interFamily,
  body: interFamily,
  mono: monoFamily,
};

export const SIZES = {
  h1: 64, h2: 48, h3: 36,
  body: 28, small: 22, code: 20, label: 18,
};
```
