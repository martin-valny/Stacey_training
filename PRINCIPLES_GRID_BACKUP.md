# Backup: Original "Four Principles. One System." Grid Layout

This documents the original 2-column grid layout for the principles section before it was converted to a 3D scroll carousel. Use this if you want to revert back to the original design.

---

## HTML Structure (Original)

```html
<!-- MY APPROACH — PRINCIPLES GRID -->
<section class="principles" id="approach">
  <div class="section-inner">
    <div class="section-header reveal">
      <span class="eyebrow" data-en="My Approach" data-es="Mi Enfoque">My Approach</span>
      <h2 data-en="Four principles.<br>One system." data-es="Cuatro principios.<br>Un sistema.">Four principles.<br>One system.</h2>
      <p class="subtitle" data-en="Everything I do is built around sustainable progress — not quick fixes. Here is how we get there together." data-es="Todo lo que hago está construido en torno al progreso sostenible — no a soluciones rápidas. Así es cómo llegamos allí juntos.">Everything I do is built around sustainable progress — not quick fixes. Here is how we get there together.</p>
    </div>

    <div class="principles-grid">
      <!-- Card 1: Movement First -->
      <div class="principle-card reveal" data-i="0">
        <img src="images/web/IMG_2623.jpg" alt="Strength training fundamentals" loading="lazy" style="object-position: center calc(50% + 50px);">
        <div class="principle-overlay" aria-hidden="true"></div>
        <span class="principle-num">01</span>
        <div class="principle-text">
          <h3 data-en="Movement First" data-es="El Movimiento Primero">Movement First</h3>
          <p data-en="Master the fundamentals before adding load. Quality reps build lasting strength." data-es="Domina los fundamentos antes de añadir carga. Las repeticiones de calidad construyen fuerza duradera.">Master the fundamentals before adding load. Quality reps build lasting strength.</p>
        </div>
      </div>

      <!-- Card 2: Progressive Overload -->
      <div class="principle-card reveal" data-i="1">
        <img src="images/web/IMG_2625.jpg" alt="Perfect form and technique" loading="lazy">
        <div class="principle-overlay" aria-hidden="true"></div>
        <span class="principle-num">02</span>
        <div class="principle-text">
          <h3 data-en="Progressive Overload" data-es="Sobrecarga Progresiva">Progressive Overload</h3>
          <p data-en="Systematic, data-driven progression — every session builds on the last." data-es="Progresión sistemática y basada en datos — cada sesión construye sobre la anterior.">Systematic, data-driven progression — every session builds on the last.</p>
        </div>
      </div>

      <!-- Card 3: Individualized Programming -->
      <div class="principle-card reveal" data-i="2">
        <img src="images/web/IMG_2558.jpg" alt="Personal coaching session" loading="lazy" style="object-position: center calc(50% + 90px);">
        <div class="principle-overlay" aria-hidden="true"></div>
        <span class="principle-num">03</span>
        <div class="principle-text">
          <h3 data-en="Individualized Programming" data-es="Programación Individualizada">Individualized Programming</h3>
          <p data-en="No cookie-cutter plans. Your program is built around your body, your schedule, your goals." data-es="Sin planes genéricos. Tu programa está diseñado en torno a tu cuerpo, tu horario y tus objetivos.">No cookie-cutter plans. Your program is built around your body, your schedule, your goals.</p>
        </div>
      </div>

      <!-- Card 4: Sustainable Consistency -->
      <div class="principle-card reveal" data-i="3">
        <img src="images/web/IMG_2598.jpg" alt="Consistent training for results" loading="lazy" style="object-position: center calc(50% + 50px);">
        <div class="principle-overlay" aria-hidden="true"></div>
        <span class="principle-num">04</span>
        <div class="principle-text">
          <h3 data-en="Sustainable Consistency" data-es="Consistencia Sostenible">Sustainable Consistency</h3>
          <p data-en="Long-term habits over short-term intensity. We build a lifestyle, not just a program." data-es="Hábitos a largo plazo sobre intensidad a corto plazo. Construimos un estilo de vida, no solo un programa.">Long-term habits over short-term intensity. We build a lifestyle, not just a program.</p>
        </div>
      </div>
    </div>
  </div>
</section>
```

### Key HTML Attributes:
- **Section ID:** `id="approach"` on the `<section>` element
- **Grid class:** `class="principles-grid"` (not `.principles-carousel`)
- **Card classes:** `class="principle-card reveal"` with `data-i="0"`, `data-i="1"`, `data-i="2"`, `data-i="3"` (for staggered reveal animation)
- **Bilingual support:** All text uses `data-en` and `data-es` attributes

---

## CSS (Original)

```css
.principles {
  background: var(--color-bg);
}

.principles-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 1.25rem;
}

.principle-card {
  position: relative;
  overflow: hidden;
  aspect-ratio: 4/3;
  border-radius: var(--radius-md);
  border: 1px solid rgba(255,255,255,0.06);
  cursor: default;
  transition: transform 0.3s var(--ease-premium), box-shadow 0.3s var(--ease-premium), border-color 0.3s var(--ease-premium);
}

.principle-card:hover {
  transform: translateY(-4px);
  border-color: rgba(90,15,34,0.5);
  box-shadow: 0 20px 60px rgba(59,11,22,0.22);
}

.principle-card img {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.5s var(--ease-premium);
}

.principle-card:hover img {
  transform: scale(1.04);
}

.principle-overlay {
  position: absolute;
  inset: 0;
  background: linear-gradient(0deg, rgba(0,0,0,0.85) 0%, rgba(0,0,0,0.1) 55%, transparent 100%);
  z-index: 1;
}

.principle-num {
  position: absolute;
  top: 1.25rem;
  right: 1.25rem;
  z-index: 2;
  font-family: var(--font-body);
  font-size: 0.75rem;
  font-weight: 500;
  letter-spacing: 0.1em;
  color: rgba(231,215,200,0.5);
}

.principle-text {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  z-index: 2;
  padding: 1.5rem 1.5rem 1.5rem;
}

.principle-text h3 {
  font-family: var(--font-display);
  font-size: 1.375rem;
  font-weight: 300;
  line-height: 1.15;
  margin-bottom: 0.4rem;
  color: var(--color-text);
}

.principle-text p {
  font-size: 0.875rem;
  color: rgba(242,240,236,0.6);
  line-height: 1.5;
}

/* Mobile responsive */
@media (max-width: 768px) {
  .principles-grid {
    grid-template-columns: 1fr;
  }
}
```

---

## Design Characteristics (Original)

| Aspect | Details |
|--------|---------|
| **Layout** | 2-column CSS Grid (desktop), 1 column (mobile ≤768px) |
| **Card Size** | Aspect ratio 4:3, responsive with gap 1.25rem |
| **Border Radius** | var(--radius-md) = 14px |
| **Hover Effect** | Card lifts up 4px, image zooms 1.04x, border shifts color |
| **Overlay** | Dark gradient from bottom (black) to transparent top |
| **Text Position** | Absolutely positioned at card bottom with overlay |
| **Reveal Animation** | IntersectionObserver on scroll — staggered fade-in via data-i attribute |
| **Number Badge** | 01–04 in top-right, semi-transparent |
| **Images** | Absolute cover, specific object-position overrides for some cards |

---

## JavaScript (Original)

No special JS beyond the global scroll reveal system. All animation handled by:

```js
// Global reveal observer (applies to all .reveal elements)
if (!reducedMotion) {
  var revealObserver = new IntersectionObserver(function (entries) {
    entries.forEach(function (e) {
      if (e.isIntersecting) {
        e.target.style.opacity = '1';
        e.target.style.transform = 'translateY(0)';
        revealObserver.unobserve(e.target);
      }
    });
  }, { threshold: 0.12, rootMargin: '0px 0px -60px 0px' });

  document.querySelectorAll('.reveal').forEach(function (el) {
    var delay = parseFloat(el.dataset.i || 0) * 0.08;
    el.style.opacity = '0';
    el.style.transform = 'translateY(28px)';
    el.style.transition =
      'opacity 0.65s cubic-bezier(0.16,1,0.3,1) ' + delay + 's, ' +
      'transform 0.65s cubic-bezier(0.16,1,0.3,1) ' + delay + 's';
    revealObserver.observe(el);
  });
}
```

Each card's `data-i` attribute multiplied by 0.08s determined its stagger delay on scroll reveal.

---

## To Revert to This Layout

1. Replace the `.principles-scroll-root` wrapper div structure back to just `<section class="principles" id="approach">`
2. Change `class="principles-carousel"` back to `class="principles-grid"`
3. Add `class="reveal" data-i="0"` (through data-i="3") back to the 4 `.principle-card` elements
4. Replace the carousel CSS with the original grid CSS above
5. Remove the principles 3D carousel JavaScript block entirely
6. The global reveal observer will handle animation automatically

---

## Notes

- Original layout was simpler and lower-motion friendly
- Grid was responsive and mobile-optimized
- No sticky scroll or perspective transforms
- All animation via standard intersection observer reveal system
- Perfect for users with reduced motion preferences (animation disabled)
