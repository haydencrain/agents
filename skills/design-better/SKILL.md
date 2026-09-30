---
name: design-better
description: "Produce studio-grade mockups by driving the `design` canvas skill through a deliberate process: intake questions, seed-string and ambitious-direction exploration, a single critic-agent pass with suggestions applied automatically, then a subtraction pass and an AI-tells sweep. Use when asked to 'design better', '/design-better', or when a mockup should look like a real product rather than a generic AI layout."
---

# Design Better

You are producing a mockup with the `design` skill (Claude Design canvas), but you do not go straight to drawing. LLMs default to the most probable design — safe, symmetrical, committee-flavoured. This skill exists to push past that default, then edit back to something restrained and specific.

Process, adapted from Anshu Chimala's "How to turn your AI into a world-class designer":

1. **Intake** — ask what is actually wanted
2. **Discover** — seed string + ambitious directions
3. **Build** — render on the canvas via the `design` skill
4. **Critique** — one critic subagent, suggestions applied automatically
5. **Deliver** — subtraction pass, then AI-tells sweep
6. **Hand off** — final canvas plus a short log

## Phase 1: Intake

Ask **one question at a time**. Do not assume; if the user already answered something in their request, skip it. Suggest options where helpful, but only build on what the user actually says.

Establish all six:

| # | Question | Why it matters |
|---|---|---|
| 1 | **What is it?** App screen / flow, landing page, marketing graphic, print piece, doc… | Decides artboard type, count, and dimensions |
| 2 | **Fidelity?** Rough wireframe vs. polished, production-looking mockup | Wireframe skips Phase 2 exploration and most of Phase 5 polish |
| 3 | **Platform / context?** iOS, Android, web desktop, mobile web, print… | Sets native conventions the critic and AI-tells sweep judge against |
| 4 | **Existing constraints?** Brand, design system, reference designs, fonts, colours it must respect | Constraints dial *down* exploration — a seed string never overrides a brand |
| 5 | **How adventurous?** "Safe and conventional" ↔ "surprise me" | Controls how hard Phase 2 is pushed and how many directions are generated |
| 6 | **The bar?** What "done" looks like — e.g. "Apple-native", "Linear-level polish", "good enough for a stakeholder demo" | Becomes the critic's scoring reference |

Summarise the answers back in 3–5 lines before moving on. That summary is the **brief** and is passed to every later phase.

## Phase 2: Discover

Skip this phase entirely if fidelity is *wireframe* or adventurousness is *safe* **and** there are strong brand constraints. Otherwise:

### 2a. Seed string

Generate a random 12–16 character alphanumeric string (e.g. `k7Qz2mXp9vLw4Tn`). Treat it as a source of inspiration: let its shape, letter/number mix, and rhythm nudge choices about palette, type, density, and layout. The point is to break the default, not to make the string visible. Never let the seed override a constraint from the brief.

### 2b. Ambitious directions

Produce **3–5 concrete design directions** (fewer when adventurousness is low). Each must be vivid and specific — a direction is something you could hand to a human designer and get a distinctive result:

- Good: "Editorial broadsheet — dense serif type, hairline rules, black-and-white with a single ochre accent, content bleeds off the right edge"
- Good: "Isometric control room — every panel is a 3D block, monospace labels, one saturated signal colour on graphite"
- Bad: "Modern and clean", "minimal", "bold and colourful"

Some directions should feel slightly wrong on paper. Those are often the ones that work.

Present the directions to the user and ask which one (or which combination) to pursue. Note their reaction — "I like the second but less glow" is exactly the signal you want. Then **write the final design prompt yourself**: one paragraph merging the brief, the chosen direction, and the user's reaction. Show it to the user only if they ask; otherwise proceed.

## Phase 3: Build

Invoke the `design` skill and produce the artboards described by the design prompt. Follow the `design` skill's own conventions for artboard files, canvas layout, and publishing.

While building, already apply the discipline from Phase 5 — do not add decoration you know will be removed later. In particular use **real, specific content**: product-specific nouns, plausible numbers, names that are not Jane Doe / Acme.

## Phase 4: Critique

Spawn **one** critic subagent with the Agent tool (`subagent_type: "general-purpose"`). Exactly one pass — no loop.

Give the critic:

1. The **rendered artboard(s)** — prefer screenshots (render the artboard in the Browser pane and screenshot it); fall back to the artboard source only if screenshots are not possible, and tell the critic to judge the visual result, not the code
2. The **brief** from Phase 1, especially the bar (question 6) and platform (question 3)
3. The following instructions, verbatim:

> You are a senior design critic at a top studio. You have not seen how this was built and you do not care. Judge only what a user would see.
>
> Score the design **0–10** against this bar: `<bar from brief>`. 10 means a top studio would ship this unchanged. 5 means competent but generic. Be honest; most first drafts are 4–6.
>
> Then list the **gaps** between this design and a 9–10 — concrete, observable things ("the three feature cards have identical visual weight so nothing is primary", "hero headline is 48px on a 375px viewport and wraps to four lines"). Prioritise: the 3–7 changes that would most raise the score. For each, give a specific suggested fix.
>
> Do not praise. Do not suggest adding decoration. If the design has *too much*, say what to remove.
>
> Return exactly:
> ```
> Score: <n>/10
> Biggest problem: <one sentence>
> Gaps (prioritised):
> 1. <gap> → <fix>
> 2. ...
> ```

Apply the critic's suggestions **automatically** — all of them unless one directly contradicts a constraint from the brief, in which case skip it and note why in the hand-off log. Republish the canvas.

## Phase 5: Deliver

### 5a. Subtraction pass

AI adds; good design subtracts. Go through every artboard and remove anything that does not earn its place:

- Decorative effects with no functional role — glows, blurred blobs, gradient borders, ambient shadows
- Redundant labels — headers on things that are self-evident, captions that repeat the visual
- Over-explanation — a sentence where a phrase would do; a phrase where a number would do
- A third thing where one thing would do — three CTAs, three badges, three adjectives
- Elements that exist only to fill space

Test: if you remove it and the design gets *calmer* without losing meaning, it should go. Restraint is what reads as premium.

### 5b. AI-tells sweep

Read [references/ai-tells.md](references/ai-tells.md) and check every artboard against both the **visual** and **language** lists. Fix every hit. The meta-tell to ask about at the end of each artboard:

> Could this design or this copy be swapped onto a competitor's product with zero edits?

If yes, it is not done. Make it specific to *this* thing.

Republish the canvas.

## Phase 6: Hand off

Give the user the canvas link and a short log, in this shape:

```
Direction: <chosen direction, one line>
Critic: <score>/10 — <biggest problem>
Applied: <3–7 bullets of what changed after critique>
Removed: <bullets from the subtraction pass>
AI-tells fixed: <bullets>
Skipped: <any critic suggestion not applied, with reason — omit if none>
```

Then stop. Do not offer another critic round unless asked; further iteration happens in the canvas editor or via a new request.

## Principles that hold across every phase

- **Constraints beat exploration.** A brand rule from the brief always wins over a seed string or a direction.
- **Specific beats generic.** Vivid directions, real content, product-specific verbs on buttons.
- **The model cannot judge its own work.** That is why the critic is a separate agent that has not seen the build.
- **Subtract before you add.** When something looks off, the first question is "what can go?", not "what's missing?".
- **Keep the odd prompts.** If a direction felt wrong but produced something interesting, mention it in the hand-off — it is worth retrying with a newer model.
