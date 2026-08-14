---
layout: post
title: "The harness outlives the model"
date: 2026-08-12 07:00:00 -0700
categories: design-engineering
---

<style>
figure.diagram{width:100vw;max-width:100vw;margin:2.6rem 0 2.6rem calc(50% - 50vw);padding:1.5rem 1rem;background:#FCFCFC;border-top:1px solid #BEBEBE;border-bottom:1px solid #BEBEBE;box-sizing:border-box;}
figure.diagram svg{display:block;width:100%;max-width:1500px;margin:0 auto;height:auto;}
figure.diagram svg text{font-family:"Atkinson Hyperlegible Next",sans-serif;}
figure.diagram figcaption{max-width:1500px;margin:.9rem auto 0;font-size:.82rem;font-style:italic;color:#606060;line-height:1.5;}
</style>

*Everyone is optimising prompts for a model that will be deprecated inside a year. The thing worth building is the harness around it: a real working setup, made of files on disk, that a model plugs into and out of without taking the practice with it. This is how mine is built, what it is made of, and what it took two years and a couple of rebuilds to get right.*

**Executive summary**

- **A prompt is tuned to a model. A harness is not.** Every hour spent tuning phrasing to one model's quirks is written off the day it is deprecated. The same hour spent on a file the next model reads just as well is not. That is the whole reason to build the thing.
- The substrate is **ICM — Interpretable Context Methodology**: folder structure as agent architecture, from Jake Van Clief and David McDermott. Five forms, a contract in every folder, reading order as build order. Not my invention, and the part of this with real validation behind it.
- Ask one question of every rule in your design system: **can a machine check it?** That question sorts everything else, and it sorts it into two destinations, not one folder.
- Most of what we write down passes. Spacing, contrast, tokens, theme, semantics. All of it should be compiled into something you cannot type wrong, and stop being prose.
- What survives is pattern choice. Accordion or list, card or row, modal or panel. That is the only part that needs real judgment, and it is far thinner than the conversation about AI and design context suggests.
- That surviving part gets a format rather than a folder of documents. A **judgment record**: a pattern, the condition it applies under, the principle and citation justifying it, and the alternatives it beat. Schema, records, research corpus, and a matcher that resolves a condition the same way every time. The repo is **cairn**.
- Three things make the format work rather than just exist. Precedence, so a tie between two records has an answer. Retrieval at the moment of the decision instead of a load at the start of a session. And a hard stop when nothing matches, because a generator that keeps going falls back on the model's own priors, quietly and at full confidence. That is where drift comes from.
- The gate is where this argument usually goes vague. Models generate and models score. A plain function decides. Nothing that generates gets a vote on its own output.
- The folder forms turn out to be memory registers under a different name — which is the strongest sign the shape is right rather than merely tidy.


## The model is the disposable part

Sit with the release cadence for a second. The model you have tuned your prompts against will be deprecated, probably within the year, and the one replacing it will have different quirks, a different context budget, and a different set of things it silently assumes when you do not tell it otherwise. Every hour you spent on phrasing goes with it.

The harness does not go with it. A folder that declares what it holds and what a person has to check before its output can be trusted reads the same to whatever is on the other end. A JSON record with a condition and a citation is not a prompt technique. A threshold function that decides pass or revise or fail does not care which model produced the score it is thresholding. Swap the engine and the practice survives, because the practice was never in the engine.

> A prompt is tuned to a model. A harness is what the model plugs into.

That is the actual argument for building one, and it is a stronger argument than the one usually made for it. The usual pitch is quality — better context, better output. Fine, and probably true. The durable pitch is that this is the only part of your AI practice that accrues instead of resetting.

So: the advice everywhere right now is to document more. The patterns, the states, the accessibility rules, the intent. Put it all somewhere the agent can reach and the output stops looking generic.

I have spent two years building exactly that, and I think it is half wrong. Not because context does not help. It does. But most of what we call design context should never have been documentation in the first place, and treating it that way is a big part of why design systems fall apart once AI is writing the code.

Here is what I had been running, and roughly what most teams land on once they take this seriously. Sources of truth on the outside, feeding a guidance layer. Guidance tiered instead of piled flat, because sooner or later two rules contradict each other and one of them has to win. An execution layer that is honestly interchangeable. Two gates instead of one, because tests and CI check whether the code runs, not whether it was worth building. And a loop back, because a system that cannot learn from what it shipped goes stale.

<figure class="diagram" role="img" aria-label="The harness as a loop: sources of truth feed a tiered guidance layer, which feeds execution, which passes two gates, with a capture path returning to guidance.">
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1720 631" data-w="1720" data-h="631" role="img" aria-label="The design harness redrawn as a loop."><defs><marker id="aG" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="6.5" markerHeight="6.5" orient="auto-start-reverse"><path d="M0,1 L9,5 L0,9 z" fill="#8C8C8C"/></marker><marker id="aP" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="6.5" markerHeight="6.5" orient="auto-start-reverse"><path d="M0,1 L9,5 L0,9 z" fill="#4D6FF2"/></marker><marker id="aT" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="6.5" markerHeight="6.5" orient="auto-start-reverse"><path d="M0,1 L9,5 L0,9 z" fill="#0E6D75"/></marker><marker id="aC" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="6.5" markerHeight="6.5" orient="auto-start-reverse"><path d="M0,1 L9,5 L0,9 z" fill="#B02016"/></marker><marker id="aK" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="6.5" markerHeight="6.5" orient="auto-start-reverse"><path d="M0,1 L9,5 L0,9 z" fill="#8C1EB3"/></marker><filter id="sh" x="-20%" y="-20%" width="140%" height="140%"><feDropShadow dx="0" dy="2" stdDeviation="3" flood-color="#010101" flood-opacity="0.07"/></filter></defs><style>text{font-family:'Atkinson Hyperlegible Next', ui-sans-serif, system-ui, sans-serif;}.lbl{font-size:11.5px;font-weight:700;letter-spacing:.09em;text-transform:uppercase;}.hd{font-size:12.5px;font-weight:700;letter-spacing:.14em;text-transform:uppercase;}</style><text x="70" y="52" class="hd" fill="#010101">The design harness</text>
<rect x="46" y="72" width="1306" height="543" rx="16" fill="none" stroke="#BEBEBE" stroke-width="1.5" stroke-dasharray="7 6"/>
<text x="70" y="255.0" class="lbl" fill="#2C38F5">Source of truth</text>
<rect x="70" y="267.0" width="252" height="181" rx="10" fill="#FCFCFC" stroke="#2C38F5" stroke-width="1.5" filter="url(#sh)"/>
<text x="88" y="303.0" font-size="13.5" fill="#242424" font-weight="500">Codebase</text>
<text x="88" y="324.0" font-size="13.5" fill="#242424" font-weight="500">Tokens</text>
<text x="88" y="345.0" font-size="13.5" fill="#242424" font-weight="500">Storybook</text>
<text x="88" y="366.0" font-size="13.5" fill="#242424" font-weight="500">Figma</text>
<text x="88" y="387.0" font-size="13.5" fill="#242424" font-weight="500">Shipped product</text>
<text x="88" y="406.0" font-size="12.5" fill="#606060" font-weight="400">It moves whether or not you</text>
<text x="88" y="425.0" font-size="12.5" fill="#606060" font-weight="400">update the docs.</text>
<text x="402" y="238.0" class="lbl" fill="#2C38F5">Guidance</text>
<rect x="402" y="250.0" width="252" height="215" rx="10" fill="#FCFCFC" stroke="#2C38F5" stroke-width="1.5" filter="url(#sh)"/>
<text x="420" y="286.0" font-size="13.5" fill="#242424" font-weight="500">1 · Constraints</text>
<text x="420" y="305.0" font-size="12.5" fill="#606060" font-weight="400">Hard rules. Flagged on</text>
<text x="420" y="324.0" font-size="12.5" fill="#606060" font-weight="400">violation.</text>
<text x="420" y="345.0" font-size="13.5" fill="#242424" font-weight="500">2 · Decisions</text>
<text x="420" y="364.0" font-size="12.5" fill="#606060" font-weight="400">The choice, the why, when to</text>
<text x="420" y="383.0" font-size="12.5" fill="#606060" font-weight="400">revisit.</text>
<text x="420" y="404.0" font-size="13.5" fill="#242424" font-weight="500">3 · Context</text>
<text x="420" y="423.0" font-size="12.5" fill="#606060" font-weight="400">Informs. Does not constrain.</text>
<text x="420" y="442.0" font-size="12.5" fill="#606060" font-weight="400">Precedence: 1 beats 2 beats 3.</text>
<text x="734" y="265.5" class="lbl" fill="#2C38F5">Execution</text>
<rect x="734" y="277.5" width="252" height="160" rx="10" fill="#FCFCFC" stroke="#2C38F5" stroke-width="1.5" filter="url(#sh)"/>
<text x="752" y="313.5" font-size="13.5" fill="#242424" font-weight="500">Claude Code</text>
<text x="752" y="334.5" font-size="13.5" fill="#242424" font-weight="500">Cursor</text>
<text x="752" y="355.5" font-size="13.5" fill="#242424" font-weight="500">Codex</text>
<text x="752" y="376.5" font-size="13.5" fill="#242424" font-weight="500">Sandbox</text>
<text x="752" y="395.5" font-size="12.5" fill="#606060" font-weight="400">Interchangeable. The least</text>
<text x="752" y="414.5" font-size="12.5" fill="#606060" font-weight="400">durable layer here.</text>
<text x="1066" y="267.5" class="lbl" fill="#B02016">Gates</text>
<rect x="1066" y="279.5" width="252" height="156" rx="10" fill="#FCFCFC" stroke="#B02016" stroke-width="1.5" filter="url(#sh)"/>
<text x="1084" y="315.5" font-size="13.5" fill="#242424" font-weight="500">Build gate</text>
<text x="1084" y="334.5" font-size="12.5" fill="#606060" font-weight="400">Tests, types, security,</text>
<text x="1084" y="353.5" font-size="12.5" fill="#606060" font-weight="400">branching, CI.</text>
<text x="1084" y="374.5" font-size="13.5" fill="#242424" font-weight="500">Design gate</text>
<text x="1084" y="393.5" font-size="12.5" fill="#606060" font-weight="400">Right pattern for this moment?</text>
<text x="1084" y="412.5" font-size="12.5" fill="#606060" font-weight="400">Can a person follow it?</text>
<text x="1398" y="306.5" class="lbl" fill="#8C8C8C">Output</text>
<rect x="1398" y="318.5" width="252" height="78" rx="10" fill="#FCFCFC" stroke="#8C8C8C" stroke-width="1.5" stroke-dasharray="6 5" filter="url(#sh)"/>
<text x="1416" y="354.5" font-size="13.5" fill="#242424" font-weight="500">The work</text>
<text x="1416" y="373.5" font-size="12.5" fill="#606060" font-weight="400">Not a part of the harness.</text>
<text x="402" y="84" class="lbl" fill="#2C38F5">Designer</text>
<rect x="402" y="96" width="252" height="59" rx="10" fill="#FCFCFC" stroke="#2C38F5" stroke-width="1.5" filter="url(#sh)"/>
<text x="420" y="132" font-size="13.5" fill="#242424" font-weight="500">Intent originates here.</text>
<path d="M528.0,161 V242.0" fill="none" stroke="#4D6FF2" stroke-width="1.6" marker-end="url(#aP)"/>
<rect x="590.0" y="189.5" width="62.919999999999995" height="20" rx="4" fill="#E8E8E8"/><text x="621.46" y="203.5" class="lbl" fill="#2C38F5" text-anchor="middle">intent</text>
<path d="M328,357.5 H394" fill="none" stroke="#8C8C8C" stroke-width="1.6" marker-end="url(#aG)"/>
<rect x="326.63" y="322.5" width="70.74" height="20" rx="4" fill="#E8E8E8"/><text x="362.0" y="336.5" class="lbl" fill="#606060" text-anchor="middle">extract</text>
<path d="M660,357.5 H726" fill="none" stroke="#8C8C8C" stroke-width="1.6" marker-end="url(#aG)"/>
<rect x="670.36" y="322.5" width="47.28" height="20" rx="4" fill="#E8E8E8"/><text x="694.0" y="336.5" class="lbl" fill="#606060" text-anchor="middle">load</text>
<path d="M992,357.5 H1058" fill="none" stroke="#8C8C8C" stroke-width="1.6" marker-end="url(#aG)"/>
<rect x="1006.27" y="322.5" width="39.459999999999994" height="20" rx="4" fill="#E8E8E8"/><text x="1026.0" y="336.5" class="lbl" fill="#606060" text-anchor="middle">run</text>
<path d="M1324,357.5 H1390" fill="none" stroke="#8C8C8C" stroke-width="1.6" marker-end="url(#aG)"/>
<rect x="1334.36" y="322.5" width="47.28" height="20" rx="4" fill="#E8E8E8"/><text x="1358.0" y="336.5" class="lbl" fill="#606060" text-anchor="middle">ship</text>
<path d="M1524.0,402.5 V561" fill="none" stroke="#0E6D75" stroke-width="1.6"/>
<path d="M1524.0,561 H196.0" fill="none" stroke="#0E6D75" stroke-width="1.6"/>
<path d="M196.0,561 V456.0" fill="none" stroke="#0E6D75" stroke-width="1.6" marker-end="url(#aT)"/>
<path d="M528.0,561 V473.0" fill="none" stroke="#0E6D75" stroke-width="1.6" marker-end="url(#aT)"/>
<rect x="550.9300000000001" y="548" width="618.14" height="20" rx="4" fill="#E8E8E8"/><text x="860.0" y="562" class="lbl" fill="#0E6D75" text-anchor="middle">capture  ·  what the gate learns becomes a constraint, a decision, or context</text></svg>
<figcaption>Two years of corrections. Sources of truth generate the context instead of sitting inside it, guidance carries precedence, and whatever the gate learns comes back as a constraint, a decision, or context.</figcaption>
</figure>

That version works. I would still defend it. It is also too big, and that is what the rest of this is about.


## The substrate is not mine, and that is the point

Before any of the design-specific parts, the harness needs a shape, and I want to be precise about where that shape came from, because it is the part of this with actual validation behind it rather than my own conviction.

It is called **ICM, Interpretable Context Methodology** — Jake Van Clief and David McDermott, *Folder Structure as Agent Architecture*. The core claim is exactly what the subtitle says: the folder hierarchy is not where the context is filed, it *is* the architecture. A five-layer model, tested across three production workflow types, validated with fifty-two practitioners, with token-count comparisons rather than vibes. That is a materially better evidence base than anything I could produce alone, and it is why I stopped inventing my own structure and adopted theirs.

What you get from it is a small vocabulary of **forms**, and the discipline is that every folder has to be one of them and say which. A **Pipeline** is sequential stages where reading order is dependency order. A **Record Library** accumulates instances rather than producing one output. A **Knowledge Bundle** is linked research holding the other forms up. An **Umbrella** groups parallel work. A **Context Map** routes. My workspace adds a **Factory** — stable reference material configured once and never regenerated per run — and treats each stage folder as a **Contract**.

That sounds like filing. It is not, and the thing that makes it not filing is the rule that **every folder declares a contract before it is allowed to hold anything**. A `CONTEXT.md`, same fields every time. Which form this folder is. What it needs and where from. The steps. What it produces and who takes it. What a person has to check. A routing table of what is inside. And an honest note about what is not built yet. No orphan folders. The contract comes before the content.

Three practical consequences, all of which took me a while to appreciate:

**An agent orients by reading, not by being told.** The routing lives in the same tree as the material, so a session starts by reading its way in rather than having a human paste an orientation paragraph. Which is precisely the part that would otherwise be model-specific prompt scaffolding.

**A stage is allowed to be empty, but not silent about being empty.** In my workspace the product-surface stage is a stub whose `CONTEXT.md` exists to explain why building it now would be a mistake. That is a structural way of holding an unbuilt thing without either pretending it exists or forgetting it should.

**The forms constrain what you are allowed to build.** Naming a folder a Record Library commits you to it accumulating. Naming it a Factory commits you to it being configured once. The vocabulary makes a certain kind of sprawl harder to do accidentally, which is most of what a methodology is for.

I want to be careful about the timing here rather than imply more than I should. My own knowledge-base repo doing folder-layered context dates to a first commit in January 2026, before the ICM paper went up around March. That is a git-verifiable fact about when I was doing this, and nothing more than that — the paper has controlled testing and fifty-two practitioners behind it, which my repo does not, and arriving somewhere earlier is not the same as arriving with evidence.


## One question sorts all of it

Ask this about every rule you have written down. **Can a machine check it?**

Sounds like a detail. It is the whole fork.

There is good evidence for where the line falls. Polar built a design system called Orbit specifically to survive being written by an LLM. Their argument is that docs cannot close an infinite surface, because a model has endless ways to be slightly wrong. Padding four instead of five. One grey instead of the grey next to it. So they stopped documenting and started closing. Typed props instead of class strings. Raw elements banned by lint. Dark mode collapsed into one value so it is not something the model has to remember. CI is the contract, docs are advice.

Here is the part that matters. They closed spacing, colour, semantics and theme. They left pattern choice completely alone. Nothing in Orbit decides accordion against tabs against a side rail. They locked down what a thing looks like and never touched which thing to use.

That is outside confirmation that the enforceable part is real, bigger than most designers assume, and has an edge to it.

The edge already has a name, older than anything I came up with. Memory research splits declarative knowledge, the stuff you look up, from procedural knowledge, the stuff that shapes what you do without ever being read. A lint rule is procedural. A retrieved rationale is declarative. Worth borrowing the older name, because the failure modes come with it.

So what a machine can check gets compiled and stops being documentation. What it cannot gets stored where you can retrieve it. Two different destinations. Almost everyone puts both in the same folder.


## So I built the thing that holds what is left

Run a design system through that question and most of it walks out. What stays is pattern choice — given this content, this audience, this much room, this viewport: accordion or flat list, card or list row, modal or inline panel. No token decides that and no linter can.

Saying that is easy. The part I could not hand-wave was what the surviving row is actually *made of*, so I gave it a format and built it. The repo is called **cairn**, and the unit it is built around is a single judgment: a condition comes in, a pattern goes out, with a stated reason.

That unit is a **judgment record**. Eight required fields, and the interesting thing is which ones:

```json
{
  "id": "pattern.disclosure.accordion-vs-flat-list.001",
  "pattern": "accordion",
  "constraint_level": "pattern",
  "enforcement": "soft",
  "condition": {
    "content_type": "optional-detail",
    "audience": "mixed",
    "density_budget": "space-constrained",
    "viewport": "mobile"
  },
  "justification": {
    "principle": "Progressive Disclosure",
    "citation": "Nielsen Norman Group — Progressive Disclosure; Miller's Law",
    "rationale": "Content is optional detail, not required for task completion,
      and viewport space is constrained. Showing it flat would exceed a reasonable
      working-memory chunk size and increase scroll-depth cost without benefit for
      users who don't need the detail."
  },
  "alternatives_considered": [
    { "pattern": "flat-list",
      "rejected_because": "Exceeds comfortable chunk size at this content volume." },
    { "pattern": "tabs",
      "rejected_because": "Tabs imply mutually exclusive parallel sections a user
        chooses between — this content is supplementary to a primary flow." }
  ],
  "origin": { "type": "universal", "authored_by": "…", "authored_date": "2026-07-24" }
}
```

Three of those fields are doing real work, and they are the ones that would be easiest to leave out.

**`constraint_level` and `enforcement` encode the sorting question directly.** The four levels are primitive, component, pattern, and block-surface, and enforcement is hard, soft, or partial. A pattern-level record should almost always be soft. A primitive-level one should almost always be hard. That is not decoration — it means the taxonomy is a schema field a machine can check rather than an argument in a blog post. One of the five records exists purely as the contrast case: a 44×44pt touch target, `constraint_level: primitive`, `enforcement: hard`, `linter_checkable: true`. It is in the library to mark the boundary. It is the shape of the thing that should *not* be a retrieved record at all.

**`alternatives_considered` is mandatory, minimum one.** This is the field I would defend hardest, because it is what makes a record falsifiable instead of an assertion. Anyone can write down that they chose an accordion. Writing down that tabs were rejected because tabs imply mutually exclusive parallel sections is a claim someone can come back and argue with. A record with no rejected alternatives is a preference wearing a citation.

**`origin.type` splits universal from org-specific from regulatory.** Universal is citable and safe to publish. Org-specific is the local reason a model could never infer, and it never leaves the building. This matters more than it looks: it is the field that decides what could ever become a shared asset across teams and what is permanently yours.

Then there is the matcher, and it is deliberately stupid. Score every record against the incoming condition. A record matches only if every field it declares agrees. Most specific match — most declared fields — wins. No embeddings, no semantic similarity, no model in the loop. You can run the demo, pick a condition across four selects, and watch it resolve to a pattern, its rationale, and the alternatives it beat, rendered as a literal grayscale wireframe.

And the behaviour I care about most is what happens when nothing matches: it says so and stops. No match is a real result. It means nobody has ruled on this condition yet.

<figure class="diagram" role="img" aria-label="The harness once enforcement absorbs the primitive and component rows: judgment shrinks to a single row and one pattern gate remains.">
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1520 790" data-w="1520" data-h="790" role="img" aria-label="The harness this proposes: judgment shrinks to a single row and enforcement absorbs the rest."><defs><marker id="aG" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="6.5" markerHeight="6.5" orient="auto-start-reverse"><path d="M0,1 L9,5 L0,9 z" fill="#8C8C8C"/></marker><marker id="aP" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="6.5" markerHeight="6.5" orient="auto-start-reverse"><path d="M0,1 L9,5 L0,9 z" fill="#4D6FF2"/></marker><marker id="aT" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="6.5" markerHeight="6.5" orient="auto-start-reverse"><path d="M0,1 L9,5 L0,9 z" fill="#0E6D75"/></marker><marker id="aC" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="6.5" markerHeight="6.5" orient="auto-start-reverse"><path d="M0,1 L9,5 L0,9 z" fill="#B02016"/></marker><marker id="aK" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="6.5" markerHeight="6.5" orient="auto-start-reverse"><path d="M0,1 L9,5 L0,9 z" fill="#8C1EB3"/></marker><filter id="sh" x="-20%" y="-20%" width="140%" height="140%"><feDropShadow dx="0" dy="2" stdDeviation="3" flood-color="#010101" flood-opacity="0.07"/></filter></defs><style>text{font-family:'Atkinson Hyperlegible Next', ui-sans-serif, system-ui, sans-serif;}.lbl{font-size:11.5px;font-weight:700;letter-spacing:.09em;text-transform:uppercase;}.hd{font-size:12.5px;font-weight:700;letter-spacing:.14em;text-transform:uppercase;}</style><text x="70" y="286" class="hd" fill="#010101">The design harness</text>
<rect x="46" y="304" width="1298" height="452" rx="16" fill="none" stroke="#BEBEBE" stroke-width="1.5" stroke-dasharray="7 6"/>
<text x="360" y="508" class="lbl" fill="#2C38F5">Expressible set</text>
<rect x="360" y="520" width="600" height="190" rx="12" fill="#4D6FF2" fill-opacity="0.06" stroke="#4D6FF2" stroke-width="1.5"/>
<text x="674" y="596" font-size="12.5" fill="#606060">Typed props, closed token set</text>
<text x="674" y="620" font-size="12.5" fill="#606060">Raw elements banned, lint as contract</text>
<text x="674" y="644" font-size="12.5" fill="#606060">Theme collapsed out of existence</text>
<text x="382" y="688" font-size="12.5" fill="#2C38F5">Primitive and component rows. This was documentation. Enforcement absorbed it.</text>
<text x="360" y="84" class="lbl" fill="#0E6D75">Shared judgment library</text>
<rect x="360" y="96" width="500" height="78" rx="10" fill="#FCFCFC" stroke="#0E6D75" stroke-width="1.5" filter="url(#sh)"/>
<text x="378" y="132" font-size="13.5" fill="#242424" font-weight="500">Pattern, condition, rationale, rejected alternatives</text>
<text x="378" y="151" font-size="12.5" fill="#606060" font-weight="400">Not yours. An asset the field holds in common.</text>
<text x="360" y="318" class="lbl" fill="#8C1EB3">Judgment</text>
<rect x="360" y="330" width="500" height="78" rx="10" fill="#FCFCFC" stroke="#8C1EB3" stroke-width="1.5" filter="url(#sh)"/>
<text x="378" y="366" font-size="13.5" fill="#242424" font-weight="500">Pattern selection. That is the whole row.</text>
<text x="378" y="385" font-size="12.5" fill="#606060" font-weight="400">The only part a linter can never reach.</text>
<text x="390" y="560" class="lbl" fill="#2C38F5">Execution</text>
<rect x="390" y="572" width="250" height="80" rx="10" fill="#FCFCFC" stroke="#2C38F5" stroke-width="1.5" filter="url(#sh)"/>
<text x="408" y="608" font-size="13.5" fill="#242424" font-weight="500">It cannot express the wrong</text>
<text x="408" y="629" font-size="13.5" fill="#242424" font-weight="500">answer.</text>
<text x="70" y="552" class="lbl" fill="#2C38F5">Source of truth</text>
<rect x="70" y="564" width="210" height="101" rx="10" fill="#FCFCFC" stroke="#2C38F5" stroke-width="1.5" filter="url(#sh)"/>
<text x="88" y="600" font-size="13.5" fill="#242424" font-weight="500">Codebase</text>
<text x="88" y="621" font-size="13.5" fill="#242424" font-weight="500">Tokens</text>
<text x="88" y="642" font-size="13.5" fill="#242424" font-weight="500">Figma</text>
<text x="1030" y="538" class="lbl" fill="#B02016">Pattern gate</text>
<rect x="1030" y="550" width="280" height="118" rx="10" fill="#FCFCFC" stroke="#B02016" stroke-width="1.5" filter="url(#sh)"/>
<text x="1048" y="586" font-size="13.5" fill="#242424" font-weight="500">Did the rendered pattern match</text>
<text x="1048" y="607" font-size="13.5" fill="#242424" font-weight="500">the record?</text>
<text x="1048" y="626" font-size="12.5" fill="#606060" font-weight="400">Check the artifact, never the</text>
<text x="1048" y="645" font-size="12.5" fill="#606060" font-weight="400">model&#x27;s account of it.</text>
<text x="1380" y="560" class="lbl" fill="#8C8C8C">Output</text>
<rect x="1380" y="572" width="140" height="59" rx="10" fill="#FCFCFC" stroke="#8C8C8C" stroke-width="1.5" stroke-dasharray="6 5" filter="url(#sh)"/>
<text x="1398" y="608" font-size="13.5" fill="#242424" font-weight="500">The work</text>
<path d="M520.0,180 V322" fill="none" stroke="#0E6D75" stroke-width="1.6" marker-end="url(#aT)"/>
<rect x="496.36" y="239.0" width="47.28" height="20" rx="4" fill="#E8E8E8"/><text x="520.0" y="253.0" class="lbl" fill="#0E6D75" text-anchor="middle">bind</text>
<path d="M720.0,322 V180" fill="none" stroke="#0E6D75" stroke-width="1.6" marker-end="url(#aT)"/>
<rect x="684.63" y="239.0" width="70.74" height="20" rx="4" fill="#E8E8E8"/><text x="720.0" y="253.0" class="lbl" fill="#0E6D75" text-anchor="middle">promote</text>
<path d="M610.0,414 V490" fill="none" stroke="#8C1EB3" stroke-width="1.6" marker-end="url(#aK)"/>
<rect x="519.89" y="440.0" width="180.22" height="20" rx="4" fill="#E8E8E8"/><text x="610.0" y="454.0" class="lbl" fill="#8C1EB3" text-anchor="middle">match, per generation</text>
<path d="M286,615.0 H352" fill="none" stroke="#4D6FF2" stroke-width="1.6" marker-end="url(#aP)"/>
<path d="M966,615.0 H1022" fill="none" stroke="#8C8C8C" stroke-width="1.6" marker-end="url(#aG)"/>
<path d="M1316,615.0 H1372" fill="none" stroke="#8C8C8C" stroke-width="1.6" marker-end="url(#aG)"/></svg>
<figcaption>Enforcement moves up front. The build gate mostly disappears. Guidance stops being something you load at the start and becomes something you look up when the decision happens.</figcaption>
</figure>

Three things change once you accept that. Guardrails stop being something output passes through and become something input cannot get around, so you do not catch the wrong token afterward, you make it impossible to type. Guidance stops being a document and becomes something the generator calls. And if the judgment layer really is portable, it stops belonging to your team at all.

Yes, this one nests too. Nesting is right when the containment is real, and a bounded set of options genuinely does contain everything the generator can produce. It is wrong when it is not, which is why documentation should never be drawn as containing a codebase.


## What that thin row needs, and where mine is still thin

Thin does not mean free. Three things have to be true or the format is just a nicer way to store opinions. Two of them the build does. One it does not, and that is the honest gap.

**It needs precedence, not a list.** Two records will contradict each other eventually and one has to win. In the capture layer I have run longest this is three tiers: constraints are hard rules and the agent flags a violation, decisions carry the choice and the reasoning and when to revisit, context informs without constraining. I keep constraints rare on purpose, because if everything is a constraint then nothing is. **In cairn this is not solved.** Two records matching the same condition with conflicting recommendations has no defined resolution beyond most-specific-wins, and most-specific-wins is a tiebreak, not a precedence model. It is the first thing I would fix.

**It needs to be retrieved when the decision happens, not loaded at the start.** A record gets matched against the actual condition at the moment it matters. That is a different thing from stuffing a context window at the top of a session and hoping. It also fails differently: the failure is no-match instead of forgetting, and no-match you can see. Worth being blunt about the current mechanism — matching is exact-field, nothing semantic. A condition phrased slightly differently misses. That is a real limit, and I would rather it miss loudly than fuzzy-match its way to a confident wrong record.

**And it has to stop when nothing matches.** This is the line I would defend hardest. A generator that keeps going without a record does not produce nothing. It falls back on everything the model already believes about interfaces, quietly, at full confidence. That looks like success. It is where drift comes from, and you cannot see it, because the output is perfectly plausible.

> Every drift problem is the model’s own priors winning.

Which changes the diagnosis. The model did not get it wrong. It did what any unbriefed model does. Stopping loudly is what turns that silence into a queue, and the queue is the only reason the system ever improves.


## Let the model score it, do not let it decide

That leaves the gate, and this is usually where these arguments go vague. Check the output against the record, people say, and then never explain how.

I had the same gap until I realised I had already built the mechanism somewhere else, in a domain with a lot less tolerance for a plausible wrong answer. It is a seven node pipeline for genomic interpretation, and the rule it runs on moves over without changes.

> Models generate and models score. A function decides. Nothing that generates gets a vote on its own output.

A critic scores the output against records pulled from a real knowledge base rather than against its own instincts, on three things. Consistency catches claims the evidence does not support. Alignment catches the hard parts that got quietly skipped. Specificity catches output that sounds fine and would have come out the same for any input.

That last one is worth sitting with. Specificity is the sameness problem with a number on it. Everyone complains that AI interfaces all look the same. Almost nobody measures it.

The critic can be a model, because it reads an artifact and scores it against something outside itself. The decision cannot be, because if the generator can influence the verdict then there is no verdict. So the decision is a plain threshold function you can unit test on its own and audit without running a model at all. Pass, revise twice, then fail. Three outcomes rather than two, because real systems need a middle and an unlimited retry is not one.

<figure class="diagram" role="img" aria-label="The operating view: a slow authoring loop, a versioned shared store, a fast generation loop ending in a critic and a deterministic governance function, and four signal types returning to the queue.">
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1590 1476" data-w="1590" data-h="1476" role="img" aria-label="Operating view: a slow authoring loop, a versioned shared store, a fast generation loop ending in a critic and a deterministic governance function, and four signal types returning to the authoring queue."><defs><marker id="aG" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="6.5" markerHeight="6.5" orient="auto-start-reverse"><path d="M0,1 L9,5 L0,9 z" fill="#8C8C8C"/></marker><marker id="aP" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="6.5" markerHeight="6.5" orient="auto-start-reverse"><path d="M0,1 L9,5 L0,9 z" fill="#4D6FF2"/></marker><marker id="aT" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="6.5" markerHeight="6.5" orient="auto-start-reverse"><path d="M0,1 L9,5 L0,9 z" fill="#0E6D75"/></marker><marker id="aC" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="6.5" markerHeight="6.5" orient="auto-start-reverse"><path d="M0,1 L9,5 L0,9 z" fill="#B02016"/></marker><marker id="aK" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="6.5" markerHeight="6.5" orient="auto-start-reverse"><path d="M0,1 L9,5 L0,9 z" fill="#8C1EB3"/></marker><filter id="sh" x="-20%" y="-20%" width="140%" height="140%"><feDropShadow dx="0" dy="2" stdDeviation="3" flood-color="#010101" flood-opacity="0.07"/></filter></defs><style>text{font-family:'Atkinson Hyperlegible Next', ui-sans-serif, system-ui, sans-serif;}.lbl{font-size:11.5px;font-weight:700;letter-spacing:.09em;text-transform:uppercase;}.hd{font-size:12.5px;font-weight:700;letter-spacing:.14em;text-transform:uppercase;}</style><text x="70" y="62" class="hd" fill="#2C38F5">Authoring loop  ·  runs in weeks  ·  a person decides</text><line x1="70" y1="76" x2="1520" y2="76" stroke="#BEBEBE" stroke-width="1.5"/>
<text x="70" y="319" class="hd" fill="#010101">Shared store  ·  versioned  ·  the only durable layer</text><line x1="70" y1="333" x2="1520" y2="333" stroke="#BEBEBE" stroke-width="1.5"/>
<text x="70" y="612" class="hd" fill="#0E6D75">Generation loop  ·  runs in seconds  ·  every request</text><line x1="70" y1="626" x2="1520" y2="626" stroke="#BEBEBE" stroke-width="1.5"/>
<text x="70" y="1146" class="hd" fill="#B02016">Signals  ·  every run emits a typed artifact</text><line x1="70" y1="1160" x2="1520" y2="1160" stroke="#BEBEBE" stroke-width="1.5"/>
<text x="70" y="122.5" class="lbl" fill="#2C38F5">Gap queue</text>
<rect x="70" y="134.5" width="300" height="78" rx="10" fill="#FCFCFC" stroke="#2C38F5" stroke-width="1.5" filter="url(#sh)"/>
<text x="88" y="170.5" font-size="13.5" fill="#242424" font-weight="500">Conditions nothing could resolve</text>
<text x="88" y="189.5" font-size="12.5" fill="#606060" font-weight="400">Not a backlog. The roadmap.</text>
<text x="470" y="112.0" class="lbl" fill="#2C38F5">Author and govern</text>
<rect x="470" y="124.0" width="320" height="99" rx="10" fill="#FCFCFC" stroke="#2C38F5" stroke-width="1.5" filter="url(#sh)"/>
<text x="488" y="160.0" font-size="13.5" fill="#242424" font-weight="500">One person decides what a model cannot</text>
<text x="488" y="181.0" font-size="13.5" fill="#242424" font-weight="500">infer</text>
<text x="488" y="200.0" font-size="12.5" fill="#606060" font-weight="400">Owner named, review cadence set.</text>
<text x="890" y="122.5" class="lbl" fill="#2C38F5">Classify</text>
<rect x="890" y="134.5" width="320" height="78" rx="10" fill="#FCFCFC" stroke="#2C38F5" stroke-width="1.5" filter="url(#sh)"/>
<text x="908" y="170.5" font-size="13.5" fill="#242424" font-weight="500">Can a machine check it?</text>
<text x="908" y="189.5" font-size="12.5" fill="#606060" font-weight="400">The fork the whole system turns on.</text>
<text x="540" y="385.0" class="lbl" fill="#8C1EB3">Record library</text>
<rect x="540" y="397.0" width="330" height="97" rx="10" fill="#FCFCFC" stroke="#8C1EB3" stroke-width="1.5" filter="url(#sh)"/>
<text x="558" y="433.0" font-size="13.5" fill="#242424" font-weight="500">Pattern, condition, rationale, rejected</text>
<text x="558" y="452.0" font-size="12.5" fill="#606060" font-weight="400">Retrieved, never enforced. Pinned to a</text>
<text x="558" y="471.0" font-size="12.5" fill="#606060" font-weight="400">version.</text>
<text x="940" y="385.0" class="lbl" fill="#2C38F5">Expressible set</text>
<rect x="940" y="397.0" width="330" height="97" rx="10" fill="#FCFCFC" stroke="#2C38F5" stroke-width="1.5" filter="url(#sh)"/>
<text x="958" y="433.0" font-size="13.5" fill="#242424" font-weight="500">Typed props, closed tokens, lint, CI</text>
<text x="958" y="452.0" font-size="12.5" fill="#606060" font-weight="400">Compiled, never consulted. The wrong answer</text>
<text x="958" y="471.0" font-size="12.5" fill="#606060" font-weight="400">stops existing.</text>
<text x="70" y="793.0" class="lbl" fill="#0E6D75">Condition</text>
<rect x="70" y="805.0" width="190" height="122" rx="10" fill="#FCFCFC" stroke="#0E6D75" stroke-width="1.5" filter="url(#sh)"/>
<text x="88" y="841.0" font-size="13.5" fill="#242424" font-weight="500">Content type</text>
<text x="88" y="862.0" font-size="13.5" fill="#242424" font-weight="500">Audience</text>
<text x="88" y="883.0" font-size="13.5" fill="#242424" font-weight="500">Space budget</text>
<text x="88" y="904.0" font-size="13.5" fill="#242424" font-weight="500">Viewport</text>
<text x="320" y="796.0" class="lbl" fill="#0E6D75">Resolve</text>
<rect x="320" y="808.0" width="260" height="116" rx="10" fill="#FCFCFC" stroke="#0E6D75" stroke-width="1.5" filter="url(#sh)"/>
<text x="338" y="844.0" font-size="13.5" fill="#242424" font-weight="500">Most specific record wins</text>
<text x="338" y="863.0" font-size="12.5" fill="#606060" font-weight="400">Ties broken by owner.</text>
<text x="338" y="882.0" font-size="12.5" fill="#606060" font-weight="400">No match halts. It does not</text>
<text x="338" y="901.0" font-size="12.5" fill="#606060" font-weight="400">proceed and guess.</text>
<text x="640" y="795.0" class="lbl" fill="#0E6D75">Generate</text>
<rect x="640" y="807.0" width="260" height="118" rx="10" fill="#FCFCFC" stroke="#0E6D75" stroke-width="1.5" filter="url(#sh)"/>
<text x="658" y="843.0" font-size="13.5" fill="#242424" font-weight="500">Inside the bounded set,</text>
<text x="658" y="864.0" font-size="13.5" fill="#242424" font-weight="500">carrying the record</text>
<text x="658" y="883.0" font-size="12.5" fill="#606060" font-weight="400">The model is the least durable</text>
<text x="658" y="902.0" font-size="12.5" fill="#606060" font-weight="400">part of this row.</text>
<text x="960" y="704.0" class="lbl" fill="#0E6D75">The gate</text>
<rect x="960" y="716.0" width="380" height="300" rx="12" fill="#0E6D75" fill-opacity="0.05" stroke="#0E6D75" stroke-width="1.5"/>
<rect x="982" y="738.0" width="336" height="119" rx="9" fill="#FCFCFC" stroke="#0E6D75" stroke-width="1.4" filter="url(#sh)"/>
<text x="1000" y="770.0" class="lbl" fill="#0E6D75">Critic</text>
<text x="1000" y="796.0" font-size="13.5" fill="#242424" font-weight="500">Consistency, alignment, specificity</text>
<text x="1000" y="815.0" font-size="12.5" fill="#606060" font-weight="400">Scores against the record, not its own</text>
<text x="1000" y="834.0" font-size="12.5" fill="#606060" font-weight="400">priors.</text>
<rect x="982" y="875.0" width="336" height="119" rx="9" fill="#FCFCFC" stroke="#2C38F5" stroke-width="1.4" filter="url(#sh)"/>
<text x="1000" y="907.0" class="lbl" fill="#2C38F5">Governance</text>
<text x="1000" y="933.0" font-size="13.5" fill="#242424" font-weight="500">A pure function, not a model</text>
<text x="1000" y="952.0" font-size="12.5" fill="#606060" font-weight="400">Pass at .75. Fail under .50. Nothing that</text>
<text x="1000" y="971.0" font-size="12.5" fill="#606060" font-weight="400">generates gets to judge itself.</text>
<text x="1400" y="824.5" class="lbl" fill="#8C8C8C">Ship</text>
<rect x="1400" y="836.5" width="120" height="59" rx="10" fill="#FCFCFC" stroke="#8C8C8C" stroke-width="1.5" stroke-dasharray="6 5" filter="url(#sh)"/>
<text x="1418" y="872.5" font-size="13.5" fill="#242424" font-weight="500">The work</text>
<path d="M376,173.5 H462" fill="none" stroke="#4D6FF2" stroke-width="1.6" marker-end="url(#aP)"/>
<path d="M796,173.5 H882" fill="none" stroke="#4D6FF2" stroke-width="1.6" marker-end="url(#aP)"/>
<path d="M1050.0,218.5 V258.5" fill="none" stroke="#8C8C8C" stroke-width="1.6"/>
<path d="M705.0,258.5 H1105.0" fill="none" stroke="#8C8C8C" stroke-width="1.6"/>
<path d="M705.0,258.5 V389.0" fill="none" stroke="#8C8C8C" stroke-width="1.6" marker-end="url(#aG)"/>
<path d="M1105.0,258.5 V389.0" fill="none" stroke="#8C8C8C" stroke-width="1.6" marker-end="url(#aG)"/>
<rect x="735.18" y="275.5" width="31.64" height="20" rx="4" fill="#E8E8E8"/><text x="751.0" y="289.5" class="lbl" fill="#606060" text-anchor="middle">no</text>
<rect x="1133.27" y="275.5" width="39.459999999999994" height="20" rx="4" fill="#E8E8E8"/><text x="1153.0" y="289.5" class="lbl" fill="#606060" text-anchor="middle">yes</text>
<path d="M1105.0,500.0 V656" fill="none" stroke="#4D6FF2" stroke-width="1.6"/>
<path d="M1105.0,656 H770.0" fill="none" stroke="#4D6FF2" stroke-width="1.6"/>
<path d="M770.0,656 V799.0" fill="none" stroke="#4D6FF2" stroke-width="1.6" marker-end="url(#aP)"/>
<rect x="909.95" y="643" width="55.099999999999994" height="20" rx="4" fill="#E8E8E8"/><text x="937.5" y="657" class="lbl" fill="#2C38F5" text-anchor="middle">binds</text>
<path d="M705.0,500.0 V690" fill="none" stroke="#0E6D75" stroke-width="1.6"/>
<path d="M705.0,690 H450.0" fill="none" stroke="#0E6D75" stroke-width="1.6"/>
<path d="M450.0,690 V800.0" fill="none" stroke="#0E6D75" stroke-width="1.6" marker-end="url(#aT)"/>
<rect x="538.22" y="677" width="78.56" height="20" rx="4" fill="#E8E8E8"/><text x="577.5" y="691" class="lbl" fill="#0E6D75" text-anchor="middle">retrieve</text>
<path d="M266,866.0 H312" fill="none" stroke="#0E6D75" stroke-width="1.6" marker-end="url(#aT)"/>
<path d="M586,866.0 H632" fill="none" stroke="#0E6D75" stroke-width="1.6" marker-end="url(#aT)"/>
<path d="M906,866.0 H952" fill="none" stroke="#0E6D75" stroke-width="1.6" marker-end="url(#aT)"/>
<rect x="902.45" y="827.0" width="55.099999999999994" height="20" rx="4" fill="#E8E8E8"/><text x="930.0" y="841.0" class="lbl" fill="#0E6D75" text-anchor="middle">+ rec</text>
<path d="M1346,866.0 H1392" fill="none" stroke="#0E6D75" stroke-width="1.6" marker-end="url(#aT)"/>
<rect x="1346.36" y="827.0" width="47.28" height="20" rx="4" fill="#E8E8E8"/><text x="1370.0" y="841.0" class="lbl" fill="#0E6D75" text-anchor="middle">pass</text>
<path d="M1070.0,1022.0 V1068" fill="none" stroke="#B02016" stroke-width="1.6"/>
<path d="M1070.0,1068 H770.0" fill="none" stroke="#B02016" stroke-width="1.6"/>
<path d="M770.0,1068 V933.0" fill="none" stroke="#B02016" stroke-width="1.6" marker-end="url(#aC)"/>
<rect x="712.59" y="1055" width="414.82" height="20" rx="4" fill="#E8E8E8"/><text x="920.0" y="1069" class="lbl" fill="#B02016" text-anchor="middle">revise, twice, then it is a failure and not a retry</text>
<rect x="70" y="1186" width="1450" height="176" rx="12" fill="#B02016" fill-opacity="0.05" stroke="#B02016" stroke-width="1.5"/>
<text x="96" y="1220" font-size="12.5" fill="#606060">Typed and versioned: scores, decision, revision count, append-only audit log. No node can edit a prior entry.</text>
<text x="96.0" y="1262" class="lbl" fill="#B02016">No match</text>
<text x="96.0" y="1286" font-size="12.5" fill="#606060">Nobody has ruled on this condition</text>
<text x="96.0" y="1338" font-size="12.5" fill="#0E6D75" font-weight="600">→ Author a record</text>
<text x="445.5" y="1262" class="lbl" fill="#B02016">Override</text>
<text x="445.5" y="1286" font-size="12.5" fill="#606060">A person rejected what was retrieved</text>
<text x="445.5" y="1338" font-size="12.5" fill="#0E6D75" font-weight="600">→ The record is wrong</text>
<text x="795.0" y="1262" class="lbl" fill="#B02016">Stale</text>
<text x="795.0" y="1286" font-size="12.5" fill="#606060">The system moved past the version pin</text>
<text x="795.0" y="1338" font-size="12.5" fill="#0E6D75" font-weight="600">→ Review, do not trust</text>
<text x="1144.5" y="1262" class="lbl" fill="#B02016">Score drop</text>
<text x="1144.5" y="1286" font-size="12.5" fill="#606060">Aggregate fell below the baseline</text>
<text x="1144.5" y="1338" font-size="12.5" fill="#0E6D75" font-weight="600">→ Drift, before a user</text>
<path d="M450.0,930.0 V1178" fill="none" stroke="#B02016" stroke-width="1.6" marker-end="url(#aC)"/>
<path d="M1245.0,1022.0 V1178" fill="none" stroke="#B02016" stroke-width="1.6" marker-end="url(#aC)"/>
<path d="M270,1368 V1416" fill="none" stroke="#B02016" stroke-width="1.6"/>
<path d="M270,1416 H34" fill="none" stroke="#B02016" stroke-width="1.6"/>
<path d="M34,1416 V173.5" fill="none" stroke="#B02016" stroke-width="1.6"/>
<path d="M34,173.5 H62" fill="none" stroke="#B02016" stroke-width="1.6" marker-end="url(#aC)"/>
<rect x="140.79000000000002" y="1403" width="258.41999999999996" height="20" rx="4" fill="#E8E8E8"/><text x="270.0" y="1417" class="lbl" fill="#B02016" text-anchor="middle">every miss is an authoring task</text></svg>
<figcaption>Two clocks. Authoring runs in weeks with a person deciding. Generation runs in seconds with nobody watching. Most complaints about documentation come from running the slow loop at the fast loop’s speed.</figcaption>
</figure>

Every run writes out a typed, versioned artifact. Scores, decision, revision count, and an audit log nothing can edit after the fact. That is what makes the feedback loop real instead of aspirational. Four signals come out of it and they go four different places. No match means nobody has ruled on this yet, so write a record. Override means someone rejected what came back, so the record is wrong. Stale means the system moved past a version pin. Score drop means drift, caught before anyone sees it.

A feedback loop that dumps everything into one inbox is a suggestion box. Four signals with four destinations is a mechanism.


## Seven kinds of memory, two of which are not memory

There is a vocabulary going around for agent memory. Working, semantic, episodic, procedural, retrieval, parametric, prospective. It usually shows up as seven parallel things, which is the one thing it definitely is not. Sorted properly it explains something the stage view cannot.

Three of them are stores you keep. Semantic is what is true regardless of when, so the records and the vocabulary. Somebody wrote it, it is pinned to a version, and it gets looked up. Procedural is how the thing gets built, so the compiled set. Nobody reads it and it shapes the work anyway. Episodic is what happened on each run, so the audit log. It piles up on its own and nobody authors it.

One is a buffer. Working memory is the run itself, bounded, thrown away at the end.

One is a trigger. Prospective memory is remembering to do something later, and almost every harness has it without calling it that. A version pin that fires when the system moves past it. A review date that fires on a named person. A question sitting in a queue until somebody answers it. All the same kind of thing, usually built three separate ways. It is the only one that fires on time rather than on lookup.

And two of the seven are not stores at all. **Retrieval is the transport**, how a store reaches the buffer. It is an arrow, not a box, and treating it as a seventh cupboard is how teams end up with a memory service nobody can describe the contents of. **Parametric is the substrate**. You cannot write to it, version it, review it or pin it. The only thing about it that matters here is that it fills any gap instantly and confidently.

<figure class="diagram" role="img" aria-label="The same machine sorted by memory register: working buffer, three durable stores, a prospective trigger layer, and parametric memory as the substrate.">
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1560 1081" data-w="1560" data-h="1081" role="img" aria-label="The same harness sorted by memory register: one ephemeral working buffer, three durable stores (semantic, procedural, episodic), a prospective trigger layer, and parametric memory as the substrate that fills any gap."><defs><marker id="aG" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="6.5" markerHeight="6.5" orient="auto-start-reverse"><path d="M0,1 L9,5 L0,9 z" fill="#8C8C8C"/></marker><marker id="aP" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="6.5" markerHeight="6.5" orient="auto-start-reverse"><path d="M0,1 L9,5 L0,9 z" fill="#4D6FF2"/></marker><marker id="aT" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="6.5" markerHeight="6.5" orient="auto-start-reverse"><path d="M0,1 L9,5 L0,9 z" fill="#0E6D75"/></marker><marker id="aC" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="6.5" markerHeight="6.5" orient="auto-start-reverse"><path d="M0,1 L9,5 L0,9 z" fill="#B02016"/></marker><marker id="aK" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="6.5" markerHeight="6.5" orient="auto-start-reverse"><path d="M0,1 L9,5 L0,9 z" fill="#8C1EB3"/></marker><filter id="sh" x="-20%" y="-20%" width="140%" height="140%"><feDropShadow dx="0" dy="2" stdDeviation="3" flood-color="#010101" flood-opacity="0.07"/></filter></defs><style>text{font-family:'Atkinson Hyperlegible Next', ui-sans-serif, system-ui, sans-serif;}.lbl{font-size:11.5px;font-weight:700;letter-spacing:.09em;text-transform:uppercase;}.hd{font-size:12.5px;font-weight:700;letter-spacing:.14em;text-transform:uppercase;}</style><text x="70" y="62" class="hd" fill="#0E6D75">Ephemeral  ·  one register, rebuilt every request</text><line x1="70" y1="76" x2="1490" y2="76" stroke="#BEBEBE" stroke-width="1.5"/>
<text x="70" y="306" class="hd" fill="#010101">Durable stores  ·  three, and they are not interchangeable</text><line x1="70" y1="320" x2="1490" y2="320" stroke="#BEBEBE" stroke-width="1.5"/>
<text x="70" y="607" class="hd" fill="#2C38F5">Triggers  ·  memory that fires on time, not on lookup</text><line x1="70" y1="621" x2="1490" y2="621" stroke="#BEBEBE" stroke-width="1.5"/>
<text x="70" y="857" class="hd" fill="#8C8C8C">Substrate  ·  not a layer you manage</text><line x1="70" y1="871" x2="1490" y2="871" stroke="#BEBEBE" stroke-width="1.5"/>
<text x="430" y="112" class="lbl" fill="#0E6D75">Working memory</text>
<rect x="430" y="124" width="700" height="78" rx="10" fill="#FCFCFC" stroke="#0E6D75" stroke-width="1.5" filter="url(#sh)"/>
<text x="448" y="160" font-size="13.5" fill="#242424" font-weight="500">The run. Condition, retrieved record, bounded vocabulary.</text>
<text x="448" y="179" font-size="12.5" fill="#606060" font-weight="400">Bounded and discarded. Nothing here survives the request.</text>
<text x="70" y="380.0" class="lbl" fill="#8C1EB3">Semantic</text>
<rect x="70" y="392.0" width="440" height="97" rx="10" fill="#FCFCFC" stroke="#8C1EB3" stroke-width="1.5" filter="url(#sh)"/>
<text x="88" y="428.0" font-size="13.5" fill="#242424" font-weight="500">What is true regardless of when</text>
<text x="88" y="447.0" font-size="12.5" fill="#606060" font-weight="400">Records, vocabulary, tokens. Authored by a person, version-</text>
<text x="88" y="466.0" font-size="12.5" fill="#606060" font-weight="400">pinned. Declarative: it gets consulted.</text>
<text x="560" y="380.0" class="lbl" fill="#2C38F5">Procedural</text>
<rect x="560" y="392.0" width="440" height="97" rx="10" fill="#FCFCFC" stroke="#2C38F5" stroke-width="1.5" filter="url(#sh)"/>
<text x="578" y="428.0" font-size="13.5" fill="#242424" font-weight="500">How the thing gets built</text>
<text x="578" y="447.0" font-size="12.5" fill="#606060" font-weight="400">Typed props, lint, CI. Compiled, not consulted. It shapes</text>
<text x="578" y="466.0" font-size="12.5" fill="#606060" font-weight="400">action without ever being read.</text>
<text x="1050" y="380.0" class="lbl" fill="#010101">Episodic</text>
<rect x="1050" y="392.0" width="440" height="97" rx="10" fill="#FCFCFC" stroke="#010101" stroke-width="1.5" filter="url(#sh)"/>
<text x="1068" y="428.0" font-size="13.5" fill="#242424" font-weight="500">What happened on each run</text>
<text x="1068" y="447.0" font-size="12.5" fill="#606060" font-weight="400">Scores, decision, revisions, timestamps. Accumulated rather</text>
<text x="1068" y="466.0" font-size="12.5" fill="#606060" font-weight="400">than authored. Append-only.</text>
<text x="70" y="657" class="lbl" fill="#2C38F5">Prospective</text>
<rect x="70" y="669" width="720" height="116" rx="10" fill="#FCFCFC" stroke="#2C38F5" stroke-width="1.5" filter="url(#sh)"/>
<text x="88" y="705" font-size="13.5" fill="#242424" font-weight="500">Remembering to act later</text>
<text x="88" y="724" font-size="12.5" fill="#606060" font-weight="400">A version pin fires when the system moves past it.</text>
<text x="88" y="743" font-size="12.5" fill="#606060" font-weight="400">A review cadence fires on the named owner.</text>
<text x="88" y="762" font-size="12.5" fill="#606060" font-weight="400">An unresolved condition waits in the queue until someone answers it.</text>
<path d="M290.0,384.0 V346" fill="none" stroke="#8C1EB3" stroke-width="1.6"/>
<path d="M290.0,346 H560" fill="none" stroke="#8C1EB3" stroke-width="1.6"/>
<path d="M560,346 V210" fill="none" stroke="#8C1EB3" stroke-width="1.6" marker-end="url(#aK)"/>
<rect x="385.72" y="333" width="78.56" height="20" rx="4" fill="#E8E8E8"/><text x="425.0" y="347" class="lbl" fill="#8C1EB3" text-anchor="middle">retrieve</text>
<path d="M780.0,384.0 V210" fill="none" stroke="#4D6FF2" stroke-width="1.6" marker-end="url(#aP)"/>
<rect x="752.45" y="284.0" width="55.099999999999994" height="20" rx="4" fill="#E8E8E8"/><text x="780.0" y="298.0" class="lbl" fill="#2C38F5" text-anchor="middle">binds</text>
<path d="M1000,208 V310" fill="none" stroke="#8C8C8C" stroke-width="1.6"/>
<path d="M1000,310 H1270.0" fill="none" stroke="#8C8C8C" stroke-width="1.6"/>
<path d="M1270.0,310 V384.0" fill="none" stroke="#8C8C8C" stroke-width="1.6" marker-end="url(#aG)"/>
<rect x="1111.36" y="297" width="47.28" height="20" rx="4" fill="#E8E8E8"/><text x="1135.0" y="311" class="lbl" fill="#606060" text-anchor="middle">emit</text>
<path d="M1270.0,495.0 V541" fill="none" stroke="#8C1EB3" stroke-width="1.6"/>
<path d="M1270.0,541 H290.0" fill="none" stroke="#8C1EB3" stroke-width="1.6"/>
<path d="M290.0,541 V497.0" fill="none" stroke="#8C1EB3" stroke-width="1.6" marker-end="url(#aK)"/>
<rect x="627.33" y="528" width="305.34" height="20" rx="4" fill="#E8E8E8"/><text x="780.0" y="542" class="lbl" fill="#8C1EB3" text-anchor="middle">consolidate  ·  the loop’s actual job</text>
<path d="M62,727.0 H42 V440.5 H62" fill="none" stroke="#4D6FF2" stroke-width="1.6" marker-end="url(#aP)"/>
<rect x="54" y="546.0" width="94.19999999999999" height="20" rx="4" fill="#E8E8E8"/><text x="101.1" y="560.0" class="lbl" fill="#2C38F5" text-anchor="middle">review due</text>
<rect x="70" y="903" width="1420" height="118" rx="12" fill="#8C8C8C" fill-opacity="0.08" stroke="#8C8C8C" stroke-width="1.5" stroke-dasharray="7 6"/>
<text x="96" y="945" font-size="13.5" fill="#010101" font-weight="600">Parametric  ·  everything the model already believes about interfaces</text>
<text x="96" y="973" font-size="12.5" fill="#606060">Not a store you write to. It is the default that fills any gap you leave, silently, at full confidence.</text>
<text x="96" y="995" font-size="12.5" fill="#B02016">Every drift incident is parametric memory winning.</text>
<path d="M1514,897 V163.0 H1138" fill="none" stroke="#B02016" stroke-width="1.6" stroke-dasharray="6 5" marker-end="url(#aC)"/>
<rect x="1226.07" y="150.0" width="195.85999999999999" height="20" rx="4" fill="#E8E8E8"/><text x="1324.0" y="164.0" class="lbl" fill="#B02016" text-anchor="middle">fills any gap you leave</text></svg>
<figcaption>The same machine sorted by register. Working memory is the only one the model ever touches. Everything else is architecture about what gets loaded into it.</figcaption>
</figure>

Sorting it this way turned up one correction I did not expect. The return path in the first diagram says capture, and capture is the wrong word. Episodic piles up for free. Semantic has to be written. The step between them is **consolidation**, turning one specific thing that happened into a general rule. That is the expensive human bit, it is the one part of the loop you cannot automate, and calling it capture makes it sound like storage.


## Where all this actually lives

An architecture that only exists in a diagram is a diagram. All of it has to sit somewhere an agent can reach at the moment it needs it, and reaching it is not the same problem as storing it.

So here is the whole thing as a directory, which is the only form of it that actually runs. Five stages, numbered, because the numbering is the dependency order and reading order is build order. Stage two cannot reason about accordion against card unless stage one has already made both named things, so stage one goes first and says so.

```
cairn/
├── CLAUDE.md              # read first, every session: where am I, where do I go
├── CONTEXT.md             # the pipeline definition and why it is shaped this way
├── 01_design-system/      # the bounded vocabulary a decision can resolve to
│   ├── CONTEXT.md
│   ├── tokens.json
│   └── components/vocabulary.json
├── 02_judgment-layer/     # the working core
│   ├── CONTEXT.md
│   ├── schema/            # Factory — the record format, configured once
│   ├── records/           # Record Library — accumulating instances
│   └── knowledge/         # Knowledge Bundle — the research holding both up
├── 03_implementation/     # where a judgment becomes a running artifact
├── 04_handoff/            # keeping that artifact coherent as it changes
└── 05_environment/        # the product surface — deliberate stub
```

Read it top to bottom and you have the argument. Stage one is the vocabulary, deliberately minimal and deliberately grayscale. Stage two is where the actual work is: schema, records, research corpus. Stage three is the reference demo. Stage four is research only. Stage five is a stub whose `CONTEXT.md` exists to say why building it now would be a mistake — without real handoff mechanics underneath, it is just a nicer code generator.

Note what that last one costs, which is nothing, and what it buys, which is a lot. The unbuilt part of the system has an address and a stated reason. It is not a ticket someone will find in eight months, and it is not silently absent either.

This is also the concrete answer to the model-independence claim, and it is worth being literal about it. There is no prompt in that tree. Nothing above is phrased for a particular model's temperament. It is a vocabulary, a schema, a set of records, some research, and seven or eight `CONTEXT.md` files stating what each folder owes the next one. Point a different model at the root and it reads its way in the same way. That is not a happy accident of the design — it is the reason for the design.

<figure class="diagram" role="img" aria-label="The workspace view: numbered stage folders, a CONTEXT.md contract in each, and four folder forms that map onto memory registers.">
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1600 902" data-w="1600" data-h="902" role="img" aria-label="The workspace view: numbered stage folders whose reading order is the build order, a CONTEXT.md contract in every folder declaring role, inputs, process, outputs and a human check, and four folder forms that map onto memory registers."><defs><marker id="aG" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="6.5" markerHeight="6.5" orient="auto-start-reverse"><path d="M0,1 L9,5 L0,9 z" fill="#8C8C8C"/></marker><marker id="aP" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="6.5" markerHeight="6.5" orient="auto-start-reverse"><path d="M0,1 L9,5 L0,9 z" fill="#4D6FF2"/></marker><marker id="aT" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="6.5" markerHeight="6.5" orient="auto-start-reverse"><path d="M0,1 L9,5 L0,9 z" fill="#0E6D75"/></marker><marker id="aC" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="6.5" markerHeight="6.5" orient="auto-start-reverse"><path d="M0,1 L9,5 L0,9 z" fill="#B02016"/></marker><marker id="aK" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="6.5" markerHeight="6.5" orient="auto-start-reverse"><path d="M0,1 L9,5 L0,9 z" fill="#8C1EB3"/></marker><filter id="sh" x="-20%" y="-20%" width="140%" height="140%"><feDropShadow dx="0" dy="2" stdDeviation="3" flood-color="#010101" flood-opacity="0.07"/></filter></defs><style>text{font-family:'Atkinson Hyperlegible Next', ui-sans-serif, system-ui, sans-serif;}.lbl{font-size:11.5px;font-weight:700;letter-spacing:.09em;text-transform:uppercase;}.hd{font-size:12.5px;font-weight:700;letter-spacing:.14em;text-transform:uppercase;}</style><text x="70" y="62" class="hd" fill="#2C38F5">The spine  ·  numbered folders, reading order is build order</text><line x1="70" y1="76" x2="1530" y2="76" stroke="#BEBEBE" stroke-width="1.5"/>
<text x="70" y="300" class="hd" fill="#0E6D75">The contract  ·  every folder declares one before it holds anything</text><line x1="70" y1="314" x2="1530" y2="314" stroke="#BEBEBE" stroke-width="1.5"/>
<text x="70" y="659" class="hd" fill="#010101">The forms  ·  a folder type, and the register it actually is</text><line x1="70" y1="673" x2="1530" y2="673" stroke="#BEBEBE" stroke-width="1.5"/>
<text x="70" y="122.5" class="lbl" fill="#2C38F5">01  Design system</text>
<rect x="70" y="134.5" width="270" height="59" rx="10" fill="#FCFCFC" stroke="#2C38F5" stroke-width="1.5" filter="url(#sh)"/>
<text x="88" y="170.5" font-size="13.5" fill="#242424" font-weight="500">What exists to choose from</text>
<text x="370" y="122.5" class="lbl" fill="#8C1EB3">02  Judgment</text>
<rect x="370" y="134.5" width="270" height="59" rx="10" fill="#FCFCFC" stroke="#8C1EB3" stroke-width="1.5" filter="url(#sh)"/>
<text x="388" y="170.5" font-size="13.5" fill="#242424" font-weight="500">Which pattern, and why</text>
<text x="670" y="122.5" class="lbl" fill="#2C38F5">03  Implementation</text>
<rect x="670" y="134.5" width="270" height="59" rx="10" fill="#FCFCFC" stroke="#2C38F5" stroke-width="1.5" filter="url(#sh)"/>
<text x="688" y="170.5" font-size="13.5" fill="#242424" font-weight="500">How it becomes running code</text>
<text x="970" y="112.0" class="lbl" fill="#2C38F5">04  Handoff</text>
<rect x="970" y="124.0" width="270" height="80" rx="10" fill="#FCFCFC" stroke="#2C38F5" stroke-width="1.5" filter="url(#sh)"/>
<text x="988" y="160.0" font-size="13.5" fill="#242424" font-weight="500">How it stays coherent as it</text>
<text x="988" y="181.0" font-size="13.5" fill="#242424" font-weight="500">changes</text>
<text x="1270" y="112.0" class="lbl" fill="#2C38F5">05  Environment</text>
<rect x="1270" y="124.0" width="270" height="80" rx="10" fill="#FCFCFC" stroke="#2C38F5" stroke-width="1.5" filter="url(#sh)"/>
<text x="1288" y="160.0" font-size="13.5" fill="#242424" font-weight="500">What surface hosts it for a</text>
<text x="1288" y="181.0" font-size="13.5" fill="#242424" font-weight="500">team</text>
<text x="70" y="366.0" class="lbl" fill="#0E6D75">CONTEXT.md  ·  the contract</text>
<rect x="70" y="378.0" width="720" height="185" rx="10" fill="#FCFCFC" stroke="#0E6D75" stroke-width="1.5" filter="url(#sh)"/>
<text x="88" y="414.0" font-size="13.5" fill="#242424" font-weight="500">Role · which form this folder is</text>
<text x="88" y="435.0" font-size="13.5" fill="#242424" font-weight="500">Inputs · what it needs, and from where</text>
<text x="88" y="456.0" font-size="13.5" fill="#242424" font-weight="500">Process · the numbered steps</text>
<text x="88" y="477.0" font-size="13.5" fill="#242424" font-weight="500">Outputs · what it produces, and who consumes it</text>
<text x="88" y="498.0" font-size="13.5" fill="#242424" font-weight="500">Human check · what a person must verify</text>
<text x="88" y="519.0" font-size="13.5" fill="#242424" font-weight="500">Nodes · a routing table, never the payload</text>
<text x="88" y="540.0" font-size="13.5" fill="#242424" font-weight="500">Status · what is honestly unbuilt</text>
<text x="850" y="389.0" class="lbl" fill="#2C38F5">What the contract buys</text>
<rect x="850" y="401.0" width="690" height="139" rx="10" fill="#FCFCFC" stroke="#2C38F5" stroke-width="1.5" filter="url(#sh)"/>
<text x="868" y="437.0" font-size="13.5" fill="#242424" font-weight="500">A folder that declares its interface can be loaded on demand instead of all at once.</text>
<text x="868" y="458.0" font-size="13.5" fill="#242424" font-weight="500">No orphan folders. The contract exists before the content.</text>
<text x="868" y="479.0" font-size="13.5" fill="#242424" font-weight="500">Link to the shelf, do not restock it here.</text>
<text x="868" y="498.0" font-size="12.5" fill="#606060" font-weight="400">Human check is prospective memory written into the filesystem. It is an obligation that fires</text>
<text x="868" y="517.0" font-size="12.5" fill="#606060" font-weight="400">on a person rather than a lookup, and every stage has one.</text>
<text x="70" y="725.0" class="lbl" fill="#2C38F5">Factory  →  Procedural</text>
<rect x="70" y="737.0" width="270" height="95" rx="10" fill="#FCFCFC" stroke="#2C38F5" stroke-width="1.5" filter="url(#sh)"/>
<text x="88" y="771.0" font-size="12.5" fill="#606060" font-weight="400">Configured once, not regenerated</text>
<text x="88" y="790.0" font-size="12.5" fill="#606060" font-weight="400">per run. The schema, the tokens,</text>
<text x="88" y="809.0" font-size="12.5" fill="#606060" font-weight="400">the vocabulary.</text>
<text x="370" y="734.5" class="lbl" fill="#8C1EB3">Record Library  →  Semantic</text>
<rect x="370" y="746.5" width="270" height="76" rx="10" fill="#FCFCFC" stroke="#8C1EB3" stroke-width="1.5" filter="url(#sh)"/>
<text x="388" y="780.5" font-size="12.5" fill="#606060" font-weight="400">Accumulating authored instances</text>
<text x="388" y="799.5" font-size="12.5" fill="#606060" font-weight="400">rather than a single output.</text>
<text x="670" y="734.5" class="lbl" fill="#0E6D75">Knowledge Bundle  →  Grounding</text>
<rect x="670" y="746.5" width="270" height="76" rx="10" fill="#FCFCFC" stroke="#0E6D75" stroke-width="1.5" filter="url(#sh)"/>
<text x="688" y="780.5" font-size="12.5" fill="#606060" font-weight="400">Linked research nodes. The</text>
<text x="688" y="799.5" font-size="12.5" fill="#606060" font-weight="400">citations underneath the judgment.</text>
<text x="970" y="734.5" class="lbl" fill="#010101">Contract  →  Working</text>
<rect x="970" y="746.5" width="270" height="76" rx="10" fill="#FCFCFC" stroke="#010101" stroke-width="1.5" filter="url(#sh)"/>
<text x="988" y="780.5" font-size="12.5" fill="#606060" font-weight="400">The stage itself. Where one run</text>
<text x="988" y="799.5" font-size="12.5" fill="#606060" font-weight="400">gets assembled.</text>
<text x="1270" y="734.5" class="lbl" fill="#8C8C8C">Run Log  →  Episodic</text>
<rect x="1270" y="746.5" width="270" height="76" rx="10" fill="#FCFCFC" stroke="#8C8C8C" stroke-width="1.5" filter="url(#sh)"/>
<text x="1288" y="780.5" font-size="12.5" fill="#606060" font-weight="400">Proposed. Every run’s artifact,</text>
<text x="1288" y="799.5" font-size="12.5" fill="#606060" font-weight="400">append only. No form for this yet.</text>
<path d="M346,164.0 H362" fill="none" stroke="#4D6FF2" stroke-width="1.6" marker-end="url(#aP)"/>
<path d="M646,164.0 H662" fill="none" stroke="#4D6FF2" stroke-width="1.6" marker-end="url(#aP)"/>
<path d="M946,164.0 H962" fill="none" stroke="#4D6FF2" stroke-width="1.6" marker-end="url(#aP)"/>
<path d="M1246,164.0 H1262" fill="none" stroke="#4D6FF2" stroke-width="1.6" marker-end="url(#aP)"/>
<path d="M505.0,199.5 V370.0" fill="none" stroke="#0E6D75" stroke-width="1.6" marker-end="url(#aT)"/>
<rect x="379.7" y="216.5" width="250.6" height="20" rx="4" fill="#E8E8E8"/><text x="505.0" y="230.5" class="lbl" fill="#0E6D75" text-anchor="middle">every stage, not just this one</text></svg>
<figcaption>Numbered stages in dependency order, a contract in every folder, and four folder types that turn out to be the memory registers wearing different clothes.</figcaption>
</figure>

Two things fell out of putting that next to the register sort, and I designed neither of them in.

**The folder forms are the registers.** A Factory gets set up once and never regenerated, which is procedural. A Record Library piles up authored instances, which is semantic. A Knowledge Bundle is the research holding both up, the citations under the judgment. A Contract is the stage where one run gets put together, which is working memory. Four forms adopted for navigation, landing exactly on registers named for something else entirely, in a literature neither vocabulary was drawn from.

That convergence is the best evidence I have that the shape is right rather than merely tidy. A filing scheme chosen for how it reads should not independently reproduce a taxonomy from memory research. When two vocabularies built for unrelated reasons land on the same four distinctions, the distinctions are probably in the problem rather than in either vocabulary.

Which also shows the hole. There is no folder type for episodic. Runs, scores, decisions and audit logs have nowhere structural to go, so they end up in whatever logging the execution layer happens to have, outside the workspace and outside the contract. That is a fifth type waiting to be written, and until it exists the consolidation loop has no address.

**And the human check field is prospective memory written into the filesystem.** Every stage says what a person has to verify before its output can be trusted. That is not documentation and it is not a lookup. It is an obligation pointed at a named human, sitting in the same file as the routing table, which is why it survives when a wiki page would not.

There is one more layer and it is the one I have run longest. Capture happens live, into three tiers: constraints, decisions, context. That is how you get reasoning out of a working session before it evaporates. The numbered workspace is where it goes to become durable and addressable. The two are different problems and you need both. **The tier says which rule wins. The stage number says which folder feeds which.** Most knowledge systems fail because they only solve one of those.

The bit I have not built is the join. Capture that writes straight into a numbered stage, tagged by type, instead of into one flat folder somebody sorts by hand later. That is the difference between something that works for one person and something a team can run.


## What is settled, and what is still a bet

Two different things are in play here and running them together would be the easiest way to overclaim, so I want to separate them.

**The harness itself is not speculative.** Folder-as-architecture has fifty-two practitioners and three production workflow types behind it, and that is somebody else's work, not mine. The three-tier capture split — constraints, decisions, context — is the thing I have run longest and would rebuild first on any new project. Contract-before-content has survived every workspace I have applied it to, mostly by making a specific failure impossible rather than by making anything better. The generate/score/decide separation came over from a genomic interpretation pipeline where a plausible wrong answer has real consequences, and it transferred without modification. None of that is a proposal. It is how the setup works, and it is buildable this afternoon.

**The judgment layer is the newest piece and the least proven.** The schema is constructible, the matcher is deterministic, the records are real and cited. What I have not shown is that any of it changes what a model produces. That is a genuine hole and I would rather name it than write around it.

So here is what a harness shaped this way is *for* — four outcomes, in descending order of how confident I am.

**A much smaller surface that needs human judgment.** Most of what gets called spatial or physical design constraint is hard primitive value — touch target minima, spacing minima, contrast ratios — that should be encoded once and enforced mechanically instead of re-derived per project. Sort a real design system into the four levels and the enforceable fraction should be large, and the residue should land at pattern level specifically. That is the outcome the whole argument rests on, and the cheapest one to check, because checking it does not require running a model at all. Orbit is outside corroboration: they closed spacing, colour, semantics and theme, and stopped exactly where pattern choice begins.

**Output that moves, not just rationale that moves.** A retrieved record should change what the model generates, not merely what it says about what it generated. That distinction is the whole game. A record that improves the stated reasoning while the rendered pattern stays parked at the statistical mode would be a post-hoc explanation generator — precisely the failure this argument accuses after-the-fact compliance checking of.

**A judgment layer that is not yours.** The records describe conditions and principles, not one vocabulary, so pointing the library at a different design system should be a matter of remapping pattern names rather than rewriting justifications. If that holds, the judgment layer stops being a team asset and starts being a field asset — something a profession could hold in common the way it holds Fitts's Law in common.

**A library that appreciates.** A record authored for one condition should earn value on later conditions nobody anticipated when writing it, so coverage grows faster than authoring effort. That is the difference between an asset and a lookup table, and it is the outcome furthest out — five records cannot compound, and no amount of architecture makes them.

The comparison that actually matters underneath all four is not context against no context. Nobody argues context helps. It is **encoded once against pasted in every time**. Somebody who pastes the same rationale into a prompt might get the same output as a system that retrieves it automatically. That is the null this has to beat, and beating it is what separates a system from a very good snippet.


## The short version

Documentation used to describe a system from outside it. Skills, memory files, system instructions, agent-readable metadata, a folder contract. All of it is documentation, and every one of them now runs inside the thing it describes. That is the shift under all of this, and most of the advice being written right now has not caught up.

Which is why the answer is not to write more. It is to sort it. Most of what you would write down should compile into something that cannot be typed wrong, and stop being prose. What is left is thin, needs actual judgment, needs to be there at the moment of the decision instead of loaded in advance, and needs a gate no model gets a vote in.

That surviving row deserves a format rather than a folder of documents, which is what cairn is an attempt at. A pattern, the condition it applies under, the principle behind it, the alternatives it beat, and an honest note about where it came from. Authored once by a person, retrieved automatically at the moment it is needed, and loud about the conditions nobody has ruled on yet.

If you want to start, do not start with the records. Start with one folder that declares what it is, what it needs, what it produces, and what a person has to check before its output can be trusted. Then refuse to put anything in a folder that has not done that. Almost everything else in this piece is downstream of that one rule, and it is a rule you can apply before lunch.

The harness gets smaller. It also gets to stay. Whatever model you are using in eighteen months will read the same files, and none of the work you did on it will need doing again.


---

*Diagrams are set in the Vanta design system: Atkinson Hyperlegible, and a colour ramp whose hues are derived from real emission wavelengths. Cadmium, Calcium H, hydrogen H-beta and H-gamma, on Vantablack and Purdue white.*
