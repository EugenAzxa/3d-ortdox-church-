# Вечан спомен · Eternal Memory

A five-chapter night walk through the churchyard of the Serbian Orthodox Parish of
St. Archangel Gabriel in Richmond Hill, rendered live in Three.js and layered with
editorial typography.

[**View the source**](https://github.com/EugenAzxa/3d-ortdox-church-) · [**Read the build prompt**](PROMPT.md)

## What it does

- Walks a live WebGL camera through a procedural Orthodox church as the page scrolls:
  cruciform nave, windowed drum, gilt cupola, three-bar cross, bell tower and apse.
- Combines moonlight, lit shoji-thin window glass, a burning candle stand, drifting
  snow, rising embers, layered haze and a hand-rolled bloom pipeline.
- Layers oversized display type, Cyrillic chapter marks, flat foreground silhouettes
  and graded parish photographs over the live world.
- Includes chapter navigation, a progress rail, a responsive mobile layout, reduced-motion
  behaviour and a custom cursor for precise pointer devices.

## How it is made

A deliberately small static site. `index.html` contains the document structure, CSS,
procedural scene construction, scroll choreography and interaction logic. A vendored
Three.js r149 build provides WebGL rendering with no package manager and no build step.

The church, churchyard wall, gate, candle stand, trees, snow, embers, moon and
post-processing are all constructed at runtime. There is no model file. The only binary
assets are the parish photographs, which are graded into the night palette in CSS.

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
├── vendor/
│   └── three.min.js
└── assets/
    ├── angel.webp
    ├── church-3.webp
    ├── church-7.webp
    ├── church-exterior.webp
    ├── qr/
    └── saints/
```

## Chapters

| # | Section | Cyrillic | The shot |
|---|---------|----------|----------|
| 00 | Hero | Вечан спомен | Outside the gate, three-quarter, the cross against the moon |
| 01 | Threshold | Праг | At the gate, looking down the swept path to the west door |
| 02 | Iconostasis | Иконостас | Inside the yard, looking up at the lit drum |
| 03 | Voices | Гласови | Off the south flank, the candle stand in the near foreground |
| 04 | Remembrance | Спомен | Above the roofline, the cupola and cross |

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
