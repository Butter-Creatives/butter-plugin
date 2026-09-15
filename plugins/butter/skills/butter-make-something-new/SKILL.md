---
name: butter-make-something-new
description: Make a new ad or video from scratch, or from assets you already have. Use when the user explicitly wants an ad or video built from scratch, or wants photos, clips or product shots they already have turned into an ad — not for a vague request that names no starting point, which butter-ambiguous-start handles.
---

# Make Something New

Make a new ad or video from scratch, or from assets you already have.

## Before you start

Anything the user has already told you, or that the sources under "Gather first" already cover, is known — do not ask for it again; if nothing is missing, go straight to "Gather first". Otherwise ask for what is still missing with `askQuestions`, once, before gathering: pass only the questions below that are still unknown, and add your own for anything else you cannot build without — give an option shaped { "label": "…", "input": "url" } when the answer is a link, { "label": "…", "input": "upload" } when the user should provide a file, and leave out options for free text. Then end your turn — the answers arrive as the user's next message, starting "Answers:". Do not ask them again in chat, with three exceptions, each at most once: when an answer says the user will upload a file in chat, ask them in plain chat to attach it; when a shared ad link cannot be read, ask for a screenshot; and when something you cannot build without was skipped, ask for it. Keep any attached file and upload it with `uploadSessionAsset` once `startSession` has run. Do not call `startSession` before the answers arrive. For any other skipped question, use your best judgment: its recommended answer if it has one, otherwise what you can infer from the sources you have.

```json
{"questions":[{"title":"What should I use as a visual reference?","options":[{"label":"Match my website's branding (recommended)","input":"url"},{"label":"Match an ad I'll share","input":"url"},{"label":"Upload an ad in chat","input":"upload"}]},{"title":"Where should the assets, product details and copy come from?","options":["Use my website (recommended)",{"label":"Upload my images in chat","input":"upload"},"Use a connected source"]},{"title":"What format would you like?","options":["Vertical 9:16 (recommended)","Square 1:1","Landscape 16:9"]},{"title":"How long should the video be?","options":["15 seconds (recommended)","6 seconds","30 seconds"]}]}
```

## Gather first

Butter does not supply this data — it comes from the connectors already available to you, or from
what the user provides.

- The brand palette, logo and typeface from the brand kit or the site

If a source is unavailable, say so and ask the user for it rather than inventing figures. Never
invent an offer, a price, a discount, or a performance claim the user did not give you.

## Hold constant

The brand system, and any reference the user gives

## Vary

Concept, hook, pacing and motion

## How many

1

## Format

Use the format answer; default to vertical 9:16

## Building each video

Butter turns HTML + CSS into an editable Butter project.

**Every Butter tool except `askQuestions` takes a `userPrompt`, and every such call must carry one.** It is the user's latest
message word for word — what they typed, never your summary of it and never your own reasoning. On
`startSession` that is the message that asked for the video in the first place. Leave it out only
when nothing the user said prompted the call.

For every video this workflow calls for:

1. Call `startSession`. It returns a `sessionId` and the HTML contract. **The contract is
   authoritative** — follow it exactly, and re-read it rather than relying on memory.
2. Upload **every** image, video and sound the video will use with `uploadSessionAsset` before it
   goes in the page, including any you add while iterating. The page uses the urls it returns, never
   the original links: the preview runs on them, and `endSession` rejects any other url. Upload
   each file **once**: an asset used more than once, such as the logo in every variant, keeps the url
   from its first upload in every video and session that uses it. Each upload is a public https link, or a file under 2MB; for a larger local file, ask for a link instead.
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

One session; offer variations once the first is approved
