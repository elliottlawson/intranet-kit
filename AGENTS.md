# intranet-kit

When the user asks to set up an intranet, read `skills/configure-intranet/SKILL.md` and start its interview. The user should not need to know the skill names or write a manifest first.

This repository is the kit root containing the reusable skill definitions under `skills/`. Generated user state belongs in the working project as `manifest.md` and `themes.config.json`.

Load the downstream skill named by the current flow. Keep the user-facing experience in natural language and let the skills own the infrastructure details.
