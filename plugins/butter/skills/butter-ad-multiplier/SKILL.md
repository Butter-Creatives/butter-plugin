---
name: butter-ad-multiplier
description: Find what's winning and make the next 3 ads to test. Use when the user wants more ads based on what is already winning, asks for variants of a top performer, or wants the next batch of creative tests.
---

# Ad Multiplier

Find what's winning and make the next 3 ads to test.

## Gather first

Butter does not supply this data — it comes from the connectors already available to you, or from
what the user provides.

- Top creatives from the last 30 days with spend, CTR and ROAS, from the Meta connector
- The brand palette, logo and typeface from the brand kit or the site

If a source is unavailable, say so and ask the user for it rather than inventing figures. Never
invent an offer, a price, a discount, or a performance claim the user did not give you.

## Hold constant

The winning layout, palette and offer structure

## Vary

Hook, proof and CTA — one axis per variant, so a win is attributable

## How many

3

## Format

The source ad's placements

## Building each video

Butter turns HTML + CSS into an editable Butter project. For every video this playbook calls for:

1. Call `startSession`. It returns a `sessionId` and the HTML contract. **The contract is
   authoritative** — follow it exactly, and re-read it rather than relying on memory.
2. Upload **every** image, video and sound the video will use with `uploadSessionAsset` before it
   goes in the page, including any you add while iterating. The page uses the urls it returns, never
   the original links: the preview runs on them, and `endSession` rejects any other url. Upload
   each file **once**: an asset used more than once, such as the logo in every variant, keeps the url
   from its first upload in every video and session that uses it.
3. Write ONE self-contained HTML document that plays itself: a single timeline, elements positioned
   in time with `animation-delay` measured from the start of the whole video, and
   `animation-fill-mode: both` on every animation. CSS motion is unrestricted and read back
   automatically. Every sound is an `<audio data-butter-audio>` element the page declares, which
   JavaScript may also play; anything visual a script drives is invisible to the extractor, so its
   timing has to be declared by hand in the manifest.
4. Call `submitSessionVersion` with the `sessionId` and the html. It returns lint findings.
5. Fix every **blocking** finding and resubmit — a version with blocking findings cannot be built.
   Warnings are safe to ship but cost editability, so prefer fixing them.
6. Call `renderPreviews` with the session and version ids. That is what puts the videos in front
   of the user — submitting a version shows them nothing. Pass several at once whenever they are
   meant to be compared, each with a short label saying what makes it different.
7. Offer the next steps — Refine, Make Variations, or Edit in Butter — and wait for their answer rather than picking one.
   Refinements are further versions in the same session; variations get a session each.
8. Only once the user says they are happy, call `prepareEndSession` with the session id and the
   approved version id. It returns the instructions for measuring that preview into a manifest:
   follow them, then call `endSession` with the session id, the version id and that manifest.
   `endSession` cannot be called until `prepareEndSession` has been. Do not start this on your
   own judgement — the preview is theirs to approve, and every successful `endSession` creates a
   project.

## Handoff

One session per variant; three Butter projects
