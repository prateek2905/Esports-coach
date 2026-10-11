# Handoff: Esports Coach Dashboard (Valorant)

## Overview
Single-page analytics dashboard for an esports coaching app. Dark, near-black canvas with orange/amber accents. Shows a Valorant player's stats, rank trend, role split, per-map win rate and an AI coach report. Heavy emphasis on motion: load sequence, scroll reveals, count-ups, chart draw-ins, cursor glow, tilt cards, typewriter report.

## About the Design Files
`reference/Coach Dashboard.dc.html` is a **design reference built in HTML**: a prototype of the intended look and behavior, not production code. Recreate it in the target project: **React + JavaScript (no TS) + Tailwind CSS + Framer Motion**. Do not ship the HTML. The vanilla-JS animation logic in the file's `componentDidMount` is a behavioral spec; port it to Framer Motion as mapped below.

## Fidelity
**High-fidelity.** Final colors, type, spacing and motion. Match closely.

## Suggested Structure
```
src/
  App.jsx
  components/
    Loader.jsx          // intro overlay
    TopBar.jsx          // logo, game tabs, sync status
    Ticker.jsx          // marquee
    Hero.jsx            // giant win rate + StatTile grid
    StatTile.jsx
    CountUp.jsx
    RankChart.jsx
    RoleDonut.jsx
    MapBars.jsx
    CoachReport.jsx
    Typewriter.jsx
    CursorGlow.jsx
    ScrollProgress.jsx
  data/mock.js
```
Max content width `1120px`, centered, page padding `0 56px` (use `px-6 md:px-14`). Page bg `#070707`, text `#F2EDE6`.

## Tailwind config (extend)
```js
colors: { bg:'#070707', panel:'#0d0c0a', line:'#1d1a16', line2:'#2a251f', ink:'#F2EDE6', mute:'#7d7366', body:'#cfc6ba',
  orange:'#EC7A2E', amber:'#FBBF24', tan:'#C48A5C', brown:'#6B5A4A', bar:'#6B3A1C', barDeep:'#2a1a10' },
fontFamily: { sans:['Instrument Sans'], mono:['JetBrains Mono'], display:['Big Shoulders Display'] },
keyframes: { mq:{to:{transform:'translateX(-50%)'}}, spin:{to:{transform:'rotate(405deg)'}}, pulse2:{'0%':{transform:'scale(1)',opacity:.7},'100%':{transform:'scale(3.2)',opacity:0}}, shim:{to:{backgroundPosition:'-200% 0'}}, blink:{'50%':{opacity:0}}, float:{'50%':{transform:'translateY(-14px) rotate(45deg)'}}, sweep:{'0%':{transform:'translateX(-120%)'},'100%':{transform:'translateX(120%)'}} }
```
Load Google Fonts: Instrument Sans 400/500/600, JetBrains Mono 400/500, Big Shoulders Display 800/900.

## Screen: Dashboard (single view)
Sections, top to bottom, each separated by `border-t 1px #1d1a16`:

### 1. Loader (fixed overlay, z-100, bg #070707)
Column, centered, gap 28. 34×34 square, 2px orange border, `spin` 1.1s cubic-bezier(.7,0,.3,1) infinite. Text `SYNCING 000%` JetBrains Mono 500 13px, tracking .2em, #7d7366, counts 0→100 over 1.9s. Progress bar 220×2 track #1d1a16, fill gradient 90deg #EC7A2E→#FBBF24. On finish: overlay clip-path wipes `inset(0 0 0 0)`→`inset(0 0 100% 0)` 0.9s cubic-bezier(.7,0,.2,1), unmounts; page reveals begin 350ms in.

### 2. Global fixed layers
- **Scroll progress**: top, 3px, width = scrollYProgress, gradient orange→amber, z-50 (`useScroll` + `scaleX`).
- **Cursor glow**: 560px circle, `radial-gradient(circle, rgba(236,122,46,.17), transparent 65%)`, follows mouse (`useMotionValue` + `useSpring`), pointer-events none, z-1.
- **Grid**: fixed, 64px lines `#ffffff08`, masked by `radial-gradient(ellipse at 50% 20%, #000, transparent 75%)`.
- **Ghost text** "VALORANT": absolute top 120px left -20px, Big Shoulders 900, 520px/0.8, transparent fill, `-webkit-text-stroke:1px #2a1a10`, parallax: `x = -scrollY*0.35`.

### 3. TopBar
Padding `32px 0`, bottom border, flex between. Left: logo (12px orange diamond, rotated 45°, `float` 3s infinite) + "COACH" 600 15px tracking .04em; tabs gap 28px, 14px: Valorant (active: #F2EDE6, 1px orange underline, pb 6), League of Legends, LiChess (#7d7366, hover #F2EDE6). Right: status dot 6px orange with pulse ring (`pulse2` 1.8s) + `[PLAYER#0000] · synced [DATE]` mono 12px #7d7366. Enter: y -30→0, fade, blur.

### 4. Ticker
Full-bleed (negative margin to page padding), border-bottom, py 14, mono 500 12px, tracking .14em, #7d7366. Content duplicated twice, translateX 0→-50% over 28s linear infinite. Items: `K/D 1.24 ▲ · WIN 54% ▲ · HS 23% — · ACS 241 ▲ · ADR 148 ▼ · KAST 71% ▲ · RR +38 ▲ · ◆`. Values: up = #EC7A2E, neutral/down = #F2EDE6, RR = #FBBF24.

### 5. Hero
Grid `repeat(auto-fit, minmax(320px,1fr))`, gap 64, padding `72px 0 64px`, align end.
- **Left**: label `WIN RATE · LAST 20` mono 12px tracking .2em #7d7366. Number `54` Big Shoulders 900, 230px/0.82, tracking -.02em, gradient text `180deg #FBBF24 → #EC7A2E 60% → #7a3a12`; `%` at 100px. Count-up 0→54 over 1.8s easeOutQuart. Delta `▲ +2% vs previous 20` mono 12px orange. Scroll: scale 1→1.25 (over first 600px) and y = -0.08×scroll.
- **Right**: 3×2 grid of StatTiles, gap 12, perspective 900px. Tile: padding 20, border 1px #1d1a16, bg #0d0c0a, no radius. Label 12px #7d7366; value 36px/500, tracking -.02em, margin `6px 0 4px`, counts up; delta mono 11px.
  | Label | Value | Delta |
  |---|---|---|
  | K/D Ratio | 1.24 (2 dec) | ▲ 0.06 orange |
  | Headshot % | 23% | — no change, mute |
  | Avg Combat Score | 241 | ▲ 11 orange |
  | Damage / Round | 148 | ▼ 3 mute |
  | KAST | 71% | ▲ 1% orange |
  | Matches (filled) | 12 | "this week"; bg/border #EC7A2E, text #070707, value 600 |
  Hover: 3D tilt `rotateY(x*18deg) rotateX(-y*18deg) translateZ(10px)` from cursor position, border → #EC7A2E; reset on leave (`whileHover` + mouse-derived motion values, spring).
  Stagger delay 80ms per tile.

### 6. Rank rating + Role distribution
Grid `repeat(auto-fit, minmax(340px,1fr))`, gap 48, padding `56px 0`.
- **Rank rating**: title 16px/600, subtitle "Last 10 matches" 12px mute. SVG viewBox `0 0 652 230`. Gridlines y=40/100/160 #1d1a16. Points: `20,150 88,120 156,135 224,95 292,110 360,70 428,85 496,50 564,58 632,35`. Line 2.5px #EC7A2E round joins/caps; area under it gradient #EC7A2E .35→0 down to y=190; dots r3.5 fill #070707 stroke orange 1.5; final point r5 #FBBF24 with pulsing ring and label `+38 RR` (mono 12px amber, above, right-aligned). X labels 1–10 at y=215, mono 11px mute. Animation on view: line draws via `pathLength` 0→1 over 2.2s ease cubic-bezier(.65,0,.35,1) (delay .2s); area fades in at 1.4s (1.2s); dots at 1.8s; end marker at 2.3s.
- **Role distribution**: 180px donut, r70, stroke 12, rotated -90°. Segments (dasharray length of 440 circumference): Duelist 40% #EC7A2E, Controller 25% #FBBF24, Initiator 20% #C48A5C, Sentinel 15% #6B5A4A; each with cumulative offset. Animate length 0→target, 1.2s `cubic-bezier(.16,1,.3,1)`, stagger 260ms from 300ms. Center: `12` 34px/500 counting up + "matches" 11px mute. Legend: 13px rows, 8px square swatch, name left, mono % right #7d7366, gap 12.

### 7. Win rate by map
Padding `56px 0`. Title + "Last 30 matches". Chart: 7 columns, gap 16, height 260, bottom border #2a251f, dashed gridlines (#1d1a16) at top/33%/66%. Values: 82, 64, 92, 52, 72, 47, 67 (% height). Normal bar: gradient `180deg #6B3A1C → #2a1a10`; hover `brightness(1.6)`. Best bar (Map 3, 92): gradient `#FBBF24 → #EC7A2E 40% → #8a4414`, glow `0 0 50px #EC7A2E55`, label "92 · BEST" amber. Value labels mono 12px mute, 24px above bar, left aligned. Map labels below (12px mute; Map 3 #F2EDE6). Animate: scaleY 0→1, origin bottom, 1.3s, stagger 120ms, spring-like overshoot `cubic-bezier(.34,1.4,.5,1)`, triggered when chart enters view (threshold .3).

### 8. AI Coach Report
Header: title "AI coach report" 16px/600 + `LIVE` badge (mono 500 10px, tracking .14em, 1px orange border, orange text, px8 py3); subtitle "Generated from your last [N] matches" 13px mute. Button "Regenerate ↻": bg #EC7A2E, text #070707, 600 13px, padding `13px 24px`, full-pill; hover bg #FBBF24; diagonal white shine sweeping across (`sweep` 2.6s infinite).
Three cards, grid `auto-fit minmax(260px,1fr)`, gap 20: border 1px #1d1a16, bg #0d0c0a, padding 28. Eyebrow mono 500 11px tracking .1em: `01 — STRENGTHS` (#EC7A2E), `02 — WEAKNESSES` (#FBBF24), `03 — NEXT STEPS` (#C48A5C). Body 15px/1.65 #cfc6ba, min-h 100.
Copy:
1. "[Placeholder: summary of what the player does well, e.g. opening duels, mechanical consistency.]"
2. "[Placeholder: patterns costing rounds, e.g. map-specific losses, late-round positioning.]"
3. "[Placeholder: 2–3 concrete drills or habits to focus on over the next week.]"
On view: typewriter, 22ms/char, cards staggered 500ms from 300ms, orange 7×15 block caret blinking, removed 1.2s after finish.
**Regenerate click**: text → "Analyzing matches…" with shimmer (gradient #7d7366/#FBBF24/#7d7366, 200% size, `shim` 1.2s linear); a 60px scan band (gradient transparent→#EC7A2E33→transparent, 1px orange bottom border) travels top -20%→110% over 1.5s; at 1.5s retype new text, cards staggered 350ms.

## Interactions & Behavior
- **Scroll reveal** (all `[data-rv]` blocks): initial opacity 0, y 60, scale .97, blur 8px → opacity 1, y 0, scale 1, blur 0; 1.1s `cubic-bezier(.16,1,.3,1)`; `whileInView`, `viewport={{ once:true, amount:.15 }}`. Header uses y -30; ticker fade only.
- Count-ups, chart draws, donut, bars, typewriter all start when their container enters view (`once`).
- Respect `prefers-reduced-motion`: skip loader, parallax, tilt; render final values.
- Responsive: hero and chart sections wrap via auto-fit grids; stat grid may drop to 2 cols below 480px; ghost text hidden below 768px.

## Framer Motion mapping
- Reveal: `motion.div` + `variants`, `whileInView`; stagger via `transition.delay` or parent `staggerChildren`.
- CountUp: `animate(0, to, { duration:1.8, ease:'easeOut', onUpdate })` inside `useInView`.
- Line: `motion.polyline` `initial={{pathLength:0}} animate={{pathLength:1}}`.
- Donut: `motion.circle` animating `strokeDasharray`.
- Bars: `motion.div` `style={{originY:1}}` `initial={{scaleY:0}}`.
- Scroll: `useScroll()` → `useTransform` for progress bar, ghost text x, hero scale/y.
- Cursor glow: `useMotionValue` x/y + `useSpring`.
- Loader exit: `AnimatePresence`, exit `clipPath`.
- Tilt: `useMotionValue` + `useTransform` to rotateX/Y with `useSpring`.

## State Management
- `loading` (bool): loader → false after 1.9s count completes.
- `activeGame` ('valorant' | 'lol' | 'lichess'): only Valorant designed; others are placeholders.
- `report` `{strengths, weaknesses, nextSteps}` + `status` ('idle' | 'analyzing' | 'typing'): Regenerate sets 'analyzing' → fetch → 'typing'.
- Data (replace mock): stats summary, last-10 RR series, role shares, per-map win rates (last 30), report. Player ID and sync date from the Riot-linked account.

## Design Tokens
- Colors: bg #070707 · panel #0d0c0a · line #1d1a16 · line-2 #2a251f · ink #F2EDE6 · mute #7d7366 · body #cfc6ba · orange #EC7A2E · amber #FBBF24 · tan #C48A5C · brown #6B5A4A · bar #6B3A1C · bar-deep #2a1a10 · hero low #7a3a12 · best-bar low #8a4414 · ghost stroke #2a1a10.
- Type: Instrument Sans (UI), JetBrains Mono (labels, numbers, deltas), Big Shoulders Display 900 (hero). Sizes: 11, 12, 13, 14, 15, 16, 34–36, 100/230 hero.
- Spacing: 12, 16, 20, 24, 28, 32, 48, 56, 64, 72.
- Radius: 0 everywhere except pill button (999px) and circles.
- Shadows: best bar glow `0 0 50px #EC7A2E55` only.
- Easing: `cubic-bezier(.16,1,.3,1)` (reveals), `cubic-bezier(.65,0,.35,1)` (line draw), `cubic-bezier(.34,1.4,.5,1)` (bars), `cubic-bezier(.7,0,.2,1)` (loader wipe).

## Assets
No images or icons. All graphics are CSS/SVG primitives. Fonts from Google Fonts.

## Files
- `reference/Coach Dashboard.dc.html`: full reference (markup, inline styles, animation logic in the script class at the bottom).
