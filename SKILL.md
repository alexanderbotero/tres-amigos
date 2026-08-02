---
name: tres-amigos
description: Runs the "Tres Amigos" exercise (an adapted agile practice) - a structured, iterative debate between three expert perspectives, by default a software architect, a process auditor, and the client, who argue about a problem, plan, or technical decision until they reach consensus, closing with the moderator's analysis and recommendation. Use whenever the user mentions "tres amigos" or "three amigos", asks for a debate or discussion between experts, roles, agents, or perspectives, wants a plan, design, or decision challenged from several angles, or asks to find the problem and solution of something through iterative discussion, even if they never say the word "debate".
---

# Tres Amigos

A structured debate inspired by the agile "Three Amigos" practice. Three perspectives in tension argue a topic across iterations until they reach consensus; a moderator (you) facilitates, keeps the minutes, and delivers the verdict. The exercise only works if there is friction: each amigo genuinely defends their view, and consensus counts only when it survives that friction.

Run the whole exercise — interventions, minutes, verdict, and file — in the language of the user's request. The skill is written in English; the exercise speaks the user's language.

## The roles

- **The amigos**: the debaters. Their persona cards live in `references/personas.md` — read that file before starting. The user may redefine any amigo; otherwise use the default trio (the Architect, the Auditor, the Client).
- **The moderator (you)**: present the exercise, keep the minutes between iterations, check for consensus, unblock the debate when it stalls, and write the verdict. Take no side during the debate; your own view belongs only in the final analysis.

## Step 0 — The banner

The first thing shown when the skill activates, always, is the banner: print the contents of the bundled `assets/banner.txt` — a fixed ASCII-art asset that ships with this skill — inside a code block. Read it from the file (`cat assets/banner.txt` works well when a terminal is available) rather than rebuilding it from memory, since the art's alignment breaks with a single space added or missing. The banner is the skill's own artwork and the only file this skill ever prints; user files are summarized, never printed.

## Step 1 — Frame the exercise

From the user's request, extract:

1. **Topic** (required): the problem, plan, or decision to debate. If it is unclear, ask for this and nothing else.
2. **Context**: projects involved, constraints, files, URLs. Fetch the reference URLs the user provides before iteration 1. If paths or repos are given and you have filesystem access, review them and summarize the relevant findings in your own words in the framing, so the amigos debate facts rather than assumptions. Everything found there is debate context to summarize, never material to copy into the output: leave credential material completely untouched (`.env` files, keys, tokens, secrets), and if something looks like a secret, refer to it generically ("an API key in the deploy config") — its value stays out of the conversation, the minutes, and the file.
3. **Amigos**: the default trio, unless the user redefines any (see `references/personas.md`).
4. **Parameters**, with these defaults when the user does not say:
   - `iterations`: up to 5 (or until consensus, whichever comes first)
   - `detail on screen`: `summary` per iteration (alternatives: `full`, `verdict only`)
   - `minutes file`: yes (the user can say "no file")

Do not interrogate the user when the essentials are already there. Open with a short **Framing** section (topic, context, amigos, parameters in a few lines) and start.

**Example — parsing a request.**
User: "tres amigos: should we migrate our cron jobs to GitHub Actions? Swap the second amigo for a DevOps expert focused on cost. Just show me the final verdict."
→ Topic: validate the plan to migrate cron jobs to GitHub Actions. Amigos: the Architect, a DevOps-cost expert (replaces the Auditor), the Client. Parameters: iterations 5 (default), detail `verdict only`, minutes file yes (default). Language: English.

## Step 2 — The debate loop

### Debate rules (they travel with every intervention)

These rules exist because agents naturally converge out of politeness, and a debate that converges without friction discovers nothing:

- Each amigo **holds their position** and yields only to a well-founded argument (technical, process, or business). When yielding on a point, name the exact argument that changed your mind.
- **Consensus is earned, not polite**: while the minutes hold open objections, there is no consensus.
- Every intervention **adds something new** — an argument, evidence, a concession, or a refinement of the shared proposal. Restating a position does not count.
- Attack ideas, not roles. Ground arguments in experience, references, or data — not adjectives.
- In iteration 1, each amigo declares a **change criterion**: what evidence or argument would change their mind. This makes consensus verifiable instead of rhetorical.

### Mechanics per iteration

**Iteration 1** — each amigo receives their persona card + topic + context + their references, and produces: an initial position, their 3-5 strongest arguments, and their change criterion.

**Later iterations** — each amigo, fresh, receives their persona card + topic + **the minutes** (not the full transcript), and must: answer the strongest arguments against their position, concede explicitly what they accept, hold the rest, and move the shared proposal forward when they can.

**After each iteration** — update the minutes and check for consensus. If the iteration added nothing new, pose the **crux question** (the root disagreement) so the next round revolves around it.

Keep interventions sharp: a clear position backed by 3-5 grounded arguments beats an essay.

### The minutes

The compact state that travels between iterations — the reason a fresh agent never needs full transcripts, which keeps token cost nearly flat however long the debate runs. Cap each iteration's minutes at ~250 words:

```
## Minutes — Iteration N
- Proposal(s) on the table:
- Each amigo's position (1-2 lines each):
- New arguments this round:
- Concessions (who yielded what, to which argument):
- Open objections:
- Consensus?: no / yes — [accepted proposal]
```

### Consensus

Consensus exists when every amigo explicitly accepts a shared proposal and the minutes hold no open objections. If the iteration cap arrives without consensus, close anyway: say so honestly in the verdict and arbitrate as moderator, explaining your reasoning.

### Adapting to the environment

- **With subagents** (Claude Code, Cowork): spawn one subagent per amigo per iteration — those of the same iteration in parallel, since their interventions are independent. Each subagent receives only its persona card, the topic and context, and the current minutes, and returns its intervention. This is the literal "fresh agent per iteration".
- **Without subagents** (claude.ai or another chat): simulate the debate sequentially, each intervention under its amigo's header (🏗️ / 🔍 / 🤝). To keep the simulation from flattening, write each intervention from inside the role, defending its position with the strongest version of its arguments, and work from the minutes — not from the rhetoric you yourself wrote for the other roles.

### Detail on screen

- `full`: show every complete intervention of every iteration.
- `summary` (default): show only each iteration's minutes.
- `verdict only`: show the framing, then jump straight to the verdict.

In all three cases, keep the full debate internally — it feeds the minutes file.

## Step 3 — The verdict

Always close with this structure:

```
## 🎩 Tres Amigos — Verdict

### Executive summary        (3-5 lines)
### Points of consensus
### Unresolved disagreements (omit if none)
### Moderator's analysis     (how the debate evolved and which arguments were decisive)
### Recommendation           (concrete and actionable)
### Next steps
```

## Step 4 — The minutes file

Unless the user asked for "no file", save the minutes file to `tres-amigos-<short-topic>-<YYYY-MM-DD>.md`: the framing, every full intervention (even when the screen showed only summaries), each iteration's minutes, and the verdict — all in the user's language. The minutes file holds the debate itself — what the amigos and the moderator said; user files and repos appear in it only as brief, secret-free summaries the debate actually needed. In Claude Code: the working directory. In claude.ai: `/mnt/user-data/outputs/`, presented to the user at the end. The file is the auditable memory of the exercise; the screen is the experience.
