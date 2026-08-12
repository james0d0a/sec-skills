Skills are organized into bucket folders under `skills/`:

- `investigation/` — investigative workflow tools
- `sec-architecture/` — risk and environment workflow tools
- `utility/` — not tied to a specific category

Every skill is a directory containing `SKILL.md`, plus any templates or reference
docs it owns. Adding or moving a skill means updating three places: the bucket
folder, the `skills` array in `.claude-plugin/plugin.json`, and the list in
`README.md`.

Skills in this repo are self-contained. A skill may reference another skill in
this repo by its `/name`, but must not depend on a skill from any other plugin.
