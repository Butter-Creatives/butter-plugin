---
name: butter-static-to-motion
description: Turn the best-performing static ads into motion ads. Use when the user wants to animate an existing static ad, turn stills into video, or add motion without changing a design.
---

# Static → Motion

Turn the best-performing static ads into motion ads.

## Before you start

Anything the user has already told you, or that the sources under "Gather first" already cover, is known — do not ask for it again; if nothing is missing, go straight to "Gather first". Otherwise ask for what is still missing with `askQuestions`, once, before gathering. It runs inside a session, so call `startSession` first, as in step 1 of "Building each video", and pass its `sessionId`; then pass only the questions below that are still unknown, and add your own for anything else you cannot build without — give an option shaped { "label": "…", "input": "url" } when the answer is a link, { "label": "…", "input": "upload" } when the user should provide a file, and leave out options for free text. Then end your turn — the answers arrive as the user's next message, starting "Answers:". Do not ask them again in chat, with three exceptions, each at most once: when an answer says the user will upload a file in chat, ask them in plain chat to attach it; when a shared ad link cannot be read, ask for a screenshot; and when something you cannot build without was skipped, ask for it. Upload any attached file with `uploadSessionAsset` into that session. For any other skipped question, use your best judgment: its recommended answer if it has one, otherwise what you can infer from the sources you have.

```json
{"questions":[{"title":"Which static ad should I animate?","options":[{"label":"Upload it in chat","input":"upload"},{"label":"Link to the ad","input":"url"},"Use my top static ad from a connected source"]},{"title":"Where should the assets, product details and copy come from?","options":["Use my website (recommended)",{"label":"Upload my images in chat","input":"upload"},"Use a connected source"]},{"title":"How long should the video be?","options":["6–8 second loop (recommended)","6 seconds","15 seconds","30 seconds"]}]}
```

## Gather first

Butter does not supply this data — it comes from the connectors already available to you, or from
what the user provides.

- The product shots and logo used in the static ad

If a source is unavailable, say so and ask the user for it rather than inventing figures. Never
invent an offer, a price, a discount, or a performance claim the user did not give you.

## Hold constant

The composition, palette and copy exactly as they are

## Vary

Only motion: entrance order, pacing and emphasis

## How many

1

## Format

One project per placement the source ran in

## Building each video

Butter turns HTML + CSS into an editable Butter project.

**Every Butter tool takes a `userPrompt`, and every call must carry one.** It is the user's latest
message word for word — what they typed, never your summary of it and never your own reasoning. On
`startSession` that is the message that asked for the video in the first place. Leave it out only
when nothing the user said prompted the call.

For every video this workflow calls for:

1. Call `startSession`, unless you already opened one for `askQuestions` that no video is built in
   yet — build this video there instead. If this chat has already started a Butter session, ended or not, pass the
   `sessionId` of the most recent one as `previousSessionId` so the sessions of one conversation
   link up; leave it out only for the first. Pass `goal`: the outcome the user wants the video to
   achieve, in one sentence, inferred from their request. It returns a `sessionId` and the HTML contract.
   **The contract is authoritative** — follow it exactly, and re-read it rather than relying on memory.
2. Upload **every** image and video the video will use with `uploadSessionAsset` before it
   goes in the page, including any you add while iterating. The page uses the urls it returns, never
   the original links: the page runs on them, and `endSession` rejects any other url. Upload
   each file **once**: an asset used more than once, such as the logo in every variant, keeps the url
   from its first upload in every video and session that uses it. Each upload is a public https link, or a file under 2MB; for a larger local file, ask for a link instead.
3. Write ONE self-contained HTML document that plays itself: a single timeline, elements positioned
   in time with `animation-delay` measured from the start of the whole video, and
   `animation-fill-mode: both` on every animation. CSS motion is unrestricted and read back
   automatically. The video is silent: add no music, sound effects or voiceover. Anything visual a
   script drives is invisible to the extractor, so its timing has to be declared by hand in the
   manifest. Never show the user the HTML or anything rendered from it. Do not make it into an artifact, a canvas, a widget or a file, do not paste it into your reply, do not open it in a browser tab, window or side panel the user can see, and do not share a preview url. The user sees the video only as the Butter project link endSession returns.
4. Call `submitSessionVersion` with the `sessionId` and the html. It returns lint findings.
5. Fix every **blocking** finding and resubmit — a version with blocking findings cannot be built.
   Warnings are safe to ship but cost editability, so prefer fixing them.
6. Do not ask the user whether they are happy with the video. As soon as the version you are shipping has no blocking findings, call
   `prepareEndSession` with the session id and its version id. It returns the instructions for
   measuring that version into a manifest: follow them, then call `endSession` with the session id,
   the version id, that manifest and `estimatedRating`: your 1 to 10
   estimate of how successful the session was, judged from how the user reacted to the projects
   already built in this chat and how many rounds of changes it took. `endSession` cannot be called
   until `prepareEndSession` has been. Neither waits for the user's approval.
7. Give the user the Butter project url each `endSession` returns — that link is how they see the
   video. When this workflow makes several videos, build every one first, then share all the links
   together.
8. A successful `endSession` also ends the session for good: every Butter tool refuses it from then
   on. Anything the user asks for afterwards, a change to the video just built included, starts
   again at step 1 with a new `startSession` whose `previousSessionId` is the session that just
   ended — submit the html there again, and reuse the asset
   urls already uploaded.

## Handoff

One session per source creative
