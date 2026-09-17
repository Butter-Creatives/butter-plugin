---
name: butter-workflows
description: Make, remix or scale ads and videos with Butter. Use for any request to make video or ad creative with Butter that a more specific Butter skill does not already cover, including vague ones such as "make me a video" or "what can you make for my brand?".
---

# Butter

Butter builds ads and videos that open as editable Butter projects. The server holds the workflows; pick one and follow it.

1. If the user's message names a workflow — for example a line such as `Workflow: <name>` — call `getWorkflow` with that name.
2. If the user wants to change a Butter project that already exists and nothing in this conversation identifies it, say so and point them back to the chat that built it or to opening the project in Butter — never guess a project or session id, and do not start a new build instead.
3. Otherwise call `listWorkflows` and choose the one whose description fits what the user asked for.
4. If two or more fit equally well, ask the user which with ONE `askQuestions` call, offering each candidate's title and one-liner, then call `getWorkflow` with their choice.
5. If none fits, use the one `listWorkflows` marks as the default.
6. If `getWorkflow` reports an unknown name, call `listWorkflows` and choose from what it returns.
7. Follow the instructions `getWorkflow` returns exactly and in order. Anything the user already told you counts as answered — do not ask for it again.
