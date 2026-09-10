# Butter plugin

Make videos in ChatGPT that open as **editable Butter projects**.

Butter gathers context from the connectors you already have, writes the video as HTML and CSS,
previews it in the conversation, and hands back a project you can keep editing.

## Install in ChatGPT

Plugins need ChatGPT **Plus, Pro, Business, Enterprise or Edu** — they are not available on Free.

1. Open **Settings → Security and login** and turn on **Developer mode**.
2. Go to **Plugins → Add → Add a marketplace**:

   | Field | Value |
   | --- | --- |
   | Source | `https://github.com/Butter-Creatives/butter-plugin.git` |
   | Git ref | *leave blank* |
   | Sparse paths | *leave blank* |

3. Press **Add marketplace**. **Butter (Beta)** appears under the Marketplace tab.
4. Switch to the **Plugins** tab, search **Butter**, and press **+** to install it.

To pick up a new release later, press **Upgrade** on the Butter (Beta) marketplace.

## What you get

| Skill | What it does |
| --- | --- |
| **Ad Multiplier** | Find what's winning and make the next 3 ads to test. |
| **Static → Motion** | Turn the best-performing static ads into motion ads. |
| **Product Catalog → Campaign** | Turn one winning ad into ads for the 10 best-selling products. |
| **Reviews → Ads** | Find the best customer reviews and turn them into 3 ads. |
| **Product Launch** | Launch a campaign for the newest product. |
| **Globalize a Winner** | Take a winning campaign and launch it in other markets. |
| **Reference → Brand** | Break down a reference ad and rebuild it as ours. |
| **Ad Fatigue** | Find ads starting to fatigue and make their replacements. |
| **Measure a preview** | Turns an approved preview into the manifest Butter builds from. |

Ask in your own words — *"find what is working in my Meta ads and make three more to test"* —
and the matching skill takes over.

## Status

**Beta.** The Butter server is not deployed yet, so the skills load and guide the model but the
tools cannot connect. Installing now is still useful for trying the workflows and telling us
where the guidance is wrong.

## This repo is generated

Everything here is written by `yarn plugin:release` in the Butter monorepo (`modules/mcp`).
Editing a `SKILL.md` here will be overwritten by the next release — change
`modules/mcp/src/playbooks/` instead.
