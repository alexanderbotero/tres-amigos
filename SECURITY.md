# Security

`tres-amigos` is an instructions-only skill: markdown plus one ASCII-art asset. No executable code ships with it.

## Boundaries the skill enforces on itself

These are written into `SKILL.md` and travel with every run:

- **One printable file.** The only file the skill ever prints is its own bundled `assets/banner.txt` (fixed ASCII art). User files are summarized, never printed.
- **Credential material is off-limits.** When reviewing user-provided paths or repos for debate context, findings are summarized in the agent's own words. `.env` files, keys, tokens, and secrets are left completely untouched; anything that looks like a secret is referenced only generically, and its value stays out of the conversation, the minutes, and the file.
- **The minutes file holds the debate, not your files.** It contains the framing, the amigos' interventions, the per-iteration minutes, and the verdict. User files and repos appear in it only as brief, secret-free summaries the debate actually needed.
- **No autonomous network activity.** The skill fetches only URLs the user explicitly provides in their request, as debate references. It phones nowhere on its own.

## Audit status

Third-party audits on skills.sh run server-side. These boundaries were introduced in commit `5e1449f` and their wording aligned across all repo files in the follow-up commit, in response to Snyk finding W007.

## Reporting

Found a way these boundaries could fail? Open an issue on this repository.
