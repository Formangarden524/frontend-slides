# Animation Patterns Reference

Use this reference when generating presentations. Match animations to the intended feeling.

## Effect-to-Feeling Guide

| Feeling | Animations | Visual Cues |
|---------|-----------|-------------|
| **Dramatic / Cinematic** | Slow fade-ins (1-1.5s), large scale transitions (0.9 to 1), parallax scrolling | Dark backgrounds, spotlight effects, full-bleed images |
| **Techy / Futuristic** | Neon glow (box-shadow), glitch/scramble text, grid reveals | Particle systems (canvas), grid patterns, monospace accents, cyan/magenta/electric blue |
| **Playful / Friendly** | Bouncy easing (spring physics), floating/bobbing | Rounded corners, pastel/bright colors, hand-drawn elements |
| **Professional / Corporate** | Subtle fast animations (200-300ms), clean slides | Navy/slate/charcoal, precise spacing, data visualization focus |
| **Calm / Minimal** | Very slow subtle motion, gentle fades | High whitespace, muted palette, serif typography, generous padding |
| **Editorial / Magazine** | Staggered text reveals, image-text interplay | Strong type hierarchy, pull quotes, grid-breaking layouts, serif headlines + sans body |
| **Scholarly / East Asian** | Ink-wash unfolds, gentle brush strokes, contemplative pacing | Generous whitespace, vertical text, seal stamps, paper textures |

## Entrance Animations

```css
/* Fade + Slide Up (most versatile) */
.reveal {
    opacity: 0;
    transform: translateY(30px);
    transition: opacity 0.6s var(--ease-out-expo),
                transform 0.6s var(--ease-out-expo);
}
.visible .reveal {
    opacity: 1;
    transform: translateY(0);
}

/* Scale In */
.reveal-scale {
    opacity: 0;
    transform: scale(0.9);
    transition: opacity 0.6s, transform 0.6s var(--ease-out-expo);
}

/* Slide from Left */
.reveal-left {
    opacity: 0;
    transform: translateX(-50px);
    transition: opacity 0.6s, transform 0.6s var(--ease-out-expo);
}

/* Blur In */
.reveal-blur {
    opacity: 0;
    filter: blur(10px);
    transition: opacity 0.8s, filter 0.8s var(--ease-out-expo);
}
```

## Song Elegance Animations（宋韵专用）

**Feeling:** Slow, contemplative, ink-wash unfolding — like watching ink spread on xuan paper

```css
/* 墨染展开 - Ink wash spread effect */
@keyframes ink-spread {
    from {
        opacity: 0;
        filter: blur(8px);
        transform: scale(0.98);
    }
    to {
        opacity: 1;
        filter: blur(0);
        transform: scale(1);
    }
}

.song-ink-reveal {
    animation: ink-spread 1.2s var(--ease-out-expo) forwards;
}

/* 淡墨渐显 - Gentle ink fade (slower, more contemplative) */
@keyframes ink-fade {
    from {
        opacity: 0;
        transform: translateY(20px);
    }
    to {
        opacity: 1;
        transform: translateY(0);
    }
}

.song-fade-in {
    opacity: 0;
    animation: ink-fade 1s var(--ease-out-expo) forwards;
}

/* 竖排文字逐字显现 - Vertical text character reveal */
.song-vertical-reveal span {
    opacity: 0;
    display: inline-block;
    animation: ink-fade 0.6s ease forwards;
}

.song-vertical-reveal span:nth-child(1) { animation-delay: 0.1s; }
.song-vertical-reveal span:nth-child(2) { animation-delay: 0.2s; }
.song-vertical-reveal span:nth-child(3) { animation-delay: 0.3s; }
.song-vertical-reveal span:nth-child(4) { animation-delay: 0.4s; }
.song-vertical-reveal span:nth-child(5) { animation-delay: 0.5s; }

/* 印章盖印 - Seal stamp effect */
@keyframes seal-stamp {
    0% {
        opacity: 0;
        transform: rotate(-8deg) scale(1.1);
    }
    50% {
        opacity: 0.8;
        transform: rotate(-2deg) scale(0.98);
    }
    100% {
        opacity: 0.7;
        transform: rotate(-3deg) scale(1);
    }
}

.song-seal-reveal {
    animation: seal-stamp 0.8s var(--ease-out-expo) forwards;
}

/* 细线延展 - Line extension */
@keyframes line-extend {
    from {
        transform: scaleX(0);
    }
    to {
        transform: scaleX(1);
    }
}

.song-line-reveal {
    transform-origin: left;
    animation: line-extend 1s var(--ease-out-expo) forwards;
}

/* 水墨晕染背景 - Ink wash background pulse */
@keyframes ink-wash {
    0%, 100% {
        opacity: 0.03;
        transform: scale(1);
    }
    50% {
        opacity: 0.06;
        transform: scale(1.05);
    }
}

.song-ink-bg {
    animation: ink-wash 8s ease-in-out infinite;
}
```

## Background Effects

```css
/* Gradient Mesh — layered radial gradients for depth */
.gradient-bg {
    background:
        radial-gradient(ellipse at 20% 80%, rgba(120, 0, 255, 0.3) 0%, transparent 50%),
        radial-gradient(ellipse at 80% 20%, rgba(0, 255, 200, 0.2) 0%, transparent 50%),
        var(--bg-primary);
}

/* Noise Texture — inline SVG for grain */
.noise-bg {
    background-image: url("data:image/svg+xml,..."); /* Inline SVG noise */
}

/* Grid Pattern — subtle structural lines */
.grid-bg {
    background-image:
        linear-gradient(rgba(255,255,255,0.03) 1px, transparent 1px),
        linear-gradient(90deg, rgba(255,255,255,0.03) 1px, transparent 1px);
    background-size: 50px 50px;
}

/* Song Elegance — Ink wash paper texture */
.song-paper-bg {
    background: 
        radial-gradient(ellipse at 15% 85%, rgba(74, 85, 104, 0.04) 0%, transparent 45%),
        radial-gradient(ellipse at 85% 15%, rgba(166, 93, 77, 0.03) 0%, transparent 40%),
        radial-gradient(ellipse at 50% 50%, rgba(125, 139, 125, 0.02) 0%, transparent 50%),
        var(--bg-primary);
}

/* Song Elegance — Grid guide lines */
.song-grid-bg {
    background-image:
        linear-gradient(rgba(74, 85, 104, 0.04) 1px, transparent 1px),
        linear-gradient(90deg, rgba(74, 85, 104, 0.04) 1px, transparent 1px);
    background-size: 60px 60px;
}
```

## Interactive Effects

```javascript
/* 3D Tilt on Hover — adds depth to cards/panels */
class TiltEffect {
    constructor(element) {
        this.element = element;
        this.element.style.transformStyle = 'preserve-3d';
        this.element.style.perspective = '1000px';

        this.element.addEventListener('mousemove', (e) => {
            const rect = this.element.getBoundingClientRect();
            const x = (e.clientX - rect.left) / rect.width - 0.5;
            const y = (e.clientY - rect.top) / rect.height - 0.5;
            this.element.style.transform = `rotateY(${x * 10}deg) rotateX(${-y * 10}deg)`;
        });

        this.element.addEventListener('mouseleave', () => {
            this.element.style.transform = 'rotateY(0) rotateX(0)';
        });
    }
}
```

## Troubleshooting

| Problem | Fix |
|---------|-----|
| Fonts not loading | Check Fontshare/Google Fonts URL; ensure font names match in CSS |
| Animations not triggering | Verify Intersection Observer is running; check `.visible` class is being added |
| Scroll snap not working | Ensure `scroll-snap-type: y mandatory` on html; each slide needs `scroll-snap-align: start` |
| Mobile issues | Disable heavy effects at 768px breakpoint; test touch events; reduce particle count |
| Performance issues | Use `will-change` sparingly; prefer `transform`/`opacity` animations; throttle scroll handlers |
| Song Elegance animations too slow | Reduce `animation-delay` between characters; use `prefers-reduced-motion` for accessibility |
