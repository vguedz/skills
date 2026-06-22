# Examples — Copy-Safe Code Patterns

## Button with Emil's Easing

```tsx
<button className="button">
  Get Started
  <span className="button-icon">→</span>
</button>
```

```css
.button {
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.75rem 1.5rem;
  border-radius: 999px;
  background: #000;
  color: #fff;
  border: none;
  cursor: pointer;
  transition: transform 160ms cubic-bezier(0.23, 1, 0.32, 1);
}
.button:active {
  transform: scale(0.97);
}
.button:hover .button-icon {
  transform: translateX(2px) translateY(-1px);
  transition: transform 200ms cubic-bezier(0.23, 1, 0.32, 1);
}
```

## Origin-Aware Popover

```css
.popover-content {
  transform-origin: var(--radix-popover-content-transform-origin);
  transition:
    transform 200ms cubic-bezier(0.23, 1, 0.32, 1),
    opacity 200ms cubic-bezier(0.23, 1, 0.32, 1);
}
.popover-content[data-state="open"] {
  transform: scale(1);
  opacity: 1;
}
.popover-content[data-state="closed"] {
  transform: scale(0.95);
  opacity: 0;
}
```

## Tooltip with Skip-Delay

```css
.tooltip {
  transition:
    transform 125ms cubic-bezier(0.23, 1, 0.32, 1),
    opacity 125ms cubic-bezier(0.23, 1, 0.32, 1);
  transform-origin: var(--transform-origin);
}
.tooltip[data-starting-style],
.tooltip[data-ending-style] {
  opacity: 0;
  transform: scale(0.97);
}
.tooltip[data-instant] {
  transition-duration: 0ms;
}
```

## Hold-to-Delete Button

```css
.delete-button {
  position: relative;
  overflow: hidden;
  transition: transform 160ms cubic-bezier(0.23, 1, 0.32, 1);
}
.delete-button:active {
  transform: scale(0.97);
}

.delete-overlay {
  position: absolute;
  inset: 0;
  background: red;
  clip-path: inset(0 100% 0 0);
  transition: clip-path 200ms cubic-bezier(0.23, 1, 0.32, 1);
}
.delete-button:active .delete-overlay {
  clip-path: inset(0 0 0 0);
  transition: clip-path 2s linear;
}
```

## Scroll-Reveal Stagger with Motion

```tsx
"use client";
import { motion, useReducedMotion } from "motion/react";

export function RevealStagger({ items }: { items: React.ReactNode[] }) {
  const reduce = useReducedMotion();

  return (
    <ul className="grid gap-6">
      {items.map((item, i) => (
        <motion.li
          key={i}
          initial={reduce ? false : { opacity: 0, y: 24 }}
          whileInView={{ opacity: 1, y: 0 }}
          viewport={{ once: true, amount: 0.3 }}
          transition={{
            duration: 0.6,
            delay: i * 0.06,
            ease: [0.23, 1, 0.32, 1],
          }}
        >
          {item}
        </motion.li>
      ))}
    </ul>
  );
}
```

## GSAP Sticky-Stack

```tsx
"use client";
import { useRef, useEffect } from "react";
import { gsap } from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";
import { useReducedMotion } from "motion/react";

gsap.registerPlugin(ScrollTrigger);

export function StickyStack({ cards }: { cards: React.ReactNode[] }) {
  const ref = useRef<HTMLDivElement>(null);
  const reduce = useReducedMotion();

  useEffect(() => {
    if (reduce || !ref.current) return;
    const ctx = gsap.context(() => {
      const cardEls = gsap.utils.toArray<HTMLElement>(".stack-card");
      cardEls.forEach((card, i) => {
        if (i === cardEls.length - 1) return;
        ScrollTrigger.create({
          trigger: card,
          start: "top top",
          endTrigger: cardEls[cardEls.length - 1],
          end: "top top",
          pin: true,
          pinSpacing: false,
        });
        gsap.to(card, {
          scale: 0.92,
          opacity: 0.55,
          ease: "none",
          scrollTrigger: {
            trigger: cardEls[i + 1],
            start: "top bottom",
            end: "top top",
            scrub: true,
          },
        });
      });
    }, ref);
    return () => ctx.revert();
  }, [reduce]);

  return (
    <div ref={ref} className="relative">
      {cards.map((card, i) => (
        <div key={i} className="stack-card sticky top-0 min-h-[100dvh] flex items-center justify-center">
          {card}
        </div>
      ))}
    </div>
  );
}
```

## GSAP Horizontal-Pan

```tsx
"use client";
import { useRef, useEffect } from "react";
import { gsap } from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";
import { useReducedMotion } from "motion/react";

gsap.registerPlugin(ScrollTrigger);

export function HorizontalPan({ children }: { children: React.ReactNode }) {
  const wrap = useRef<HTMLDivElement>(null);
  const track = useRef<HTMLDivElement>(null);
  const reduce = useReducedMotion();

  useEffect(() => {
    if (reduce || !wrap.current || !track.current) return;
    const ctx = gsap.context(() => {
      const distance = track.current!.scrollWidth - window.innerWidth;
      gsap.to(track.current, {
        x: -distance,
        ease: "none",
        scrollTrigger: {
          trigger: wrap.current,
          start: "top top",
          end: () => `+=${distance}`,
          pin: true,
          scrub: 1,
          invalidateOnRefresh: true,
        },
      });
    }, wrap);
    return () => ctx.revert();
  }, [reduce]);

  return (
    <section ref={wrap} className="relative overflow-hidden">
      <div ref={track} className="flex h-[100dvh] items-center">
        {children}
      </div>
    </section>
  );
}
```

## Double-Bezel Premium Card

```tsx
export function PremiumCard({ children }: { children: React.ReactNode }) {
  return (
    <div className="bg-black/5 dark:bg-white/5 p-1.5 rounded-[2rem] ring-1 ring-black/5 dark:ring-white/10">
      <div className="bg-white dark:bg-zinc-900 rounded-[calc(2rem-0.375rem)] shadow-[inset_0_1px_1px_rgba(255,255,255,0.15)]">
        {children}
      </div>
    </div>
  );
}
```

## Button-In-Button CTA

```tsx
export function PremiumCTA({ children }: { children: React.ReactNode }) {
  return (
    <button className="group inline-flex items-center gap-3 px-6 py-3 rounded-full bg-black text-white dark:bg-white dark:text-black transition-transform duration-160 ease-[cubic-bezier(0.23,1,0.32,1)] active:scale-[0.97]">
      <span>{children}</span>
      <span className="w-8 h-8 rounded-full bg-black/10 dark:bg-white/10 flex items-center justify-center transition-transform duration-200 ease-[cubic-bezier(0.23,1,0.32,1)] group-hover:translate-x-0.5 group-hover:-translate-y-[1px] group-hover:scale-105">
        →
      </span>
    </button>
  );
}
```
