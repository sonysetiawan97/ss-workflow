---
name: ss-grilling
description: Relentless interview to sharpen a plan or idea into shared understanding, leaving a paper trail in GLOSSARY.md and ADRs. Use when the user wants to think through, plan, or stress-test a feature or decision in a repo. Always writes domain docs as decisions settle.
---

Interview the user relentlessly until you reach a shared understanding. Map this as a **design tree**: every decision branches into the decisions that hang off it. Work the tree in **rounds**; the **frontier** is every decision whose prerequisites are already settled — the questions you can ask *now* without guessing at answers you haven't heard yet. Ask the whole frontier in one round: number each question and give your recommended answer. Then wait for the user's answers before the next round.

Format a round like so:

```
❓ **Q1** - **<question title>**: <question body, might be multiple paragraphs, including multiple choices>

➡️ <your recommended answer>

---

❓ **Q2** - **<question title>**: <question body>

➡️ <your recommended answer>
```

Word each question so "yes" accepts your recommended answer.

Each round the user answers reshapes the tree: settled decisions push the frontier outward and unblock questions that depended on them. Recompute the frontier and ask the next round. A question whose answer depends on another question still open in this round belongs to a *later* round, not this one.

Finding *facts* is your job, never the user's. When a frontier question needs a fact from the environment (filesystem, existing code, tools, etc.), look it up yourself; don't ask the user for anything you could check. Don't block on it: an open fact lookup is an unsettled prerequisite, so only the questions downstream of it wait; ask the rest of the frontier now. The *decisions* are the user's: put each to them and wait.

When the fact is a codebase structure question (what calls what, where a symbol lives, how two modules relate) and `graphify-out/graph.json` exists, answer it with `graphify query "<question>"` before falling back to file reads or grep.

## Writing the docs (always, not optional)

Every session leaves a paper trail. As decisions settle, write them to the repo *now*, not at the end:

1. **Resolve the domain context.** Read `GLOSSARY-MAP.md` if it exists (multi-context repo); otherwise the root `GLOSSARY.md`. If neither exists, use single-context layout: `GLOSSARY.md` at the repo root, ADRs under `docs/adr/`.

2. **Update `GLOSSARY.md` inline, immediately.** When a term is resolved or a fuzzy word is sharpened, write it to `GLOSSARY.md` right there — don't batch. Use the [GLOSSARY-FORMAT.md](../ss-domain-modeling/GLOSSARY-FORMAT.md) format. `GLOSSARY.md` is a pure glossary: term → one or two sentence definition + `_Avoid_` list. No implementation details, no decisions, no scratch notes.

3. **Offer ADRs sparingly.** When a decision meets all three of: (a) hard to reverse, (b) surprising without context to a future reader, (c) result of a real trade-off — offer to record it as `docs/adr/NNNN-slug.md` (next sequential number). Use the template in [ADR-FORMAT.md](../ss-domain-modeling/ADR-FORMAT.md) exactly: 1–3 sentences of context, decision, and why under `Decision`, and "None decided." for an empty section.

4. **Challenge as you go.** If the user uses a term that conflicts with `GLOSSARY.md`, call it out immediately ("your glossary defines 'cancellation' as X, but you seem to mean Y — which is it?"). If the user states how something works and the code disagrees, surface it ("the code cancels entire Orders, but you just said partial cancellation is possible — which is right?"). Stress-test domain relationships with concrete edge-case scenarios.

The session is done when the frontier is empty — every branch of the design tree visited, nothing left silently assumed — AND the paper trail (glossary entries, ADRs) is written. Show the user what landed in `GLOSSARY.md` / `docs/adr/` and confirm the shared understanding before anyone builds.

## It's working if

- The user says "grill me on this idea" and gets numbered rounds with recommended answers, not a wall of text.
- `GLOSSARY.md` gained exactly the terms that were fuzzy during the session — nothing more, nothing less.
- An ADR exists only for decisions that are genuinely hard-to-reverse + surprising + traded-off; trivial choices left unwritten.
- A fresh session can re-read `GLOSSARY.md` + `docs/adr/` and see everything this session decided.
