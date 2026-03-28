# Effect Library

A living collection of user-approved interactive effects.
Each entry is AI-generated and user-confirmed, stored for automatic reuse when similar content contexts arise.

---

## How Effects Are Matched

When generating new slides, check this file before writing new effect code.

**Match dimensions (by priority):**
1. **Content type** — e.g., "skill list" → tag-cloud; "stats / metrics" → stat-bar
2. **Interaction intent** — e.g., "let audience explore" → interactive-panel; "show numbers with impact" → animated-counter
3. **Data structure** — e.g., "4 parallel items" → 4-tag floating; "image + numbers" → mac-browser + stat-bar
4. **Mood/aesthetic fit** — e.g., "professional but energetic" → chipIn + dark bar

**Reuse thresholds:**
- **>80% match** — Reuse silently. After generation, tell user: *"Used EFFECT-001 for the skill tags section."*
- **50–80% match** — Prompt user: *"This looks similar to EFFECT-002 (stat bar). Use it here?"*
- **<50% match** — Use generic effect. Offer to save the new effect after generation.

---

## How to Add an Effect

**User-initiated:** Say something like:
> "Save this floating tag effect for reuse" or "Add the stat bar to the effect library"

**AI-initiated (after page generation):** AI will ask:
> "I used a new effect here (canvas text-fly). Save it to your effect library for future reuse?"

---

## Effect Index

| ID | Name | Category | Trigger Keywords |
|----|------|----------|-----------------|
| [EFFECT-001](#effect-001) | Floating Tags + Click-to-Expand Panel | interactive-panel / tag-cloud | skills, traits, "why me", clickable chips, explore |
| [EFFECT-002](#effect-002) | Story Carousel Panel | interactive-panel / carousel | narrative, story, multiple examples, before/after, timeline |
| [EFFECT-003](#effect-003) | Mac Browser Frame Panel | contextual-container | social media, website preview, product demo, "show the thing" |
| [EFFECT-004](#effect-004) | Stat Bar (chipIn animation) | data-display | metrics, statistics, numbers, impressions, followers, results |
| [EFFECT-005](#effect-005) | Canvas Text-Fly (Observer) | fullscreen-canvas | thoughts, brainstorm, overflow of ideas, stream of consciousness |

---

## EFFECT-001

**Name:** Floating Tags + Click-to-Expand Panel

**User description:** Tags that float around the screen with a gentle bobbing animation. Clicking one highlights it and pops open a detail panel.

**Trigger semantics:**
- Content type: skill list, personal traits, "why me", feature highlights, value propositions
- Keywords: tags, chips, 可点击, expand, 技能点, traits, qualities, "what I bring"
- Interaction intent: let audience actively discover, not passively read

**Categories:** `interactive-panel`, `tag-cloud`

**Parameters:**
- `--tag-count`: number of tags (tested with 4–5)
- `--float-amplitude`: bobbing range, ~6–10px per axis
- `--accent-color`: highlight color on `.active` state (default `#ff3300`)
- `--rot`: per-tag rotation angle (CSS custom property, assigned inline)
- `--tx`, `--ty`: viewport-relative offsets from center (CSS custom properties)

**CSS snippet:**
```css
/* === FLOATING TAGS === */
.arena {
  position: absolute; inset: 0;
  display: flex; align-items: center; justify-content: center;
  pointer-events: none; z-index: 2;
}
.tag {
  position: absolute;
  font-weight: 900;
  font-size: clamp(.75rem, 1.4vw, 1.15rem);
  background: rgba(255,255,255,.82);
  border: 1.5px solid rgba(0,0,0,.13);
  border-radius: 999px;
  padding: .55em 1.3em;
  cursor: pointer; pointer-events: all;
  backdrop-filter: blur(6px);
  box-shadow: 0 2px 12px rgba(0,0,0,.07);
  transition: transform .18s cubic-bezier(.16,1,.3,1), box-shadow .18s, background .18s, color .18s;
  white-space: nowrap;
}
.tag:hover {
  transform: translate(var(--tx,0),var(--ty,0)) scale(1.06) !important;
  box-shadow: 0 6px 24px rgba(0,0,0,.12);
}
.tag.active {
  background: var(--accent-color, #ff3300) !important;
  color: #fff !important;
  border-color: var(--accent-color, #ff3300) !important;
  box-shadow: 0 8px 28px rgba(255,51,0,.28) !important;
}

/* Float keyframes — assign float0–float3 to tags */
@keyframes float0 {
  0%,100% { transform: translate(var(--tx),var(--ty)) rotate(var(--rot)) }
  50%     { transform: translate(calc(var(--tx) + 7px),calc(var(--ty) - 9px)) rotate(var(--rot)) }
}
/* (float1–float3 vary the offset directions) */

/* Entrance animation */
@keyframes tagIn {
  from { opacity:0; transform: translate(var(--tx),calc(var(--ty) + 20px)) rotate(var(--rot)) }
  to   { opacity:1; transform: translate(var(--tx),var(--ty)) rotate(var(--rot)) }
}
/* Each tag: tagIn first, then float looping */
.tag[data-i="0"] { --tx:-32vw; --ty:-14vh; --rot:-3.5deg;
  animation: tagIn .55s cubic-bezier(.16,1,.3,1) .15s both, float0 5.8s ease-in-out 0.75s infinite }
```

**JS snippet:**
```javascript
let activeKey = null;
const tags     = document.querySelectorAll('.tag');
const backdrop = document.getElementById('backdrop');

function openPanel(key) {
  if (activeKey !== null) closePanel(false);
  activeKey = key;
  tags.forEach(t => t.classList.toggle('active', t.dataset.key === String(key)));
  document.getElementById('panel-' + key).classList.add('show');
  backdrop.classList.add('show');
}

function closePanel(resetActive = true) {
  if (activeKey === null) return;
  document.getElementById('panel-' + activeKey).classList.remove('show');
  backdrop.classList.remove('show');
  tags.forEach(t => t.classList.remove('active'));
  if (resetActive) activeKey = null;
}

tags.forEach(tag => {
  tag.addEventListener('click', () => {
    const k = Number(tag.dataset.key);
    activeKey === k ? closePanel() : openPanel(k);
  });
});
backdrop.addEventListener('click', closePanel);
document.addEventListener('keydown', e => { if (e.key === 'Escape') closePanel(); });
```

**HTML template:**
```html
<!-- Floating tags -->
<div class="arena">
  <div class="tag" data-i="0" data-key="0">Tag One</div>
  <div class="tag" data-i="1" data-key="1">Tag Two</div>
  <div class="tag" data-i="2" data-key="2">Tag Three</div>
  <div class="tag" data-i="3" data-key="3">Tag Four</div>
</div>
<div class="hint">tap a card to expand</div>
<div class="backdrop" id="backdrop"></div>

<!-- Detail panel (one per tag) -->
<div class="panel" id="panel-0">
  <div class="panel-close" id="close-0">✕</div>
  <div class="panel-num">01</div>
  <div class="panel-title">Tag One Title</div>
  <div class="panel-body">Detail content here. <strong>Highlight key ideas.</strong></div>
</div>
```

**Deletion checklist** (when removing a tag):
- [ ] Remove `.tag` element from HTML
- [ ] Remove corresponding `#panel-X` element
- [ ] Remove `close-X` from the close listener array
- [ ] Adjust remaining `data-i` positions so tag distribution stays balanced

**Source:** tera-intro/slide-04.html, 2026-03-19
**Times used:** 1

---

## EFFECT-002

**Name:** Story Carousel Panel

**User description:** A wide panel with an image on top and text below, plus prev/next navigation and dot indicators. Swipes through multiple "story pages."

**Trigger semantics:**
- Content type: narrative evidence, "tell me about X" with multiple sub-stories, case studies, timeline
- Keywords: story, narrative, 案例, 经历, before/after, multiple examples, evidence
- Interaction intent: walk the audience through a progression

**Categories:** `interactive-panel`, `carousel`

**Parameters:**
- `STORY_PAGES`: total page count (set in JS)
- image aspect ratio: `16/7` (landscape, suitable for event photos)
- panel width: `min(660px, 82vw)`

**CSS snippet:**
```css
.story-viewport { overflow: hidden; position: relative; }
.story-track {
  display: flex;
  transition: transform .38s cubic-bezier(.16,1,.3,1);
}
.story-page { min-width: 100%; display: flex; flex-direction: column; }
.story-img-wrap { width: 100%; aspect-ratio: 16/7; overflow: hidden; }
.story-img-wrap img { width: 100%; height: 100%; object-fit: cover; }
.story-nav {
  display: flex; align-items: center; justify-content: space-between;
  padding: .6rem 1.5rem;
  border-top: 1px solid rgba(0,0,0,.07);
}
.story-dot { width: .45rem; height: .45rem; border-radius: 50%; background: rgba(0,0,0,.15); cursor: pointer; transition: background .2s, transform .2s; }
.story-dot.active { background: var(--accent-color, #ff3300); transform: scale(1.25); }
```

**JS snippet:**
```javascript
const STORY_PAGES = 2;
let storyPage = 0;

function storyGoTo(p) {
  storyPage = p;
  document.getElementById('story-track').style.transform = `translateX(-${p * 100}%)`;
  document.querySelectorAll('.story-dot').forEach((d,i) => d.classList.toggle('active', i === p));
  document.getElementById('story-counter').textContent = `${p+1} / ${STORY_PAGES}`;
  document.getElementById('story-prev').disabled = p === 0;
  document.getElementById('story-next').disabled = p === STORY_PAGES - 1;
}
// Arrow key support: ArrowLeft/ArrowRight when this panel is active
```

**Source:** tera-intro/slide-04.html, 2026-03-19
**Times used:** 1

---

## EFFECT-003

**Name:** Mac Browser Frame Panel

**User description:** A panel styled like a macOS browser window, with traffic-light dots and a URL bar. The content area shows a real screenshot or image, making it feel like you're literally looking at the product.

**Trigger semantics:**
- Content type: social media presence, website/product demo, "let me show you the real thing"
- Keywords: social media, screenshot, profile page, product, website, 展示, demo
- Interaction intent: ground abstract claims in real, recognizable UI context

**Categories:** `contextual-container`

**Parameters:**
- panel width: `min(780px, 88vw)`
- image inside: `width: 100%; object-fit: cover`
- URL bar text: replace `xinyun · Social Media Profile` with your own

**CSS snippet:**
```css
.mac-wrap {
  position: absolute; left: 50%; top: 50%;
  transform: translate(-50%,-50%) scale(.88);
  width: min(780px, 88vw); max-height: 80vh;
  display: flex; flex-direction: column;
  border-radius: 10px; overflow: hidden;
  box-shadow: 0 32px 80px rgba(0,0,0,.28), 0 0 0 1px rgba(0,0,0,.08);
  z-index: 20; opacity: 0; pointer-events: none;
  transition: opacity .25s cubic-bezier(.16,1,.3,1), transform .25s cubic-bezier(.16,1,.3,1);
}
.mac-wrap.show { opacity: 1; pointer-events: all; transform: translate(-50%,-50%) scale(1); }
.mac-bar {
  display: flex; align-items: center; gap: .8rem;
  padding: .6rem 1rem; background: #e8e8e8;
  border-bottom: 1px solid rgba(0,0,0,.1);
}
.mac-dots { display: flex; gap: .42rem; }
.mac-dot { width: .75rem; height: .75rem; border-radius: 50%; cursor: pointer; }
.md-red { background: #ff5f57; }
.md-yellow { background: #febc2e; }
.md-green { background: #28c840; }
```

**Compose with:** EFFECT-004 (stat-bar) placed below the image for metrics display.

**Source:** tera-intro/slide-04.html, 2026-03-19
**Times used:** 1

---

## EFFECT-004

**Name:** Stat Bar (chipIn animation)

**User description:** A dark bottom bar showing 3–4 key numbers (e.g., impressions, followers, views), each animating in with a subtle slide-up on panel open. Re-triggers every time the panel is opened.

**Trigger semantics:**
- Content type: social metrics, product stats, achievements, "the numbers"
- Keywords: statistics, metrics, impressions, followers, views, likes, 数据, results, KPIs
- Interaction intent: make numbers feel earned and impactful, not just listed

**Categories:** `data-display`

**Parameters:**
- stat count: 3–4 chips (use `.stat-divider` between)
- `animation-delay`: stagger per chip (0.15s, 0.3s, 0.45s, 0.6s)
- background: `#111` (dark, designed to contrast with light slide backgrounds)

**CSS snippet:**
```css
.stat-bar {
  display: flex; align-items: center; justify-content: space-around;
  background: #111; flex-shrink: 0;
  padding: .6rem 1.2rem;
}
.stat-divider { width: 1px; height: 1.6rem; background: rgba(255,255,255,.12); }
.stat-chip {
  display: flex; flex-direction: column; align-items: center; gap: .18em;
  opacity: 0;
  animation: chipIn .4s cubic-bezier(.16,1,.3,1) forwards;
}
@keyframes chipIn {
  from { opacity: 0; transform: translateY(6px); }
  to   { opacity: 1; transform: translateY(0); }
}
.stat-num { font-weight: 900; color: #fff; line-height: 1; white-space: nowrap; }
.stat-num span { color: var(--accent-color, #ff3300); }
.stat-label { font-size: .36rem; letter-spacing: .1em; text-transform: uppercase; color: rgba(255,255,255,.4); }
```

**JS snippet (re-trigger on panel open):**
```javascript
// Call this inside openPanel() when the stat-bar panel opens:
document.querySelectorAll('.stat-chip').forEach(c => {
  c.style.animation = 'none';
  void c.offsetWidth;   // force reflow
  c.style.animation = '';
});
```

**HTML template:**
```html
<div class="stat-bar">
  <div class="stat-chip" style="animation-delay:.15s">
    <div class="stat-num">300k<span>+</span></div>
    <div class="stat-label">Impressions</div>
  </div>
  <div class="stat-divider"></div>
  <div class="stat-chip" style="animation-delay:.3s">
    <div class="stat-num">84<span>%</span></div>
    <div class="stat-label">Active Followers</div>
  </div>
  <div class="stat-divider"></div>
  <div class="stat-chip" style="animation-delay:.45s">
    <div class="stat-num">6,000<span>+</span></div>
    <div class="stat-label">Likes</div>
  </div>
</div>
```

**Source:** tera-intro/slide-04.html, 2026-03-19
**Times used:** 1

---

## EFFECT-005

**Name:** Canvas Text-Fly (Observer / Stream of Consciousness)

**User description:** Fullscreen dark overlay where text strings float upward like rising thoughts. Some lines are red (accent), most are white with varying weights and opacities. Gives a feeling of an overflowing mind or continuous observation.

**Trigger semantics:**
- Content type: brainstorming, "my thought process", observations, insights, "how I think"
- Keywords: thoughts, observations, 洞察, 想法, 记录, stream, ideas, detail-oriented
- Interaction intent: immerse audience in a mental landscape, not a list

**Categories:** `fullscreen-canvas`

**Parameters:**
- `THOUGHTS[]`: array of text strings to display
- `isRed` probability: 18% (for accent-colored lines)
- spawn rate: one new line every 800–2000ms
- initial batch: 5–8 lines spawned immediately on open

**JS snippet:**
```javascript
const canvas = document.getElementById('obs-canvas');
const ctx    = canvas.getContext('2d');
let rafId = null, lines = [], running = false;

function randomLine(immediate) {
  const isRed   = Math.random() < .18;
  const size    = 13 + Math.random() * 9;
  const opacity = .12 + Math.random() * .52;
  const speed   = .9 + Math.random() * 1.2;
  const text    = THOUGHTS[Math.floor(Math.random() * THOUGHTS.length)];
  const y       = immediate ? Math.random() * canvas.height : canvas.height + size + Math.random() * 60;
  return { text, x: canvas.width * .5, y, size, opacity, speed,
           color: isRed ? 'var(--accent, #ff3300)' : '#ffffff',
           weight: isRed ? 900 : 300 };
}

function draw() {
  if (!running) return;
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  lines.forEach(l => {
    l.y -= l.speed;
    ctx.save();
    ctx.globalAlpha = l.opacity;
    ctx.font        = `${l.weight} ${l.size}px 'Archivo', sans-serif`;
    ctx.fillStyle   = l.color;
    ctx.textAlign   = 'center';
    ctx.fillText(l.text, l.x, l.y);
    ctx.restore();
  });
  lines = lines.filter(l => l.y > -40);
  rafId = requestAnimationFrame(draw);
}

function startObserver() {
  running = true;
  canvas.width  = window.innerWidth;
  canvas.height = window.innerHeight;
  lines = [];
  // spawn initial batch
  for (let i = 0; i < 6; i++) lines.push(randomLine(true));
  // continue spawning
  const spawn = () => { if (!running) return; lines.push(randomLine(false)); setTimeout(spawn, 800 + Math.random() * 1200); };
  spawn();
  draw();
}

function stopObserver() {
  running = false;
  cancelAnimationFrame(rafId);
  lines = [];
  ctx.clearRect(0, 0, canvas.width, canvas.height);
}
```

**HTML template:**
```html
<div id="panel-observer" class="fs-canvas">
  <button class="fs-close" id="close-observer">✕</button>
  <div class="fs-label">Observer</div>
  <canvas id="obs-canvas"></canvas>
</div>
```

**CSS snippet:**
```css
.fs-canvas {
  position: fixed; inset: 0; z-index: 30;
  background: rgba(10,10,10,.93);
  opacity: 0; pointer-events: none;
  transition: opacity .3s cubic-bezier(.16,1,.3,1);
}
.fs-canvas.show { opacity: 1; pointer-events: all; }
.fs-canvas canvas { position: absolute; inset: 0; width: 100%; height: 100%; }
.fs-close {
  position: absolute; top: 1.4rem; right: 2rem; z-index: 5;
  font-weight: 900; font-size: .9rem;
  color: rgba(255,255,255,.35); background: none; border: none; cursor: pointer;
  transition: color .15s;
}
.fs-close:hover { color: var(--accent, #ff3300); }
```

**Source:** tera-intro/slide-04.html, 2026-03-19
**Times used:** 1
