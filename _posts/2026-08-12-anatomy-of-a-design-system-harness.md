---
layout: post
title: "Anatomy of a design system harness"
date: 2026-08-12 07:00:00 -0700
categories: design-engineering
---

<style>
figure.diagram{width:100vw;max-width:100vw;margin:2.6rem 0 2.6rem calc(50% - 50vw);padding:1.5rem 1rem;background:#FCFCFC;border-top:1px solid #BEBEBE;border-bottom:1px solid #BEBEBE;box-sizing:border-box;}
figure.diagram svg{display:block;width:100%;max-width:1500px;margin:0 auto;height:auto;}
figure.diagram svg text{font-family:"Atkinson Hyperlegible Next",sans-serif;}
figure.diagram figcaption{max-width:1500px;margin:.9rem auto 0;font-size:.82rem;font-style:italic;color:#606060;line-height:1.5;}
.sources{width:100vw;max-width:100vw;margin:2.6rem 0 0 calc(50% - 50vw);padding:1.6rem 1rem 1.8rem;background:#FCFCFC;border-top:1px solid #BEBEBE;border-bottom:1px solid #BEBEBE;box-sizing:border-box;}
.sources-inner{max-width:1100px;margin:0 auto;}
.sources-label{font-variant:small-caps;letter-spacing:.08em;color:#606060;font-size:.78rem;margin:0 0 .9rem;}
.sources-list{font-size:.8rem;color:#606060;line-height:1.7;margin:0;max-width:80ch;}
.sources-list a{color:#242424;}
</style>

*A design system tells you what exists. A harness is everything around it that decides what gets built, checks whether the build was right, and carries the reasoning between the two. This is the model I've arrived at after two years of running one, laid out part by part.*

**Executive summary**

- **A harness is not a design system, and it's not the model.** It's the connective structure: sources of truth, a guidance layer, execution, gates, and a path back. The design system is one input to it.
- The substrate is **ICM, Interpretable Context Methodology**, from Jake Van Clief and David McDermott. Folder structure as agentic architecture. A small set of folder forms, a contract in every folder, reading order as build order.
- Ask one question of every rule in your design system: **can a machine check it?** That question sorts everything else. It sorts into two destinations, not one folder.
- Most of what we write down passes. Spacing. Contrast. Tokens, theme, semantics. All of it should compile into something you can't type wrong, and stop being prose.
- What survives is pattern choice. Accordion or list. Card or row. Modal or panel. That's the only part needing real judgment, and it's far thinner than it first appears.
- What survives gets a format, not a folder of documents. A **judgment record**: a pattern, the condition it applies under, the principle justifying it, and the alternatives it beat. Schema, records, research corpus, and a matcher that resolves a condition the same way every time. The repo is **cairn**.
- Three things make that format work instead of just exist. Precedence, so a tie between two records has an answer. Retrieval at the decision, not a load at the start of a session. And a hard stop when nothing matches. A generator that keeps going falls back on the model's own priors, quietly, at full confidence. That's where drift comes from.
- The gate is where this argument usually goes vague. Models generate and models score. A plain function decides. Nothing that generates gets a vote on its own output.
- The folder forms turn out to be memory registers wearing different clothes. That convergence is the strongest sign the shape is right rather than merely tidy.


## What a harness is

Start with the word, because it does a lot of work here and I mean something specific by it.

A design system is a vocabulary. Tokens, components, patterns. It tells you what exists and what things are called. It does not tell you which one to reach for, whether the thing that got built was the right thing, or why the last person chose what they chose.

A harness is everything around that. Sources of truth on one side. A guidance layer that carries what the vocabulary can't. An execution layer. Gates that check the work. And a path from what the gates learn back into the guidance. The design system sits inside it as one input among several.

> A design system says what exists. A harness decides what gets built and checks whether it was right.

The distinction matters because the two get collapsed constantly. Teams write more documentation into the design system and expect it to start behaving like a harness. It won't. A vocabulary can't hold a decision procedure, and stuffing one into the other is why design systems buckle once AI is writing the code against them.

So this is a model of the harness, part by part. Not a survey and not a methodology I'm selling. It's where I landed after building one, running it, and rebuilding it twice.

Here's the version I ran longest, with the parts specified. Guidance is tiered instead of piled flat, because sooner or later two rules contradict each other and one of them has to win. The execution layer is honestly interchangeable. Two gates rather than one, because tests and CI check whether the code runs, not whether it was worth building. And the path back is a loop, because a system that can't learn from what it shipped goes stale.

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

That version works. I'd still defend it. It's also too big, and that's what the rest of this is about.


## The substrate isn't mine

The harness needs a shape before any of the design-specific parts go in it. I want to be precise about where that shape came from, because it's the part of this with real validation behind it instead of my own conviction.

It's called **ICM, Interpretable Context Methodology**. Jake Van Clief and David McDermott, [*Folder Structure as Agentic Architecture*](https://arxiv.org/abs/2603.16021), March 2026. The core claim is exactly what the subtitle says. The folder hierarchy isn't where the context gets filed. It *is* the architecture. Their method replaces framework-level orchestration with filesystem structure, and numbered folders carry the stages.

That's a formalization of something I'd been doing by feel, written down by people who bothered to specify it. So I stopped inventing my own structure and adopted theirs.

What you get is a small vocabulary of **forms**, and the discipline is that every folder has to be one of them and say which. A **Pipeline** is sequential stages where reading order is dependency order. A **Record Library** accumulates instances instead of producing one output. A **Knowledge Bundle** is linked research holding the other forms up. An **Umbrella** groups parallel work. A **Context Map** routes. My workspace adds a **Factory**, which is stable reference material configured once and never regenerated per run, and treats each stage folder as a **Contract**.

That sounds like filing. It isn't.

What makes it not filing is one rule: **every folder declares a contract before it's allowed to hold anything**. A `CONTEXT.md`, same fields every time. Which form this folder is. What it needs and where from. The steps. What it produces and who takes it. What a person has to check. A routing table of what's inside. An honest note about what isn't built yet. No orphan folders. The contract comes before the content.

Three consequences, and all three took me a while to appreciate.

**An agent orients by reading, not by being told.** The routing lives in the same tree as the material. A session starts by reading its way in instead of having a human paste an orientation paragraph. That paragraph is exactly the thing that would otherwise be model-specific scaffolding.

**A stage can be empty. It can't be silent about being empty.** In my workspace the product-surface stage is a stub whose `CONTEXT.md` exists to say why building it now would be a mistake. That's how you hold an unbuilt thing without pretending it exists or forgetting it should.

**The forms constrain what you're allowed to build.** Call a folder a Record Library and you've committed to it accumulating. Call it a Factory and you've committed to it being configured once. The vocabulary makes a certain kind of sprawl harder to do by accident. That's most of what a methodology is for.

One note on timing, stated narrowly. My own knowledge-base repo doing folder-layered context has a first commit in January 2026, two months before the ICM paper went up on 17 March. That's a git-verifiable fact about when I was working on this and nothing more. They wrote the method down and specified it. I had a habit. Getting somewhere earlier isn't the same as getting there with a specification.


## One question sorts all of it

Ask this about every rule you've written down. **Can a machine check it?**

Sounds like a detail. It's the whole fork.

There's good evidence for where the line falls. Polar built a design system called Orbit specifically to survive being written by an LLM. Their argument is that docs can't close an infinite surface, because a model has endless ways to be slightly wrong. Padding four instead of five. One gray instead of the gray next to it. So they stopped documenting and started closing. Typed props instead of class strings. Raw elements banned by lint. Dark mode collapsed into one value so it's not something the model has to remember. CI is the contract. Docs are advice.

Here's the part that matters. They closed spacing, color, semantics, and theme. They left pattern choice completely alone. Nothing in Orbit decides accordion against tabs against a side rail.

They locked down what a thing looks like and never touched which thing to use.

That's outside confirmation. The enforceable part is real, it's bigger than most designers assume, and it has an edge to it.

The edge already has a name older than anything I came up with. Memory research splits declarative knowledge, the stuff you look up, from procedural knowledge, the stuff that shapes what you do without ever being read. A lint rule is procedural. A retrieved rationale is declarative. Borrow the older name, because the failure modes come with it.

So what a machine can check gets compiled and stops being documentation. What it can't gets stored where you can retrieve it. Two destinations.

Almost everyone puts both in the same folder.


## So I built the thing that holds what's left

Run a design system through that question and most of it walks out. What stays is pattern choice. Given this content, this audience, this much room, this viewport: accordion or flat list, card or list row, modal or inline panel. No token decides that. No linter can.

Saying that is easy. What I couldn't hand-wave was what the surviving row is actually *made of*.

So I gave it a format and built it. The repo is **cairn**. The unit it's built around is a single judgment: a condition comes in, a pattern goes out, with a stated reason.

That unit is a **judgment record**. Eight required fields, and the interesting thing is which ones.

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
    "citation": "Nielsen Norman Group, Progressive Disclosure; Miller's Law",
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
        chooses between. This content is supplementary to a primary flow." }
  ],
  "origin": { "type": "universal", "authored_by": "…", "authored_date": "2026-07-24" }
}
```

Three of those fields do the real work. They're also the three that would be easiest to leave out.

**`constraint_level` and `enforcement` encode the sorting question directly.** Four levels: primitive, component, pattern, block-surface. Enforcement is hard, soft, or partial. A pattern-level record should almost always be soft. A primitive-level one should almost always be hard. That isn't decoration. It makes the taxonomy a schema field a machine can check instead of an argument in a blog post. One of the five records exists purely as the contrast case: a 44×44pt touch target, `constraint_level: primitive`, `enforcement: hard`, `linter_checkable: true`. It's in the library to mark the boundary. It's the shape of the thing that should *not* be a retrieved record at all.

**`alternatives_considered` is mandatory, minimum one.** This is the field I'd defend hardest. It's what makes a record falsifiable instead of an assertion. Anyone can write down that they chose an accordion. Writing down that tabs got rejected because tabs imply mutually exclusive parallel sections is a claim someone can come back and argue with. A record with no rejected alternatives is a preference wearing a citation.

**`origin.type` splits universal from org-specific from regulatory.** Universal is citable and safe to publish. Org-specific is the local reason a model could never infer, and it never leaves the building. That field decides what could ever become a shared asset across teams and what stays permanently yours.

Then the matcher, which is deliberately stupid. Score every record against the incoming condition. A record matches only if every field it declares agrees. Most specific match wins, meaning the one with the most declared fields. No embeddings. No semantic similarity. No model in the loop. Run the demo, pick a condition across four selects, and watch it resolve to a pattern, its rationale, and the alternatives it beat, rendered as a literal grayscale wireframe.

The behavior I care about most is what happens when nothing matches. It says so and stops.

No match is a real result. It means nobody has ruled on this condition yet.

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

Three things change once you accept that. Guardrails stop being something output passes through and become something input can't get around, so you don't catch the wrong token afterward, you make it impossible to type. Guidance stops being a document and becomes something the generator calls. And if the judgment layer really is portable, it stops belonging to your team at all.

Yes, this one nests too. Nesting is right when the containment is real, and a bounded set of options genuinely does contain everything the generator can produce. It's wrong when it isn't. Which is why documentation should never be drawn as containing a codebase.


## What that thin row needs, and where mine is still thin

Thin doesn't mean free. Three things have to be true or the format is just a nicer way to store opinions. The build does two of them. It doesn't do the third, and that's the honest gap.

**It needs precedence, not a list.** Two records will contradict each other eventually and one has to win. The capture layer I've run longest handles this with three tiers. Constraints are hard rules and the agent flags a violation. Decisions carry the choice, the reasoning, and when to revisit. Context informs without constraining. I keep constraints rare on purpose, because if everything is a constraint then nothing is.

**cairn doesn't solve this.** Two records matching the same condition with conflicting recommendations have no resolution beyond most-specific-wins, and most-specific-wins is a tiebreak, not a precedence model. It's the first thing I'd fix.

**It needs retrieval at the decision, not a load at the start.** A record gets matched against the actual condition at the moment it matters. That's a different thing from stuffing a context window at the top of a session and hoping. It fails differently too. The failure is no-match instead of forgetting, and no-match you can see. Being blunt about the current mechanism: matching is exact-field, nothing semantic. A condition phrased slightly differently misses. That's a real limit. I'd rather it miss loudly than fuzzy-match its way to a confident wrong record.

**It has to stop when nothing matches.** A generator that keeps going without a record doesn't produce nothing. It falls back on everything the model already believes about interfaces. Quietly. At full confidence. That looks like success.

That's where drift comes from, and you can't see it, because the output is perfectly plausible.

> Every drift problem is the model's own priors winning.

Which changes the diagnosis. The model didn't get it wrong. It did what any unbriefed model does. Stopping loudly turns that silence into a queue, and the queue is the only reason the system ever improves.


## Let the model score it. Don't let it decide.

That leaves the gate, and the gate is where these arguments usually go vague. Check the output against the record, people say, and then never explain how.

I had the same gap. Then I realized I'd already built the mechanism somewhere else, in a domain with a lot less tolerance for a plausible wrong answer. A seven node pipeline for genomic interpretation. The rule it runs on moves over without changes.

> Models generate and models score. A function decides. Nothing that generates gets a vote on its own output.

A critic scores the output against records pulled from a real knowledge base instead of against its own instincts. Three things. Consistency catches claims the evidence doesn't support. Alignment catches the hard parts that got quietly skipped. Specificity catches output that sounds fine and would have come out the same for any input.

Sit with that last one. Specificity is the sameness problem with a number on it. Everyone complains that AI interfaces all look the same. Almost nobody measures it.

The critic can be a model, because it reads an artifact and scores it against something outside itself. The decision can't be. If the generator can influence the verdict, there's no verdict.

So the decision is a plain threshold function. Unit testable on its own, auditable without running a model at all. Pass, revise twice, then fail. Three outcomes instead of two, because real systems need a middle and an unlimited retry isn't one.

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

Every run writes out a typed, versioned artifact. Scores, decision, revision count, and an audit log nothing can edit after the fact. That's what makes the feedback loop real instead of aspirational.

Four signals come out of it and they go four different places. No match means nobody has ruled on this yet, so write a record. Override means someone rejected what came back, so the record is wrong. Stale means the system moved past a version pin. Score drop means drift, caught before anyone sees it.

A feedback loop that dumps everything into one inbox is a suggestion box. Four signals with four destinations is a mechanism.


## Seven kinds of memory, two of which aren't memory

There's a vocabulary going around for agent memory. Working, semantic, episodic, procedural, retrieval, parametric, prospective. It usually shows up as seven parallel things, which is the one thing it definitely isn't. Sort it properly and it explains something the stage view can't.

Three of them are stores you keep. Semantic is what's true regardless of when, so the records and the vocabulary. Somebody wrote it, it's pinned to a version, it gets looked up. Procedural is how the thing gets built, so the compiled set. Nobody reads it and it shapes the work anyway. Episodic is what happened on each run, so the audit log. It piles up on its own and nobody authors it.

One is a buffer. Working memory is the run itself. Bounded, thrown away at the end.

One is a trigger. Prospective memory is remembering to do something later, and almost every harness has it without calling it that. A version pin that fires when the system moves past it. A review date that fires on a named person. A question sitting in a queue until somebody answers it. All the same kind of thing, usually built three separate ways. It's the only one that fires on time instead of on lookup.

And two of the seven aren't stores at all. **Retrieval is the transport**, how a store reaches the buffer. It's an arrow, not a box. Treating it as a seventh cupboard is how teams end up with a memory service nobody can describe the contents of. **Parametric is the substrate**. You can't write to it, version it, review it, or pin it. The only thing about it that matters here is that it fills any gap instantly and confidently.

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

Sorting it this way turned up a correction I didn't expect. The return path in the first diagram says capture. Capture is the wrong word. Episodic piles up for free. Semantic has to be written. The step between them is **consolidation**, turning one specific thing that happened into a general rule. That's the expensive human bit. It's the one part of the loop you can't automate, and calling it capture makes it sound like storage.


## Where all this actually lives

An architecture that only exists in a diagram is a diagram. All of it has to sit somewhere an agent can reach at the moment it needs it, and reaching it isn't the same problem as storing it.

So here's the whole thing as a directory, which is the only form of it that actually runs. Five stages, numbered, because the numbering is the dependency order and reading order is build order. Stage two can't reason about accordion against card unless stage one has already made both named things. So stage one goes first, and says so.

```
cairn/
├── CLAUDE.md              # read first, every session: where am I, where do I go
├── CONTEXT.md             # the pipeline definition and why it's shaped this way
├── 01_design-system/      # the bounded vocabulary a decision can resolve to
│   ├── CONTEXT.md
│   ├── tokens.json
│   └── components/vocabulary.json
├── 02_judgment-layer/     # the working core
│   ├── CONTEXT.md
│   ├── schema/            # Factory: the record format, configured once
│   ├── records/           # Record Library: accumulating instances
│   └── knowledge/         # Knowledge Bundle: the research holding both up
├── 03_implementation/     # where a judgment becomes a running artifact
├── 04_handoff/            # keeping that artifact coherent as it changes
└── 05_environment/        # the product surface. deliberate stub
```

Read it top to bottom and you have the argument. Stage one is the vocabulary, deliberately minimal and deliberately grayscale. Stage two is where the actual work is: schema, records, research corpus. Stage three is the reference demo. Stage four is research only. Stage five is a stub whose `CONTEXT.md` exists to say why building it now would be a mistake. Without real handoff mechanics underneath, it's just a nicer code generator.

That last one costs nothing and buys a lot. The unbuilt part of the system has an address and a stated reason. It isn't a ticket someone finds in eight months. It isn't silently absent either.

Worth naming what that tree is made of, because it's the clearest statement of what a harness actually is. There's no prompt in it. It's a vocabulary, a schema, a set of records, some research, and seven or eight `CONTEXT.md` files stating what each folder owes the next one.

That's the whole harness. Structure and stated obligations, sitting where the work happens.

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

Two things fell out of putting that next to the register sort. I designed neither of them in.

**The folder forms are the registers.** A Factory gets set up once and never regenerated, which is procedural. A Record Library piles up authored instances, which is semantic. A Knowledge Bundle is the research holding both up, the citations under the judgment. A Contract is the stage where one run gets put together, which is working memory. Four forms adopted for navigation, landing exactly on registers named for something else entirely, in a literature neither vocabulary was drawn from.

That convergence is the best evidence I have that the shape is right rather than merely tidy. A filing scheme chosen for how it reads shouldn't independently reproduce a taxonomy from memory research. When two vocabularies built for unrelated reasons land on the same four distinctions, the distinctions are probably in the problem rather than in either vocabulary.

It also shows the hole. There's no folder form for episodic. Runs, scores, decisions, and audit logs have nowhere structural to go, so they end up in whatever logging the execution layer happens to have. Outside the workspace. Outside the contract. That's a fifth form waiting to be written, and until it exists the consolidation loop has no address.

**The human check field is prospective memory written into the filesystem.** Every stage says what a person has to verify before its output can be trusted. That isn't documentation and it isn't a lookup. It's an obligation pointed at a named human, sitting in the same file as the routing table. Which is why it survives when a wiki page wouldn't.

One more layer, and it's the one I've run longest. Capture happens live, into three tiers: constraints, decisions, context. That's how you get reasoning out of a working session before it evaporates. The numbered workspace is where it goes to become durable and addressable. Two different problems, and you need both. **The tier says which rule wins. The stage number says which folder feeds which.** Most knowledge systems fail because they only solve one of those.

The bit I haven't built is the join. Capture that writes straight into a numbered stage, tagged by type, instead of into one flat folder somebody sorts by hand later. That's the difference between something that works for one person and something a team can run.


## What is settled, and what is still a bet

Two different things are in play here. Running them together is the easiest way to overclaim, so let me separate them.

**The harness isn't speculative.** Folder-as-architecture is specified and published, and that's somebody else's work, not mine. The three-tier capture split is the thing I've run longest and would rebuild first on any new project. Contract-before-content has survived every workspace I've applied it to, mostly by making one specific failure impossible rather than by making anything better. The generate/score/decide separation came over from a genomic interpretation pipeline where a plausible wrong answer has real consequences, and it transferred without modification.

None of that is a proposal. It's how the setup works, and you can build it this afternoon.

**The judgment layer is the newest piece and the least proven.** The schema is constructible. The matcher is deterministic. The records are real and cited. What I haven't shown is that any of it changes what a model produces. That's a genuine hole, and I'd rather name it than write around it.

So here's what a harness shaped this way is *for*. Four outcomes, in descending order of how confident I am.

**A much smaller surface that needs human judgment.** Most of what gets called spatial or physical design constraint is hard primitive value. Touch target minima. Spacing minima. Contrast ratios. All of it should be encoded once and enforced mechanically instead of re-derived per project. Sort a real design system into the four levels and the enforceable fraction should be large, with the residue landing at pattern level specifically. That's the outcome the whole argument rests on, and the cheapest one to check, because checking it doesn't require running a model at all. Orbit corroborates it from the outside. they closed spacing, color, semantics, and theme, and stopped exactly where pattern choice begins.

**Output that moves, not just rationale that moves.** A retrieved record should change what the model generates, not what it says about what it generated. That distinction is the whole game. A record that improves the stated reasoning while the rendered pattern stays parked at the statistical mode is a post-hoc explanation generator. Which is precisely the failure this argument accuses after-the-fact compliance checking of.

**A judgment layer that isn't yours.** The records describe conditions and principles, not one vocabulary. Pointing the library at a different design system should be remapping pattern names, not rewriting justifications. If that holds, the judgment layer stops being a team asset and becomes a field asset. Something a profession holds in common the way it holds Fitts's Law in common.

**A library that appreciates.** A record authored for one condition should earn value on later conditions nobody anticipated when writing it, so coverage grows faster than authoring effort. That's the difference between an asset and a lookup table. It's also the outcome furthest out. Five records can't compound, and no amount of architecture makes them.

The comparison underneath all four isn't context against no context. Nobody argues context helps. It's **encoded once against pasted in every time**. Somebody who pastes the same rationale into a prompt might get the same output as a system that retrieves it automatically. That's the null this has to beat. Beating it is what separates a system from a very good snippet.


## The short version

Documentation used to describe a system from outside it. Skills, memory files, system instructions, agent-readable metadata, a folder contract. All of it is documentation, and every one of them now runs inside the thing it describes. That's the shift under all of this, and most of the advice being written right now hasn't caught up.

So the answer isn't to write more. It's to sort it. Most of what you'd write down should compile into something you can't type wrong, and stop being prose. What's left is thin. It needs real judgment, it needs to be there at the decision instead of loaded in advance, and it needs a gate no model gets a vote in.

That surviving row deserves a format, not a folder of documents. A pattern, the condition it applies under, the principle behind it, the alternatives it beat, and an honest note about where it came from. Authored once by a person. Retrieved automatically when it's needed. Loud about the conditions nobody has ruled on yet.

If you want to start, don't start with the records. Start with one folder that declares what it is, what it needs, what it produces, and what a person has to check before its output can be trusted. Then refuse to put anything in a folder that hasn't done that.

Almost everything else here is downstream of that one rule. You can apply it before lunch.

The harness gets smaller. The part that stays gets a lot more serious.

<div class="sources"><div class="sources-inner">
<p class="sources-label">Sources</p>
<p class="sources-list">Jake Van Clief and David McDermott, <a href="https://arxiv.org/abs/2603.16021">Interpretable Context Methodology: Folder Structure as Agentic Architecture</a> (arXiv, March 2026) · <a href="https://github.com/RinDig/icm-architect">ICM-Architect</a>, an independent open-source implementation of the same five forms · <a href="https://polar.sh/blog/orbit-llm-safe-design-system">Building an LLM-Safe Design System</a>, Polar engineering, on Orbit · <a href="https://lawsofux.com/">Laws of UX</a>, Jon Yablonski, for Miller's Law and Fitts's Law · <a href="https://www.nngroup.com/articles/tabs-used-right/">Tabs, Used Right</a>, Nielsen Norman Group · Shumailov, Shumaylov, Zhao, Papernot, Anderson and Gal, "AI models collapse when trained on recursively generated data," <em>Nature</em> 631, 755–759 (2024), read alongside <a href="https://arxiv.org/abs/2410.12954">A Note on Shumailov et al. (2024)</a> · <a href="https://arxiv.org/html/2411.03477v2">CrowdGenUI</a> and <a href="https://arxiv.org/pdf/2601.17614">AlignUI</a>, preference-driven UI generation · <a href="https://arxiv.org/pdf/2511.00843">Portal UX Agent</a>, bounding selection without reasoning about it · <a href="https://github.com/UGAIForge/DesignRepair">DesignRepair</a> · <a href="https://github.com/nexu-io/open-design">Open Design</a> · <a href="https://salt-nlp.github.io/Design2Code/">Design2Code</a>, Stanford SALT · <a href="https://research.google/pubs/api-governance-at-scale/">API Governance at Scale</a>, Google Research, for the governance structure the tiering borrows from</p>
</div></div>

---

*Diagrams are set in Vanta: Atkinson Hyperlegible, and seven color ramps whose hues are each a real, measured atomic emission line. Cadmium, krypton, helium, oxygen, hydrogen H-beta and H-gamma, calcium. Black and white are sourced the same way, from Vantablack and Purdue white rather than `#000` and `#fff`. These diagrams draw on four of the seven. [How the palette was derived.]({{ site.baseurl }}/vanta-a-color-system-with-a-citation.html)*
