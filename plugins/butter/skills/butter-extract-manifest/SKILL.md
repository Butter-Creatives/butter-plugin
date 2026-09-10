---
name: butter-extract-manifest
description: Measure a Butter preview into the manifest endSession requires. Use when a Butter video version has been approved and is ready to become a project, before calling endSession.
---

# Measure a Butter preview into a manifest

`submitSessionVersion` returned a `previewUrl`. Open it in a browser and measure it, producing
the JSON `endSession` takes as its `manifest` argument.

**CRITICAL RULE: every number must be MEASURED from the live DOM** — `getBoundingClientRect()` and
`getComputedStyle()` in the page you opened. Never estimate, recall, or copy numbers out of the CSS
source. If you cannot measure something, omit it and record it in `census.skipped`. A number you
guessed is worse than one you left out: it produces a project that is subtly wrong everywhere, with
no error to point at.

All geometry in CSS pixels at device scale factor 1, relative to the top-left of the
`[data-butter-canvas]` element — not the viewport.

## What the document is

One self-playing HTML file on a single timeline. `[data-butter-canvas]` carries `data-w`,
`data-h` and `data-duration`; each `<section data-scene>` carries `data-start` and
`data-duration`. Those declarations give you the scene boundaries directly — do not infer them.

Because it plays itself, **seek before you measure**: pause every animation with
`document.getAnimations()`, set `currentTime` into the middle of a layer's own window, and measure
there. Measuring at time zero records an entrance's starting position as the layer's real position.

## Rasterize as little as possible

The target engine draws vector shapes and live text natively.

- **`<img>` and `<video>`** → `layers`, kind `img` or `vid`. Their `src` is already a hosted
  url — put it straight in `pngUrl` / `vidUrl`. Do not re-rasterize them.
- **`<svg>`** → `layers`, kind `svg`. Capture it to a transparent PNG, upload it, and put that url
  in `pngUrl` — the raster is what gets drawn today. Also put its `outerHTML` in `svgSource`, with
  every style it depends on inlined so it stands alone, so the same layer can become real vector
  art later. A layer with no `pngUrl` is rejected.
- **A text run** → `textLayers`, ALWAYS — even with a shadow, stroke or background. Record those as
  data rather than baking them in. Split into separate layers when one element mixes colours, fonts
  or weights; each text layer carries exactly one of each.
- **Everything else that paints** (boxes, pills, discs, rules, cards, buttons, glows) →
  `shapeLayers` with its measured style. Only when a shape cannot be expressed natively — a blend
  mode, `backdrop-filter`, `clip-path`, a radial or conic gradient, per-corner radii, **a border** —
  compute the painted bounds (the union of the element's DOM rect and any shadow or glow that spills
  outside it), take a transparent PNG screenshot clipped to exactly those bounds, upload it, and set
  `fallbackPng` to `{ "url": "<uploaded url>", "bbox": [x, y, w, h] }` where `bbox` is those same
  painted bounds. Use those bounds as the clip rect and report them here — they travel with the image
  so the engine can place it correctly even when it is larger than the shape's own `bbox`. Also set
  `preferFallback` to a one-line reason. A shape that needs a raster and has none is drawn natively
  without whatever needed it — a bordered pill loses its stroke — or dropped if it has no fill at all.
- **Elements that paint nothing** (layout wrappers, `<br>`, `<defs>`) → `census.skipped`.

### Hosting an asset

Two ways. Never inline image bytes into the manifest itself.

1. **It already has a public https url** — use it directly. Hotlinked `<img>` and `<video>` sources
   need nothing, and that covers anything large: product photos, footage, brand assets. The build
   fetches these from our servers, not from your machine, so `localhost`, `127.0.0.1` and any
   other private address are rejected — serving the files yourself does not work.
2. **Otherwise call `uploadSessionAsset`.** Pass `url` when the asset has one that may not last
   — a signed or expiring CDN link — and the server fetches it, with nothing binary crossing the
   conversation. Pass `dataBase64` only for bytes that exist nowhere else, such as a fallback
   raster you just captured. Those are small element crops; anything approaching the 2MB limit means
   you are rasterizing something that should have stayed a native shape or a hotlink.

Either way you get back a url. Put it in `fallbackPng`, `pngUrl` or `vidUrl` as appropriate.

## Schema

```json
{
  "canvas": { "w": 1080, "h": 1920 },
  "scenes": [
    { "startTimestamp": 0, "endTimestamp": 3,
      "transition": { "type": "fade", "duration": 0.4 } }
  ],
  "background": { "color": "#rrggbb" },
  "layers": [
    { "id": "l1", "label": "Product shot", "z": 0, "bbox": [0,0,100,100], "rotation": -3.4,
      "kind": "img", "pngUrl": "https://…", "startTimestamp": 0, "endTimestamp": 3,
      "animationCss": "0% {transform:translateY(60px);opacity:0} 100% {transform:none;opacity:1}",
      "visualEffects": [{ "type": "drop-shadow", "color": "#000000", "offsetX": 0, "offsetY": 8, "blur": 12, "opacity": 0.4 }] }
  ],
  "shapeLayers": [
    { "id": "s1", "label": "Offer pill", "z": 3, "bbox": [0,0,100,100], "shape": "rectangle",
      "fill": { "type": "solid", "color": "#rrggbb" }, "cornerRadius": 48,
      "startTimestamp": 0, "endTimestamp": 3 },
    { "id": "s3", "label": "Gradient bar", "z": 2, "bbox": [0,800,1080,120], "shape": "rectangle",
      "fill": { "type": "linear-gradient", "angle": 150,
                "startPoint": {"x": -52, "y": 30}, "endPoint": {"x": 52, "y": -30},
                "stops": [{ "color": "#ffffe0", "at": 0 }, { "color": "#ccd080", "at": 1 }] },
      "startTimestamp": 0, "endTimestamp": 3 },
    { "id": "s2", "label": "Glow ring", "z": 4, "bbox": [10,10,80,80], "shape": "circle",
      "fill": { "type": "solid", "color": "#rrggbb" }, "preferFallback": "radial gradient",
      "fallbackPng": { "url": "https://…/s2.png", "bbox": [-20,-20,120,120] },
      "startTimestamp": 0, "endTimestamp": 3 }
  ],
  "textLayers": [
    { "id": "t1", "text": "AIR MAX", "z": 5, "bbox": [0,0,100,100], "fontSize": 96,
      "fontFamily": "Poppins", "color": "#ffffff", "fontStyle": "800", "align": "center",
      "letterSpacing": -2, "lineHeight": 92, "xWidth": 41.2, "naturalLineHeight": 110,
      "startTimestamp": 0, "endTimestamp": 3,
      "shadow": { "color": "#000000", "offsetX": 0, "offsetY": 4, "blur": 12, "opacity": 0.4 },
      "stroke": { "color": "#000000", "width": 2 },
      "textBackground": { "color": "#fecb2f", "paddingX": 20, "paddingY": 38,
                          "cornerRadius": 65 },
      "visualEffects": [{ "type": "blur", "amount": 4 }] }
  ],
  "groups": [
    { "id": "g1", "label": "Offer badge", "members": ["s1","t1"],
      "reason": "pill plus the line printed on it; they read as one object" }
  ],
  "soundEffects": [{ "url": "https://…", "startTimestamp": 0, "duration": 2 }],
  "warnings": ["one line per thing that will not survive conversion"],
  "census": { "positionedElements": 19, "emitted": 19,
              "skipped": [{ "selector": ".wrap", "why": "layout wrapper, paints nothing" }] }
}
```

Timestamps are in seconds and **relative to the whole video**, never to the scene.

## Rules that matter

- **Fonts are real families.** The contract requires Google Fonts loaded by `<link>`, so
  `getComputedStyle()` gives you the real name. If you find an invented `@font-face` alias,
  report the real family it was built from, or say so in `warnings` — never pass the alias off as
  real.
- **Rotation.** The engine rotates a component about its own centre, so a rotated element's
  `bbox` must be its UNROTATED box: strip the rotation in the live DOM, re-measure, restore it.
  Never report a rotated bounding box.
- **`fontStyle` carries the measured weight.** Do not round 600 or 800 to 700; each weight maps to
  its own font variant.
- **`shadow.opacity`** is the alpha of the measured shadow colour, with `color` its opaque form.
- **A button, pill, chip or tag is one element**, so it is one text layer carrying
  `textBackground` — its measured `background-color`, box padding and border radius, all in px.
  Do not leave that paint in `animationCss`, which carries animation only, and do not emit a
  second shape layer behind the text: the census counts one layer per element.
- **Measuring `xWidth`:** set a canvas 2d context font to the element's computed font shorthand and
  take `measureText("x").width` — but measure at 1000px and scale back, because `measureText`
  quantises to whole pixels at display sizes and the raw figure can be several percent out. Letter
  spacing is derived by dividing by this number, so the error lands straight in the tracking.
- **`animationCss`** is strictly for keyframe animations — nothing else. Do not use it to set
  fills, backgrounds, or any static paint; those belong in the typed fields (`fill`, `color`, etc.)
  or in `fallbackPng`. Inline every variable — no `var()`. All values must be absolute — no
  `em`, `rem`, `%`, `vw`, or other relative units; use `px` throughout. Use the `transform`
  shorthand for all motion (`transform: translateY(60px) rotate(15deg)`), never the individual
  CSS transform properties (`translate`, `rotate`, `scale`) — the converter only recognises
  `transform`. Delays are relative to the layer's own `startTimestamp`: a layer starting at 5.0s
  whose animation fires at 5.1s has a 0.1s delay here, and that delay is kept, so a
  staggered group must carry one delay per layer.
- **Shape fills** support `{ "type": "solid", "color": "#rrggbb" }` and
  `{ "type": "linear-gradient", "angle": <degrees>, "startPoint": {"x":<px>,"y":<px>}, "endPoint": {"x":<px>,"y":<px>}, "stops": [{ "color": "#rrggbb", "at": 0 }, …] }`
  with at least two stops and `at` values from 0 to 1. `startPoint` and `endPoint` are CSS pixels
  relative to the bbox center (positive x right, positive y down); read them from
  `getComputedStyle()` and convert: for `linear-gradient(Xdeg, ...)` the start point is at
  `(-sin(X)*len/2, cos(X)*len/2)` and end at `(sin(X)*len/2, -cos(X)*len/2)` where `len` covers
  the element. When `startPoint` and `endPoint` are both provided `angle` is ignored by the renderer,
  but always include it for completeness. Measure from `getComputedStyle()`; do not approximate
  with `animationCss`.
- **`visualEffects`** is the array for drop-shadows and blurs on any layer type. Both fields come
  from `getComputedStyle()` — measure them, do not guess.
  - Drop-shadow: `{ "type": "drop-shadow", "color": "<opaque hex>", "offsetX": <px>, "offsetY": <px>, "blur": <px>, "opacity": <0–1> }`.
    `color` is the shadow colour without alpha; `opacity` is the alpha separately.
    Source: `filter: drop-shadow(…)` or `box-shadow`.
  - Blur: `{ "type": "blur", "amount": <px> }` where `amount` is the CSS pixel radius from
    `filter: blur(<px>)` as returned by `getComputedStyle()`. Use this for frosted-glass
    or defocus effects — never put filter values in `animationCss`.
- Use `\n` for line breaks inside text.
- Text inside an element you rasterized must NOT also appear in `textLayers`.
- `z` is one shared paint order across `layers`, `shapeLayers` and `textLayers`; 0 is backmost.

## Grouping

Emit a group for any set of layers a designer would move, scale or animate as one thing — a badge
and the text on it, an icon beside its label, a headline and the rule under it. Two hard rules:
members must be **adjacent in paint order**, and a layer belongs to at most one group. Group by what
reads as one object, not by what is merely nearby. When in doubt leave it ungrouped and say so in
`warnings`; an over-eager group is harder to undo in the editor than a missing one.

## Before you call endSession

`census.positionedElements` must equal `census.emitted` plus `census.skipped.length`, where
emitted counts background plus every layer, shape and text layer. **`endSession` rejects a manifest
that does not balance**, and the fix is to re-measure — never to adjust the numbers until the check
passes. Scene windows must meet exactly: each scene's end is the next one's start.

Then call `endSession` with the session id, the approved version id, and this manifest. Report the
census numbers, how many shapes versus rasters you emitted, and any warnings.
