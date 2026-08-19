# Вечан спомен · Eternal Memory

A six-chapter night walk into the Serbian Orthodox Parish of St. Archangel Gabriel in
Richmond Hill: across the churchyard, in through the west door, down the nave to a wall
of lit arches where every page is kept by Saylavy, and up out through the cupola.
Rendered live in Three.js and layered with editorial typography.

[**View the source**](https://github.com/EugenAzxa/3d-ortdox-church-) · [**Read the build prompt**](PROMPT.md)

## What it does

- Walks a live WebGL camera through a procedural Orthodox church as the page scrolls,
  outside and in: cruciform nave, windowed drum, gilt cupola, three-bar cross, bell tower,
  apse, and a full interior with iconostasis, kandila and a memorial wall.
- Combines moonlight, lit shoji-thin window glass, a burning candle stand, drifting
  snow, rising embers, layered haze and a hand-rolled bloom pipeline.
- Layers oversized display type, Cyrillic chapter marks, flat foreground silhouettes
  and graded parish photographs over the live world.
- Builds the memorial as thirty-six lit arches down the north wall, each one flickering on
  its own clock, standing for a Memory Page kept by Saylavy.
- Carries the browsable memorial wall itself: six Memory Pages read from
  `data/memorials.json`, filterable and searchable, each opening a full page with the
  epitaph, life story, timeline, time capsule and candle count.
- Follows the calendar. The churchyard is in spring, summer, autumn or winter depending on
  the date, changing foliage, weather, ground, sky, fog and moon.
- Includes chapter navigation, a progress rail, a responsive mobile layout, reduced-motion
  behaviour and a custom cursor for precise pointer devices.

## How it is made

A deliberately small static site. `index.html` contains the document structure, CSS,
procedural scene construction, scroll choreography and interaction logic. A vendored
Three.js r149 build provides WebGL rendering with no package manager and no build step.

The church, churchyard wall, gate, candle stand, trees, snow, embers, moon and
post-processing are all constructed at runtime. There is no model file. The only binary
assets are the parish photographs, which are graded into the night palette in CSS.

### Going inside

Exterior walls are `FrontSide` boxes, so from within they are back-facing and cull to
nothing: the interior shell is what closes the room in. The nave ceiling is built in four
pieces around an opening under the drum, and both the nave and transept roofs are split
around the crossing, because a solid prism over the nave caps the cupola from below. The
whole interior is registered for a single opacity fade driven by scroll position, and the
snow and embers are faded out with it - weather belongs to the churchyard.

### The seasons

`SEASONS` holds four palettes and weather profiles, picked from the current month for the
northern hemisphere. One particle field serves all four: a negative fall speed is what
turns snow into fireflies drifting up on an August night, and the sway and size come from
the same config. Tree canopies are three overlapping lumps per trunk so a crown has a
silhouette rather than reading as a lollipop; winter draws none and keeps the bare frame.
The chapter 01 heading names what is underfoot, so it is written from the season too.

Append `?season=spring|summer|autumn|winter` to walk a season that is not the current one.
The hero carries a switcher for the same purpose.

### The memorial wall

The six people are fictional and the notice above the grid says so; that notice is load
bearing and must stay. Candles lit in the browser are kept in `localStorage` only, because
the real stand is in Richmond Hill and the page should not pretend otherwise.

### The bloom

Rather than pulling in `examples/jsm`, the post chain is hand-rolled on core Three.js:
the scene renders into a half-float target, a bright pass feeds a short mip chain,
each level takes a separable nine-tap gaussian, the chain is accumulated back up with
additive blits, and one composite pass applies a filmic shoulder and gamma. This keeps
the whole site to a single vendored file.

Every colour in the scene is authored in sRGB and linearised on the way in
(`ColorManagement.enabled = true`), because the composite pass ends with its own gamma.
Without that step the night renders as an overcast afternoon.

## Run locally

From the repository root:

```bash
python3 -m http.server 4173 --bind 127.0.0.1
```

Then visit [http://127.0.0.1:4173/](http://127.0.0.1:4173/).

There is no build step, environment variable, analytics script or runtime network
dependency beyond the Google Fonts stylesheet.

## Project structure

```text
3d-ortdox-church/
├── index.html
├── PROMPT.md
├── README.md
├── data/
│   └── memorials.json
├── vendor/
│   └── three.min.js
└── assets/
    ├── angel.webp
    ├── church-3.webp
    ├── church-7.webp
    ├── church-exterior.webp
    ├── portraits/
    ├── qr/
    └── saints/
```

`data/memorials.json` is fetched at runtime, so the site must be served over HTTP. Opened
straight off the disk the wall says so rather than sitting empty.

## Chapters

| # | Section | Cyrillic | The shot |
|---|---------|----------|----------|
| 00 | Hero | Вечан спомен | Outside the gate, three-quarter, the cross against the moon |
| 01 | Threshold | Праг | At the gate, looking down the swept path to the west door |
| 02 | Iconostasis | Иконостас | Inside, down the nave, facing the gilt screen |
| 03 | Voices | Гласови | Mid-nave, angled across the candle stand |
| 04 | Memorial | Спомен-зид | The north wall, thirty-six arches, the six pages in front of it |
| 05 | Remembrance | Вечан спомен | Under the crossing, looking up through the drum |
| -- | Footer | | The ascent out, above the roofline |

The camera never cuts. It crosses the threshold at chapter 02 and the interior shell
dissolves on the way up at chapter 05, so the rise out through the cupola is one move.

## Accessibility and degradation

- The complete reading experience survives `prefers-reduced-motion`. Nothing is hidden
  behind a scroll trigger; only the movement stops.
- If WebGL fails to initialise the canvas is removed, a gradient ground takes its place,
  and the page reads as a flat editorial document. This path is tested, not assumed.
- Semantic landmarks, a skip link, visible focus rings and labelled controls throughout.
- The custom cursor is applied only to fine pointer devices.

## Design and attribution

The parish photographs belong to St. Archangel Gabriel Serbian Orthodox Parish and are
taken from arhangelgavrilotoronto.com for the purpose of this design demonstration.
This is a demonstration, not an official parish publication.

The structural and motion language, the chapter format and the hand-rolled bloom
approach are a reinterpretation of [Kage](https://github.com/MengTo/kage) by MengTo,
built from the portable brief that project publishes for exactly this purpose. No Kage
code or artwork is reused here; the scene, the palette, the type system and the
silhouettes are original to this project. Kage grants no licence for reuse of its own
code or artwork, and none is claimed.

The vendored Three.js r149 build retains its MIT licence notice and copyright attribution.
