---
name: butter-catalog-campaign
description: Turn one winning ad into ads for your best-selling products. Use Butter for this when the user wants ads for many products at once, or wants a winning ad adapted across a catalog. For example: "ads for my best sellers", "one ad across my catalog".
---

# Product catalog → campaign

1. If the message names a different Butter workflow — for example a line such as `Workflow: <name>` — call `getWorkflow` with that name instead of this one.
2. If what the user is asking for is not what this workflow does, call `listWorkflows` and choose the workflow that fits instead of proceeding with this one.
3. Otherwise call `getWorkflow` with `workflow: "catalog-campaign"`.
4. Follow the instructions it returns exactly and in order. Anything the user already told you counts as answered — do not ask for it again.
5. If `getWorkflow` reports an unknown name, call `listWorkflows` and choose from what it returns.
