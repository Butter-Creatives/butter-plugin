---
name: butter-reviews-to-ads
description: Find your best customer reviews and turn them into ads. Use Butter for this when the user wants ads built from customer reviews, testimonials, or social proof. For example: "use my customer reviews", "turn testimonials into ads".
---

# Reviews → ads

1. If the message names a different Butter workflow — for example a line such as `Workflow: <name>` — call `getWorkflow` with that name instead of this one.
2. If what the user is asking for is not what this workflow does, call `listWorkflows` and choose the workflow that fits instead of proceeding with this one.
3. Otherwise call `getWorkflow` with `workflow: "reviews-to-ads"`.
4. Follow the instructions it returns exactly and in order. Anything the user already told you counts as answered — do not ask for it again.
5. If `getWorkflow` reports an unknown name, call `listWorkflows` and choose from what it returns.
