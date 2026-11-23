---
name: statechart-explain
description: "Explain a statechart to the person who must decide on it: split a busy chart into one small life per piece, then show where the pieces cross as concrete cases to test, the rules in plain words, and what exists versus what to build. Use after /statechart when the reader must understand the behavior, when a chart has more than about 20 arrows, or when the user asks what a chart means."
---

# Statechart explain

A statechart from `/statechart` is right, but a chart of a system with several independent pieces is their cross product: each step is drawn again for every combination of the other pieces. Twenty states and fifty arrows is a wall the reader cannot judge. This skill turns the same model into an explanation the reader can check in a few minutes.

Write everything the reader sees in the reader's language and words. Keep TLA+ names, variable names, and action names out of it.

## Pick the form

Count the arrows in `chart.mmd`.

- One main piece and about 15 arrows or fewer: show the chart itself, with colors and numbered notes. Go to [Annotate a small chart](#annotate-a-small-chart).
- More pieces, a regions chart, nested parent states, or more arrows: split it. Go to [Split into lives](#split-into-lives). Keep the full chart only as an appendix.

## Split into lives

1. **Find the pieces.** A piece is a variable, or a group of variables, that answers one question the reader asks, such as "does the pass still work?", "where is the person?", "has HR checked the company?". Leave out counters and bookkeeping that only serve the rules, such as a day counter or a list of flagged visits.
2. **Draw each life.** For each piece, write a view whose `state()` returns only that piece's label, and run `/statechart` again with it and a new `--out`. Each chart now has a handful of states and arrows. A step that does not change the piece disappears, as it should.
3. **Say who does each step.** Give every arrow its actor: the person, the back office, the system on its own. Pick one color per actor and keep it on every arrow, in the lives and in the cases. Steps that leave the piece as it is, but that the reader must know about, go under the state as a "stays the same:" line.
4. **Lay them out side by side**, one column per life, read top to bottom, with a legend of the actors. On a phone the columns stack.

## Find the crossings

The real work, and almost all of the manual testing, sits where the pieces meet. A crossing is one of these:

- a step that changes two pieces at once, such as "end of day" closing a visit and flagging it;
- a step that one piece allows only in some states of another, such as "scan in" only with an active pass;
- a rule that names two pieces, such as "inside only with a valid pass".

Find them in the model: read the guards and the effects of each action, and the rules. Each crossing becomes a case:

- a short title in the reader's words, such as "HR revokes the pass while they are inside";
- a scene with one invented, concrete person and times, the same person across all cases, such as "Marco, of Rossi Impianti, registers at 7:50";
- what happens, step by step, and what the reader will see;
- the rules it touches, by number.

When the model had to choose something the requirement does not say, mark that case as a decision for the reader, state the choice the model made, and say what would change if they chose the other way. Aim for 4 to 8 cases. These cases are the manual test list.

## Write the rules

Number the INVARIANTs and write each as one sentence the reader would sign, such as "Nobody is left inside at night". Say how many states the check covered. Cases point to rules by number.

## Say what exists and what to build

When the system sits on a product, such as an ERP, split the steps into two lists: what the product already does out of the box, and what has to be built. Back each "already there" item with a source you checked, not with memory.

## Annotate a small chart

1. Render the chart with Pele, as `/statechart` says in its `references/render.md`.
2. Color states by what they mean to the reader, with a `classDef` per meaning: blocked, ready, done, closed; or normal, needs attention. Pele colors only classes that have a `classDef`.
3. Clean the labels: drop repeated prefixes from parent and region names.
4. When the chart hides a fact the reader needs, such as a flag that no state shows, add it to the arrow label, and mark every note you add as yours.
5. Write 3 to 6 numbered notes under the chart: what one box is, what the colors mean, how to read it (top to bottom, the parallel paths), which rules show as missing arrows, and what the chart leaves out.

## Show the full chart, if at all

Put the full chart at the end, behind a fold, annotated as above. Say why it looks big: list each step with how many times it is drawn, such as "end of day: 15 arrows". This is what tells the reader that the split loses nothing.

## Hand off

Lead with a strip of numbers the reader can hold: pieces, steps between them, crossings to test, rules and the states checked. Then the lives, the cases, the rules, exists versus build. Give the views and the command that make each chart again.
