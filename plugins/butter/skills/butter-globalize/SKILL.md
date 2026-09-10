---
name: butter-globalize
description: Take a winning campaign and launch it in other markets. Use when the user wants an existing campaign translated, localized, or launched in another market.
---

# Globalize a Winner

Take a winning campaign and launch it in other markets.

## Gather first

Butter does not supply this data — it comes from the connectors already available to you, or from
what the user provides.

- The winning creative and its copy
- Target locales, local pricing, offers and any legally required text

If a source is unavailable, say so and ask the user for it rather than inventing figures. Never
invent an offer, a price, a discount, or a performance claim the user did not give you.

## Hold constant

The creative direction, composition and motion

## Vary

Language, price, offer and legal text — and the layout only as much as the language forces

## How many

1

## Format

The source format, per locale

## Building each video

Butter turns HTML + CSS into an editable Butter project. For every video this playbook calls for:

1. Call `startSession`. It returns a `sessionId` and the HTML contract. **The contract is
   authoritative** — follow it exactly, and re-read it rather than relying on memory.
2. Write ONE self-contained HTML document that plays itself: a single timeline, elements positioned
   in time with `animation-delay` measured from the start of the whole video, and
   `animation-fill-mode: both` on every animation. CSS motion is unrestricted and read back
   automatically. JavaScript may play audio; anything visual it drives is invisible to the
   extractor, so its timing has to be declared by hand in the manifest.
3. Call `submitSessionVersion` with the `sessionId` and the html. It returns a preview URL plus
   lint findings.
4. Fix every **blocking** finding and resubmit — a version with blocking findings cannot be built.
   Warnings are safe to ship but cost editability, so prefer fixing them.
5. Give the user the preview URL, and iterate on their feedback by submitting further versions to
   the same session.
6. Only once the user says they are happy, follow the **butter-extract-manifest** skill to measure
   the approved preview, then call `endSession` with the session id, the version id and that
   manifest. Do not call it on your own judgement — the preview is theirs to approve, and a project
   is created every time you call it.

## Handoff

One session per locale; check that longer translations still fit
