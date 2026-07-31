# tres-amigos

```
           _.._                _.._                _.._
         .'    '.            .'    '.            .'    '.
        /   __   \          /   __   \          /   __   \
    ,---|  (__)  |---,  ,---|  (__)  |---,  ,---|  (__)  |---,
   (    '._    _.'    )(    '._    _.'    )(    '._    _.'    )
    '-.._  (__)) _..-'  '-.._  (__)) _..-'  '-.._  (__)) _..-'
          '----'              '----'              '----'
  _____ ____  _____ ____      _    __  __ ___ ____  ___  ____
 |_   _|  _ \| ____/ ___|    / \  |  \/  |_ _/ ___|/ _ \/ ___|
   | | | |_) |  _| \___ \   / _ \ | |\/| || | |  _| | | \___ \
   | | |  _ <| |___ ___) | / ___ \| |  | || | |_| | |_| |___) |
   |_| |_| \_\_____|____/ /_/   \_\_|  |_|___\____|\___/|____/
```

An [Agent Skill](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview) that turns the agile **Three Amigos** practice into a structured debate between AI personas: three perspectives in tension argue a problem, plan, or technical decision across iterations — with a fresh agent every round — until consensus is earned, and a moderator closes with analysis and a recommendation.

## Why

Agents debating each other tend to agree out of politeness, and a debate that converges without friction discovers nothing. This skill is built around three counter-measures: amigos **hold their position** unless a well-founded argument moves them (and must name the argument that did), each amigo declares up front **what evidence would change their mind**, and every round starts with a **fresh agent** reading compact minutes instead of an ever-growing transcript.

## How it works

1. **Banner** — the three sombreros, always. Reproduced byte for byte from `assets/banner.txt`.
2. **Framing** — topic, context, amigos, and parameters extracted from your request.
3. **The debate loop** — each iteration, every amigo (fresh) receives their persona card plus the minutes, answers the strongest arguments against them, concedes what they must, and pushes a shared proposal forward. The moderator updates the minutes, checks for consensus, and poses the crux question if a round stalls.
4. **The verdict** — executive summary, points of consensus, unresolved disagreements, moderator's analysis, recommendation, next steps.
5. **The minutes file** — the full auditable record (every intervention, every round's minutes, the verdict) saved as markdown.

With subagents available (Claude Code, Cowork) each iteration spawns its amigos in parallel; without them (claude.ai) the debate is simulated sequentially. The skill is written in English, but the exercise always runs in **the language of your request**.

## The default trio

- 🏗️ **The Architect** — the best technical solution, optimal design, best practices. Risk: over-engineering.
- 🔍 **The Auditor** — efficient, simple, understandable processes; solves the cause, not the symptom. Risk: undershooting.
- 🤝 **The Client** — real value, reasonable timelines, something the team can actually operate. Risk: underestimating cost.

You can redefine any amigo at invocation time (role, values, references), run with 2–4 of them, or ask for the **classic trio** (Business / Development / QA). See [`references/personas.md`](references/personas.md).

## Install

**Claude Code** — with the [`skills` CLI](https://github.com/vercel-labs/skills) ([docs](https://code.claude.com/docs/en/skills)):

```bash
# personal (all projects)
npx skills add alexanderbotero/tres-amigos -a claude-code -g

# or project-scoped (travels with the repo)
npx skills add alexanderbotero/tres-amigos -a claude-code
```

Or copy the folder into your skills directory by hand:

```bash
# personal (all projects)
git clone https://github.com/alexanderbotero/tres-amigos ~/.claude/skills/tres-amigos

# or project-scoped (travels with the repo)
git clone https://github.com/alexanderbotero/tres-amigos .claude/skills/tres-amigos
```

**claude.ai** — upload the skill from Settings → Capabilities (custom Skills), or package the folder as a `.skill`/ZIP first. See the [Agent Skills overview](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview).

## Usage

```text
I want to run the three amigos exercise to decide how to handle auth in my app:
FastAPI backend, React frontend. Custom JWT or a service like Auth0? Max 3 iterations.
```

```text
tres amigos: valida este plan de migración a GitHub Actions. Cambia al segundo
amigo por un experto en DevOps enfocado en costos. Solo quiero ver el veredicto.
```

Parameters (all optional, stated in plain language in your request):

| Parameter | Default | Options |
|---|---|---|
| `iterations` | 5, or until consensus | any number |
| `detail on screen` | `summary` (minutes per round) | `full`, `verdict only` |
| `minutes file` | yes | say "no file" |
| amigos | default trio | redefine any, 2–4 amigos, or "classic trio" |
| language | language of your request | any |

## Repository structure

```
tres-amigos/
├── SKILL.md              # the exercise: workflow, debate rules, output templates
├── assets/
│   └── banner.txt        # the three sombreros, printed verbatim on activation
└── references/
    └── personas.md       # default persona cards + how to redefine them
```

## Ideas & inspiration

This skill stands on three sets of ideas:

- **[ponytail](https://github.com/DietrichGebert/ponytail)** — the "lazy senior dev" philosophy (lazy = efficient, not careless): solve the cause, not the symptom; the best code is the code never written; boring over clever. Distilled into the Auditor's operating principles.
- **[Ralph Wiggum](https://ghuntley.com/ralph/)** — Geoffrey Huntley's technique of running agents in a loop with a **fresh context every iteration**, keeping state in files rather than in a growing conversation. It inspired this skill's core mechanic: fresh amigos every round, with compact minutes as the only state that travels — no context rot, near-flat token cost.
- **Prompting best practices** — [Anthropic's prompting guide](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices), the [Prompting Guide](https://www.promptingguide.ai/techniques), and [MIT Sloan's effective prompts](https://mitsloanedtech.mit.edu/ai/basics/effective-prompts/). Distilled into the Architect's operating principles, and applied to the authoring of the skill itself.

## About the author

I'm [Alexander Botero](https://github.com/alexanderbotero). This skill is built on the idea that holding your position until the argument is good enough is a feature, not friction — a theme I explore at length in my book **Tenacidad**:

- :gb: [Tenacity: Silent strength, unbreakable power](https://www.amazon.com/dp/9083578127)
- :es: [Tenacidad: Fuerza silenciosa, poder inquebrantable](https://www.amazon.com/dp/9083578100)

## License

[MIT](LICENSE)
