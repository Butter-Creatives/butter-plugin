---
name: butter-make-something-new
description: Make a new ad or video from scratch, or from assets you already have. Use when the user explicitly wants an ad or video built from scratch, or wants photos, clips or product shots they already have turned into an ad — not for a vague request that names no starting point, which butter-ambiguous-start handles.
---

# Make Something New

Make a new ad or video from scratch, or from assets you already have.

## Before you start

Anything the user has already told you, or that the sources under "Gather first" already cover, is known — do not ask for it again; if nothing is missing, go straight to "Gather first". Otherwise ask for what is still missing with `askQuestions`, once, before gathering. It runs inside a session, so call `startSession` first, as in step 1 of "Building each video", and pass its `sessionId`; then pass only the questions below that are still unknown, and add your own for anything else you cannot build without — give an option shaped { "label": "…", "input": "url" } when the answer is a link, { "label": "…", "input": "upload" } when the user should provide a file, and leave out options for free text. Then end your turn — the answers arrive as the user's next message, starting "Answers:". Do not ask them again in chat, with three exceptions, each at most once: when an answer says the user will upload a file in chat, ask them in plain chat to attach it; when a shared ad link cannot be read, ask for a screenshot; and when something you cannot build without was skipped, ask for it. Upload any attached file with `uploadSessionAsset` into that session. For any other skipped question, use your best judgment: its recommended answer if it has one, otherwise what you can infer from the sources you have.

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
7. A successful `endSession` also ends the session for good: the build-session tools refuse it
   from then on, and only `repairProject` and `askNextSteps` still take its ids. Whatever comes
   next starts a session of its own — the asset urls already uploaded stay valid there.
8. `endSession` answers with only the ids: call `repairProject` next with the same ids, before
   telling the user anything. It checks what did not survive conversion. When nothing needs
   repair it routes you to `askNextSteps`; when something does it lists the repairs — make
   ONLY those by driving the editor, then finish on `askNextSteps` with the same ids and
   `repaired`: a line on what was put back. Do not offer the repairs or ask permission.
9. The build report lives in `askNextSteps`.
   Never tell the user the project is open — the card gives them the link.
   Say your one line about the build first, then call `askNextSteps` with those ids as your LAST action
   and end the turn — no text after it: the card it renders carries the link, the report and
   the suggested next steps (Edit this project, Make another version, or Adapt the format). They are suggestions, not
   steps — the user may simply be done; wait for their answer rather than picking one. Edits to the built project go through `startEditSession`. A variant begins with
   `describeProject` — the project as it is NOW, studio edits included, custom blocks named as
   opaque references — then a fresh `startSession` to re-author it, or `cloneProject` +
   `startEditSession` on the clone when the changes are small (a clone keeps custom blocks
   verbatim; a rebuilt variant cannot). Some answers need detail the session cannot start without. Collect it with ONE askQuestions follow-up call — one question per missing detail — and only then start the session:
  - "Add an end card": what the end card should include
  - "Add background music": whether to upload a file, generate it, or choose from stock
  - "Add sound effects": whether to upload a file, generate it, or choose from stock
  - "Add a voice over": whether to upload a file, generate it, or choose from stock
  - "New layout": which layout, with options tailored to this project
  - "Different visual style": which style, with options tailored to this project
Any other answer naming a direction without its detail gets the same treatment, its options tailored to this project.

## Handoff

One session per video; offer variations once its project link is shared, each in a new session
