# Build prompt

Create a single-page, cinematic WebGL experience called **Вечан спомен**: a six-chapter
night walk into a Serbian Orthodox parish - across the churchyard, through the west door,
down the nave, and up out through the cupola. The result should feel
like an editorial art book moving through a live 3D world, not a conventional parish
landing page.

## Experience

- Use a fixed full-viewport Three.js canvas as the environmental layer.
- Build the church, churchyard wall, gate, swept path, candle stand, bare winter trees,
  moon, terrain, snow, embers, haze and atmosphere procedurally. No model files.
- The church is Serbian Orthodox in the Raska manner reduced to essentials: a cruciform
  nave, one cupola on a windowed drum, a semicircular apse facing east, a bell tower over
  the west door, and a three-bar cross with its foot bar raised toward the penitent thief.
- Drive one continuous camera path from page scroll. Each section should feel like a new
  composed shot rather than a hard scene replacement.
- Take the camera inside. Build a nave interior with an iconostasis, royal doors, hanging
  kandila, a candle stand, and a memorial wall of small lit arches, one per Memory Page.
- Leave the drum open: build the ceiling around the opening and split the nave and
  transept roofs around the crossing, or the cupola is capped from below.
- Dissolve the interior on the ascent rather than cutting, and fade the snow and embers
  out with it.
- Add restrained bloom, film grain, vignette, depth haze, warm candlelight, cold
  moonlight, and a high pale moon.
- Keep the palette near-black, blue-charcoal, warm bone, gilt bronze, and candle amber.

## Layout

- Structure the page as a hero, threshold, iconostasis, voices, memorial, remembrance
  closing, and manifesto footer.
- Use oversized uppercase serif headings, large Cyrillic display type, small technical
  labels in mono, chapter numbers, fine rules, and generous negative space.
- Layer graded parish photographs into editorial cards, and flat SVG silhouettes of a
  stone wall, bare branches and a candle stand at the bottom of the active viewport.
- Foreground layers should arrive at full visual opacity, remain pinned while their
  section is active, then fade and blur away during the handoff.
- Center any play icon within the image frame itself, excluding the caption area.

## Motion

- Reveal headings word by word and supporting elements individually.
- Use slow, precise section transitions, subtle parallax, and eased camera interpolation.
- Let the navigation, chapter rail, hero chips and foreground layers respond to the
  active section from one shared source of truth.
- Include reduced-motion behavior that preserves the complete reading experience.

## Interaction and quality

- Use a custom cursor only for fine pointer devices.
- Provide working anchor navigation, mobile navigation, responsive layouts, semantic
  landmarks, and accessible labels.
- Keep runtime assets local and use relative paths.
- Author every scene colour in sRGB and linearise on the way in, because the composite
  pass applies its own gamma. Skipping this renders the night as an overcast afternoon.
- Hand-roll the bloom on core Three.js: bright pass, mip chain, separable blur, additive
  upsample, filmic composite. Do not pull in `examples/jsm`.
- Survive a WebGL failure: remove the canvas, drop in a gradient ground, keep the page
  readable as a flat editorial document.
- Avoid frameworks, build tooling, analytics, trackers, placeholder imagery, generic
  glassmorphism, excessive glow, em dashes, emoji, and decorative motion without
  narrative purpose.
- Verify at desktop and approximately 390 x 844, check all assets for 404s, parse every
  inline script, inspect the browser console, and test one complete scroll and navigation
  interaction before shipping.
