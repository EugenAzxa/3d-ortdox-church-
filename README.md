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
- Follows the calendar and the clock. The churchyard is in spring, summer, autumn or winter
  by date, and in day, dusk or night by hour, changing foliage, weather, ground, sky, fog,
  sun or moon, window light and the bloom.
- Flies the Serbian state flag on a pole by the gate, arms and all, painted to a canvas and
  waved in the vertex shader, and puts a flock of birds over the churchyard in every season
  but winter.
- Hangs the six people themselves in the arches of the 3D wall, graded to the same sepia as
  the cards on the page, with smaller unlit niches above and below for the room a parish
  still has.
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

The room is modelled from photographs of the parish, and it is not a cathedral: a low, wide
hall about 3.3m to a suspended white tile ceiling on recessed troffers, a flagstone floor, cream
walls, and a white and gold screen across the east end with striped pilasters between arched
icons. Building it as a tall stone nave was the wrong room.

It is dressed the way the parish dresses it: a brass polyeleos over the nave, the long run of
small framed icons down the south wall with a memorial board and speakers, banners on staves
either side of the screen, royal doors carrying four round icons under a gilt arch, chairs with
backs down both walls, a skirting board, and the candle stand built as a chest on a plinth with
a tray of sand and a pale reredos behind it.

One thing worth knowing if you edit the floor texture: its cells randomise a single value
against a fixed hue. Randomising R, G and B independently randomises hue, not brightness, and
gives a floor of pink, green and yellow flagstones.

Two things make it work. The exterior shell is opaque, and its cross-arm wall stands between
the lens and the room from chapter 02 onward, so the shell is hidden while the camera is
inside it - the room is closed on all four sides, so nothing is lost. And the troffers make
this the brightest ground the type ever sits on, so a scrim rides the same fade as the room
itself, keeping bone-white headings readable against a lit ceiling.

The whole interior is registered for one opacity fade driven by scroll position, and the snow
and embers fade with it - weather belongs to the churchyard.

### The church

Modelled from renders of the parish's own idiom rather than from a generic dome-and-cross
silhouette: coursed ashlar limestone, standing-seam copper on every roof, a melon dome whose
eight ribs are half-torus arcs so each is a true meridian, an octagonal belfry stage pierced
on all eight faces above a square shaft with a string course, narrow slit windows in stone
surrounds, an arcaded west porch with archivolts over dark bays, and a recessed portal with
two timber leaves standing open on strap hinges.

There is no arcade or portico. The west front is the bell tower, and the entrance is a
single arched portal in its base. An earlier pass invented a five-bay porch across the
front, which is not on the building and read as wrong from every angle.

The leaves are hinged forward of the jambs and swung back at about 108 degrees, not propped
ajar. Forward of the jambs matters: the jamb stones stand proud to z=6.24, so a leaf hinged
on the wall face at 6.16 swings straight through them. A door held at a shallow angle presents its edge to anyone walking up the
path and reads as a plank; only well past ninety does it read as a door standing open.

Both openings are real, not painted. The tower shaft is hollow for its lower storey and its
west wall is an extruded shape with an arch-shaped hole cut in it, so what shows through
parallaxes as the camera moves: a vestibule with a stone floor, a red runner, plastered
reveals, an icon, a candle stand burning inside, and the nave lit through a second arch at
the back. The porch's back wall is pierced the same way, and behind it sits a
metre of actual room: gilt boards, an icon in a gold frame, a stand of candles burning in
front of it, and the runner carrying on in. Painting either opening as a gradient plane on
a solid wall, which is what both were, reads as a poster hung on the tower.

Two things fought this. `arch()` adds a mullion and two transoms to anything wider than
half a metre, so the doorway was being glazed like a window. And `ExtrudeGeometry` writes
UVs in shape units - metres - so a texture set to repeat across a box tiles several times
per metre on an extrusion and reads as a pale panel beside the coursed walls.

Stone and copper are both canvas textures. If you retint them, tint light: the map is
multiplied by the material colour, so a near-black tint erases the masonry drawn into it,
which is exactly what happened the first time.

### The trees

Grown rather than assembled. A trunk forks, forks again, and carries leaf clusters on the
outer two levels; every branch of every tree goes into one instanced mesh and every cluster
into another, so a wood of twenty-four trees is two draw calls whatever the season. Winter
keeps the frame and drops the leaves, which is the reason for building the branching
properly rather than putting lumps on a stick.

Foliage is opaque in every season. A season with less of it gets fewer clusters, not
see-through ones: alpha on several thousand instances costs early-z, which is the thing the
performance pass was about, and a thinning crown is the truer picture anyway.

### The living look

The season and the hour are not fixed at boot. Every value either side of them is a colour
or a scalar, so a change is a lerp over two and a half seconds rather than a reload: sky,
fog, three lights, the sun or moon and its halo, stars, ground, canopy, weather, window
glow, candle strength, bloom, exposure and the page scrim all move together.

The one thing that used to differ structurally was winter having no leaves, and winter keeps
its leaves now, under snow. That is what makes the rest possible. Two follow-ons fell out of
it: leaf tint moved from the instance colours to the material, so a season is one assignment
rather than several thousand buffer writes; and the weather is built once at full count with
density, size, fall, sway, colour and alpha as uniforms.

By default the churchyard walks a year and a day on its own, twelve stations of about ten
seconds. Touching either picker holds it where you put it, on the assumption that somebody
who picks winter would like to stay in it.

### Windows

A church window at night is not a lit panel, it is a hole with a room behind it. The glass
carries that as a texture: dark at the head where the vault is, warm low down where the
candles are, shadowed down both reveals, a lamp burning somewhere behind, and the leading
across it. That reads as something seen through rather than something glowing.

Two faults were hiding in them. The stone surrounds were centred at y=2.7 against windows
running from 2.5 to about 5.2, so they hung below the glass as dark slabs in the wall. And
each surround is 0.07 thick, so centring it at 4.735 spanned 4.70 to 4.77 and swallowed the
light sitting at 4.76 - which is why some windows lit and others did not.

### The seasons

`SEASONS` holds four palettes and weather profiles, picked from the current month for the
northern hemisphere. One particle field serves all four: a negative fall speed is what
turns snow into fireflies drifting up on an August night, and the sway and size come from
the same config. Tree canopies are three overlapping lumps per trunk so a crown has a
silhouette rather than reading as a lollipop; winter draws none and keeps the bare frame.
The chapter 01 heading names what is underfoot, so it is written from the season too.

Append `?season=spring|summer|autumn|winter` and `?time=day|dusk|night` to walk a season or
an hour that is not the current one. The hero carries switchers for both.

### The flag

Red over blue over white. Russia runs the same three colours the other way up, so the band
order is already the difference, but a bare tricolour gets read as Russian by almost
everyone. What identifies the Serbian flag is the lesser coat of arms, so it is drawn:
crowned red shield with a red velvet cap under gold arches on a pearled band, silver
double-headed eagle displayed, gold beaks, talons and fleurs-de-lis, and on the breast the
cross with four firesteels. Painted to a canvas at 1200x800 rather than fetched, which
keeps the no-remote-asset rule and costs one draw call.

### Performance

The scene is small - about 29k triangles - so nothing here is geometry bound. What cost
frames was fill rate and compositing, and the profile said so plainly:

- The room faded in and out on per-material alpha, which meant 115 transparent meshes, no
  early-z, and heavy overdraw with every fragment looping 13 lights. The room and the shell
  now trade places behind a brief veil instead, keyed to where the camera actually is. That
  took transparent meshes from 115 to 8, and the interior's seven lights leave the scene
  with it.
- Device pixel ratio was capped at 1.85, so a retina panel rendered a 2664x1665 target with
  a bloom chain over it. Capped at 1.45.
- The bright pass now lands at quarter resolution rather than half, taking the blur chain
  from 1332x832 down to 522x326 and full-screen blits from 16 to 12.
- Six backdrop-filters, one of them on the nav, which repaints its backdrop on every scroll
  frame. Down to one, on the Memory Page scrim, which only exists while the page is open.
- The grain layer was inset by 120%, making it 4896x3060 of composited noise under an
  animated transform. Now 1958x1224.

### Time of day

`TIMES` sits over `SEASONS` rather than replacing it: a night entry leaves a field null and
the season's own value survives, while day and dusk override sky, key light, ambient, fog
tint, sun disc, star opacity, window glow, candle strength, bloom and exposure. Day is not
the night turned up. The windows stop being the brightest thing in frame, the stars go, and
the page needs its own scrim, because bone-white type on a bright sky is unreadable. That
scrim is weighted to the left and the top and bottom bands where the type actually sits, so
the middle of the sky is left alone.

Both pickers rewrite the query string and reload. The world is built once at boot from the
two values, and rebuilding it live would mean tearing down every material and light for a
control almost nobody touches.

### Candles

Every stand is built with `taperStand`: an instanced mesh of wax bodies and one Points for
the flames, so thirty candles cost two draw calls. Flames on their own read as blobs of
light hanging in the air, which is what all three interior stands were doing before the wax
was added.

The scrim over the interior has to stay light enough for them to survive it. A flat
full-frame black protects the type perfectly and puts out every candle in the room, so it is
weighted to the bands the columns sit in and left thin through the middle.

### The parish priest

Chapter 03 carries a panel for Father Đurađ Kojić: nine questions a family actually arrives
with, and the answers the office gives, appended to a transcript as they are chosen.

There is no figure of him in the churchyard. One stood at the door through several passes and
never got past reading as a lathe-and-sphere lump: everyone reads human form too well for
primitives to pass, which is not true of the stonework or the trees. It was cut rather than
shipped at that standard, and the scene is better for being consistently architectural. What
is left is what was working - light on the threshold, and a marker on the door jamb that
leads to his section.

To bring a figure back, use a photographic cut-out rather than geometry. That is how
architectural work has always handled people, and at the 168 pixels this scene gives a
person, a photograph is indistinguishable from real while a model is not. It would want a
shot against a plain background, and a decision about placing an identifiable real person
inside a synthetic scene, which is a step beyond showing their photograph.

It is deliberately a fixed set, not a generative chat, and it takes no free text. Father
Đurađ is a real, named man, and nothing on this page should be able to put a sentence in his
mouth that the parish has not agreed to. Every answer is procedural - what the office does,
in what order - rather than theology or opinion, he is given a typographic card rather than
an invented portrait, and the notice under the panel says plainly that the wording was
written for the demonstration and is not his. A test asserts the panel has no input or
textarea.

If this ever goes live, the answers are the thing to replace first, with his own words.

### Asking for a candle

Chapter 03 carries the parish's candle offering: three things it will light, an intention,
a name, an address for the photograph, and a note to the priest. Submitting shows what would
come back, with the coming Sunday worked out from today's date.

It is a request, not a checkout. The parish office would take the payment. This page asks
for no card details anywhere, accepts none, and says so under the form; a test asserts that
no payment field exists. Nothing is posted to any server.

From a Memory Page, "ask the parish for a real one" closes the page and carries that
person's name down into the form.

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
| 02 | Iconostasis | Иконостас | Inside, down the hall, facing the white and gold screen |
| 03 | Voices | Гласови | Mid-room, angled across the candle stand |
| 04 | Memorial | Спомен-зид | The north wall, the six faces in its arches, the pages in front |
| 05 | Remembrance | Вечан спомен | Centred on the iconostasis, the kontakion over it |
| -- | Footer | | The ascent out, above the roofline |

Chapter 01 is squared onto the doorway and chapter 02 sits just inside it, so the leg
between them is a dolly straight through the opening. It is worth checking that literally
rather than by eye: sample the leg, find where it crosses the door plane, and confirm the
crossing falls inside the jambs. An earlier version stood three metres off the axis and
aimed a metre and a half above the lintel, and slid past the door instead of entering it.

Curve tension is 0.22 to 0.3 rather than the default. The tangent at each waypoint is set
by its neighbours, so a hero shot fourteen metres to one side swings the next leg wide
before it comes back.

A near-field drift layer crosses the frame close to the lens: large, slow, tumbling, one
leaf or petal or flake to the season. The weather above it is far and fine, and the two read
as different distances rather than as one field of dots. It is what gives the opening shot
something moving in it.

Measure the camera by bucketing speed against the camera parameter, not against scroll. The
sections have very different heights - chapter 03 carries the priest and the candle offering
and is several times the height of chapter 01 - so equal slices of scroll are nowhere near
equal chapters, and a profile bucketed by scroll invents spikes that are not there. Bucketed
correctly, the largest velocity step at any chapter handover is 0.036.

Each segment of the camera path is eased in and out, so velocity reaches zero at every
waypoint. Traversed at a constant rate per chapter, waypoints tens of metres apart outside
and two metres apart inside give 1.25 units a step out there and 0.05 in here, and the step
change at each handover is what reads as lurching.

The camera never cuts. It crosses the threshold at chapter 02 and the room dissolves on the
way out at chapter 05, so the rise back over the roofline is one move. Section anchors sit at
each section's centre, so the memorial - which is much taller than the others - holds its
waypoint while the reader browses the wall.

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
