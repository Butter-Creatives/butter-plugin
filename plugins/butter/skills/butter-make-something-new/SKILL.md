---
name: butter-make-something-new
description: Make a new ad or video from scratch, or from assets you already have. Use Butter for this when the user wants an ad or video built from scratch, wants photos, clips or product shots they already have turned into an ad, or asks for an ad without saying what to start from. For example: "make me an ad", "I have photos and clips".
---

# Make something new

1. If the message names a different Butter workflow — for example a line such as `Workflow: <name>` — call `getWorkflow` with that name instead of this one.
2. If what the user is asking for is not what this workflow does, call `listWorkflows` and choose the workflow that fits instead of proceeding with this one.
3. Otherwise call `getWorkflow` with `workflow: "make-something-new"`.
4. Follow the instructions it returns exactly and in order. Anything the user already told you counts as answered — do not ask for it again.
5. If `getWorkflow` reports an unknown name, call `listWorkflows` and choose from what it returns.
