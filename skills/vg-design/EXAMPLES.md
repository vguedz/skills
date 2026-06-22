# Examples — Copy-Safe Code Patterns

> **Before copying:** Verify the imported libraries exist in `package.json`. Motion, GSAP, and icon libraries must be installed. Never assume availability.
>
> Colors use off-white (`neutral-50`/`neutral-950`) and OKLCH values, never `#fff` or `#000`.

## Button with Emil's Easing

```tsx
<button className="group inline-flex items-center gap-2 px-6 py-3 rounded-full bg-zinc-900 text-zinc-50 dark:bg-zinc-50 dark:text-zinc-900 border-0 cursor-pointer transition-transform duration-[160ms] ease-[cubic-bezier(0.23,1,0.32,1)] active:scale-[0.97]">
  Get Started
  <span className="w-8 h-8 rounded-full bg-zinc-50/10 dark:bg-zinc-900/10 flex items-center justify-center transition-transform duration-200 ease-[cubic-bezier(0.23,1,0.32,1)] group-hover:translate-x-0.5 group-hover:-translate-y-[1px] group-hover:scale-105">
    →
  </span>
</button>
```

## Origin-Aware Popover

```css
.popover-content {
  transform-origin: var(--radix-popover-content-transform-origin);
  transition:
    transform 200ms cubic-bezier(0.23, 1, 0.32, 1),
    opacity 200ms cubic-bezier(0.23, 1, 0.32, 1);
}
.popover-content[data-state="open"]  { transform: scale(1); opacity: 1; }
.popover-content[data-state="closed"] { transform: scale(0.95); opacity: 0; }
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
.tooltip[data-ending-style] { opacity: 0; transform: scale(0.97); }
.tooltip[data-instant] { transition-duration: 0ms; }
```

## Hold-to-Delete Button

```css
.delete-button {
  position: relative;
  overflow: hidden;
  transition: transform 160ms cubic-bezier(0.23, 1, 0.32, 1);
}
.delete-button:active { transform: scale(0.97); }

.delete-overlay {
  position: absolute;
  inset: 0;
  background: oklch(0.55 0.18 25); /* error red */
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
          initial={reduce ? false : { opacity: 0, transform: 'translateY(24px)' }}
          whileInView={{ opacity: 1, transform: 'translateY(0px)' }}
          viewport={{ once: true, amount: 0.3 }}
          transition={{
            duration: 0.3,
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

gsap.registerPlugin(ScrollTrigger);

export function StickyStack({ cards }: { cards: React.ReactNode[] }) {
  const ref = useRef<HTMLDivElement>(null);

  useEffect(() => {
    const reduce = window.matchMedia("(prefers-reduced-motion: reduce)").matches;
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
          transform: "scale(0.92)",
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
  }, []);

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

gsap.registerPlugin(ScrollTrigger);

export function HorizontalPan({ children }: { children: React.ReactNode }) {
  const wrap = useRef<HTMLDivElement>(null);
  const track = useRef<HTMLDivElement>(null);

  useEffect(() => {
    const reduce = window.matchMedia("(prefers-reduced-motion: reduce)").matches;
    if (reduce || !wrap.current || !track.current) return;
    const ctx = gsap.context(() => {
      const distance = track.current!.scrollWidth - window.innerWidth;
      gsap.to(track.current, {
        transform: `translate3d(${-distance}px, 0, 0)`,
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
  }, []);

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

Use only for premium showcase surfaces (pricing, product, hero-level features). Max 3 per page. Only when VARIANCE ≥ 6 AND DENSITY ≤ 5.

```tsx
export function PremiumCard({ children }: { children: React.ReactNode }) {
  return (
    <div className="bg-neutral-950/5 dark:bg-neutral-50/5 p-1.5 rounded-[2rem] ring-1 ring-neutral-950/5 dark:ring-neutral-50/10">
      <div className="bg-neutral-50 dark:bg-neutral-900 rounded-[calc(2rem-0.375rem)] shadow-[inset_0_1px_1px_rgba(255,255,255,0.15)]">
        {children}
      </div>
    </div>
  );
}
```
