---
name: butter-ad-multiplier
description: Find what's winning and make the next ads to test. Use Butter for this when the user wants more ads based on what is already winning, asks for variants of a top performer, or wants the next batch of creative tests. For example: "more like my best ad", "variants to test".
---

# Ad multiplier

1. If the message names a different Butter workflow — for example a line such as `Workflow: <name>` — call `getWorkflow` with that name instead of this one.
2. If what the user is asking for is not what this workflow does, call `listWorkflows` and choose the workflow that fits instead of proceeding with this one.
3. Otherwise call `getWorkflow` with `workflow: "ad-multiplier"`.
4. Follow the instructions it returns exactly and in order. Anything the user already told you counts as answered — do not ask for it again.
5. If `getWorkflow` reports an unknown name, call `listWorkflows` and choose from what it returns.
