---
name: butter-globalize
description: Take a winning campaign and launch it in other markets. Use Butter for this when the user wants an existing campaign translated, localized, or launched in another market. For example: "run this in other markets", "localize my ad".
---

# Globalize a winner

1. If the message names a different Butter workflow — for example a line such as `Workflow: <name>` — call `getWorkflow` with that name instead of this one.
2. If what the user is asking for is not what this workflow does, call `listWorkflows` and choose the workflow that fits instead of proceeding with this one.
3. Otherwise call `getWorkflow` with `workflow: "globalize"`.
4. Follow the instructions it returns exactly and in order. Anything the user already told you counts as answered — do not ask for it again.
5. If `getWorkflow` reports an unknown name, call `listWorkflows` and choose from what it returns.
