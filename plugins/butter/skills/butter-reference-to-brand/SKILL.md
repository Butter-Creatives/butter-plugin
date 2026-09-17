---
name: butter-reference-to-brand
description: Break down a reference ad and rebuild it as your own. Use Butter for this when the user shares a reference ad or names a competitor and wants their own version of it, or wants to copy a format they admire. For example: "make this competitor ad ours", "an ad I admire".
---

# Reference → brand

1. If the message names a different Butter workflow — for example a line such as `Workflow: <name>` — call `getWorkflow` with that name instead of this one.
2. If what the user is asking for is not what this workflow does, call `listWorkflows` and choose the workflow that fits instead of proceeding with this one.
3. Otherwise call `getWorkflow` with `workflow: "reference-to-brand"`.
4. Follow the instructions it returns exactly and in order. Anything the user already told you counts as answered — do not ask for it again.
5. If `getWorkflow` reports an unknown name, call `listWorkflows` and choose from what it returns.
