---
name: butter-ambiguous-start
description: Start a Butter ad or video when the request is too vague to pick a workflow, such as asking for an ad without saying what to start from. Use when the user wants an ad or video from Butter and no other butter- skill clearly fits.
---

# Start with Butter

The user wants an ad or video from Butter, but it is not yet clear which workflow fits.

## Find the workflow

1. If the request clearly fits one of the workflows below, tell the user which one and follow that skill.
2. Otherwise ask one question — "What are you starting from?" — offering these:
   - **Something new** → `butter-make-something-new`, `butter-product-launch`, or `butter-reviews-to-ads`
   - **An ad of mine that's working** → `butter-ad-multiplier`, `butter-static-to-motion`, `butter-catalog-campaign`, `butter-globalize`, or `butter-ad-fatigue`
   - **A competitor or an ad I admire** → `butter-reference-to-brand`
   - **My own photos or clips** → `butter-make-something-new`
3. If their answer leads to more than one workflow, ask which goal they are after, offering each
   workflow's one-liner. If none fits, use `butter-make-something-new`.
4. Follow the chosen workflow's skill. Its "Before you start" section does not ask again for anything
   this conversation already answered.

## Workflows

### Make Something New — `butter-make-something-new`

Make a new ad or video from scratch, or from assets you already have. Use when the user explicitly wants an ad or video built from scratch, or wants photos, clips or product shots they already have turned into an ad — not for a vague request that names no starting point, which butter-ambiguous-start handles.

### Ad Multiplier — `butter-ad-multiplier`

Find what's winning and make the next 3 ads to test. Use when the user wants more ads based on what is already winning, asks for variants of a top performer, or wants the next batch of creative tests.

### Static → Motion — `butter-static-to-motion`

Turn the best-performing static ads into motion ads. Use when the user wants to animate an existing static ad, turn stills into video, or add motion without changing a design.

### Product Catalog → Campaign — `butter-catalog-campaign`

Turn one winning ad into ads for the 10 best-selling products. Use when the user wants ads for many products at once, or wants a winning ad adapted across a catalogue.

### Reviews → Ads — `butter-reviews-to-ads`

Find the best customer reviews and turn them into 3 ads. Use when the user wants ads built from customer reviews, testimonials, or social proof.

### Product Launch — `butter-product-launch`

Launch a campaign for the newest product. Use when the user is launching a new product and needs a set of launch creative across surfaces.

### Globalize a Winner — `butter-globalize`

Take a winning campaign and launch it in other markets. Use when the user wants an existing campaign translated, localized, or launched in another market.

### Reference → Brand — `butter-reference-to-brand`

Break down a reference ad and rebuild it as ours. Use when the user shares a reference ad or names a competitor and wants their own version of it, or wants to copy a format they admire.

### Ad Fatigue — `butter-ad-fatigue`

Find ads starting to fatigue and make their replacements. Use when the user asks which ads are declining, mentions creative fatigue, or wants replacements for tiring ads.
