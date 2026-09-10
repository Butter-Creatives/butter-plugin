---
name: butter-catalog-campaign
description: Turn one winning ad into ads for the 10 best-selling products. Use when the user wants ads for many products at once, or wants a winning ad adapted across a catalogue.
---

# Product Catalog → Campaign

Turn one winning ad into ads for the 10 best-selling products.

## Gather first

Butter does not supply this data — it comes from the connectors already available to you, or from
what the user provides.

- Best-selling products with images, titles and prices, from the Shopify connector
- The winning ad to adapt

If a source is unavailable, say so and ask the user for it rather than inventing figures. Never
invent an offer, a price, a discount, or a performance claim the user did not give you.

## Hold constant

The winning format, layout and typography

## Vary

Product image, name, price and any product-specific claim

## How many

10

## Format

The source ad's format

## Building each video

Butter turns HTML + CSS into an editable Butter project. For every video this playbook calls for:

1. Call `startSession`. It returns a `sessionId` and the HTML contract. **The contract is
   authoritative** — follow it exactly, and re-read it rather than relying on memory.
2. Write ONE self-contained HTML document that plays itself: a single timeline, elements positioned
   in time with `animation-delay` measured from the start of the whole video, and
   `animation-fill-mode: both` on every animation. CSS motion is unrestricted. JavaScript may play
   audio but must never animate or restyle.
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

One session per product
