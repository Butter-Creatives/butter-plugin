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
- **`<svg>`** → `layers`, kind `svg`. Put its `outerHTML` in `svgSource`, with every style it
  depends on inlined so it stands alone. A PNG is only needed if it will not render standalone.
- **A text run** → `textLayers`, ALWAYS — even with a shadow, stroke or background. Record those as
  data rather than baking them in. Split into separate layers when one element mixes colours, fonts
  or weights; each text layer carries exactly one of each.
- **Everything else that paints** (boxes, pills, discs, rules, cards, buttons, glows) →
  `shapeLayers` with its measured style. Only when a shape cannot be expressed natively — a blend
  mode, `backdrop-filter`, `clip-path`, a radial or conic gradient, per-corner radii — capture a
  transparent PNG cropped to its painted bounds, upload it, and set `preferFallback` with a
  one-line reason.
- **Elements that paint nothing** (layout wrappers, `<br>`, `<defs>`) → `census.skipped`.

### Hosting an asset

Two ways. Never inline image bytes into the manifest itself.

1. **It already has an https url** — use it directly. Hotlinked `<img>` and `<video>` sources
   need nothing, and that covers anything large: product photos, footage, brand assets.
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
      "css": "0% {transform:translateY(60px);opacity:0} 100% {transform:none;opacity:1}" }
  ],
  "shapeLayers": [
    { "id": "s1", "label": "Offer pill", "z": 3, "bbox": [0,0,100,100], "shape": "rectangle",
      "fill": { "type": "solid", "color": "#rrggbb" }, "cornerRadius": 48,
      "startTimestamp": 0, "endTimestamp": 3 }
  ],
  "textLayers": [
    { "id": "t1", "text": "AIR MAX", "z": 5, "bbox": [0,0,100,100], "fontSize": 96,
      "fontFamily": "Poppins", "color": "#ffffff", "fontStyle": "800", "align": "center",
      "letterSpacing": -2, "lineHeight": 92, "xWidth": 41.2, "naturalLineHeight": 110,
      "startTimestamp": 0, "endTimestamp": 3 }
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
- **Measuring `xWidth`:** set a canvas 2d context font to the element's computed font shorthand and
  take `measureText("x").width` — but measure at 1000px and scale back, because `measureText`
  quantises to whole pixels at display sizes and the raw figure can be several percent out. Letter
  spacing is derived by dividing by this number, so the error lands straight in the tracking.
- **`css`** carries what the fields could not: animation and keyframes, and any leftover paint.
  Inline every variable — no `var()`. Delays are relative to the layer's own
  `startTimestamp`: a layer starting at 5.0s whose animation fires at 5.1s has a 0.1s delay here.
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
