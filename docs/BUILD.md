# Building a scroll-driven 3D scene for a website

A short guide to the technique behind this site, and to the mistakes that cost the most
time building it. Nothing here is specific to a church; it applies to any page that wants a
live 3D world behind editorial type.

## The shape of it

One `index.html`. A vendored `three.min.js`. No build step, no package manager, no
framework. Structure, styles, scene construction, scroll choreography and interaction all
live in that one document, which is unusual and is the point: there is nothing between the
source and what runs.

```
index.html      the whole site
vendor/         three.min.js, MIT, copied in
assets/         images
data/           anything the page fetches at runtime
```

Serve over HTTP (`python3 -m http.server 4173`). Opened off the disk, `fetch` cannot read a
sibling file.

## The five systems

**1. A fixed canvas.** `position: fixed; inset: 0; z-index: 0`. Everything else floats over
it. The page scrolls; the canvas never moves.

**2. A camera on a curve.** One waypoint per section, a Catmull-Rom through them, and the
scroll position mapped onto it. Each section anchors at its own centre. The camera lags the
scrollbar deliberately: scroll is a step function, and the lag is what turns it into a walk.

**3. An editorial layer.** Real HTML at `z-index: 10`, revealed by IntersectionObserver.
Headings split word by word into clipping boxes so a line assembles rather than fading in.

**4. Post-processing.** Hand-rolled on core three: render to a half-float target, bright
pass into a mip chain, separable blur, additive upsample, one composite with a filmic
shoulder and gamma. This avoids `examples/jsm` entirely and keeps the site to one vendored
file.

**5. A look.** Every value that varies - sky, fog, lights, ground, foliage, weather, bloom,
exposure, page scrim - held in one config object per state and interpolated between them.
If nothing differs structurally between states, a change is a lerp rather than a rebuild.

## Rules that cost me time

**Colour management.** Author in sRGB and set `ColorManagement.enabled = true`, because the
composite pass ends with its own gamma. Skip it and everything is corrected twice: night
renders as an overcast afternoon.

**A material colour multiplies its map.** Tint light. A near-black tint erases the texture
you just drew into it.

**Randomise value, not hue.** Randomising R, G and B independently randomises hue. Vary one
scalar against a fixed colour, or you get pink and green flagstones.

**Watch fill rate, not triangle count.** 30k triangles and 300k both cost nothing. What
costs is pixels: the device pixel ratio, the size of the bloom chain, and overdraw.

**Transparency is expensive.** A transparent material loses early-z, so every fragment
behind it is shaded anyway. Fading a whole interior with per-material alpha put 115
transparent meshes in the scene and cost more than everything else combined. Swap groups
instead and cover the cut.

**backdrop-filter on anything that repaints while scrolling** is the single most expensive
thing you can put on the page, and it has nothing to do with the 3D.

**One trigger per transition.** If a veil comes off scroll position and the geometry swap
comes off camera position, they fire at different moments: a flash, then an unmasked change
of world. Derive both from one number.

**Distance to a box is not distance to its planes.** Testing against the wall planes lit a
veil at full strength thirty metres away, because the camera happened to sit at the
ceiling's height.

**Groups swapped on a change must be built in their starting state.** A handler that only
acts on a change never runs at boot, so whatever the constructor left visible stays visible.

**No waypoint should rest inside a transition's ramp.**

**Ease each segment of the camera path.** Waypoints tens of metres apart outdoors and two
metres apart indoors, traversed at a constant rate, give a twenty-fold change in speed at
each handover. Smoothstep within the segment puts velocity at zero on every waypoint.

**Measure the camera by bucketing against the camera parameter, not scroll.** Sections have
very different heights, so equal slices of scroll are nowhere near equal chapters. Bucketed
by scroll, the profile invents spikes that do not exist.

**ExtrudeGeometry writes UVs in shape units.** Metres, not 0..1. A texture set to repeat
across a box will tile several times per metre on an extrusion.

**ShapeGeometry writes the shape's own coordinates into the UV buffer.** Remap to the
bounding box or the texture samples outside 0..1 and smears.

**A hole is a hole.** A gradient plane painted on a solid wall reads as a poster. Cut the
opening out of an extruded shape and put geometry behind it, and it parallaxes.

**Judge everything at the scale the site actually uses.** Measure how many pixels the thing
occupies. Detail below that threshold is not detail, it is noise. I spent several passes
fixing a face that is eighteen pixels tall.

**Do not model people with primitives.** Everyone reads human form too well; a
lathe-and-sphere figure will not pass, which is not true of stonework or trees. Use a
photographic cut-out, or leave the figure out.

## Verify it, do not assume it

Drive a real browser headlessly and assert. Every one of these caught something:

- Console errors and failed requests, at every breakpoint.
- `prefers-reduced-motion`: every reveal visible, nothing waiting on a scroll trigger.
- WebGL unavailable: canvas removed, page still readable as a flat document.
- No horizontal overflow.
- Every combination of whatever states the look has.
- Camera speed sampled by scrolling, not by teleporting the rig.
- Raycast from the camera when something looks wrong, rather than guessing what is in front.

Two habits worth keeping. When a surface looks wrong, tint each candidate material a
different flat colour and render one frame: it answers in seconds what guessing does not
answer in an hour. And add a flag that freezes the camera, so a scene can be inspected by
hand while the rig keeps running.
