---
layout: post
title: "Why I stopped calling this 'context architecture'"
date: 2026-07-21 09:00:00 -0700
categories: design-engineering
---

I've been trying to name what I'm actually doing underneath the [Component Lab work]({{ site.baseurl }}/building-an-ai-assisted-code-first-design-practice.html), and for a while I kept reaching for "context architecture." It's the phrase everywhere right now — every AI-adjacent LinkedIn post, half of them clearly drafted by the same models they're describing, all converging on the same handful of words. I used it too. Then I noticed I couldn't actually explain what I meant by it beyond "I keep documentation that helps the AI stay on track," which is true but not interesting, and not really what's going on.

## Two things I keep seeing, and neither is this

There are two visible patterns in how designers are working with AI right now. One is showing off what a model can produce — screenshots of a slick flow, a prompt that generated something impressive. Fine, that's a real skill, but it's a demonstration, not a description of a practice.

The other is designers vibe-coding real products into existence — orchestrating a model to write actual functioning code, the way an engineer would, and getting something that works. I've seen an engineer describe why this feels "off" to them, and I think they're right: for an engineer, the code *is* the craft. Knowing when to reach for one pattern over another, when to build the tool you need instead of forcing an existing one to fit, is the same kind of judgment a woodworker has about which joint to cut and when to build a jig on the spot because the right tool doesn't exist yet. A vibe-coded product can work without any of that judgment being present. It's real, it's functioning, and it's often not designed at the level that judgment operates at.

I don't think what I've been doing is either of those. I'm not demonstrating AI output, and I'm not using AI to do an engineer's job. I've been using it to do more of *my* job — the designer's job — at a scale my own memory can't sustain by itself.

## What the actual practice looks like

Concretely: I capture decisions, constraints, and reasoning as I make them, not after the fact. Why a component is shaped the way it is. Why we rejected an alternative. What a token means and doesn't mean. That capture is the boring, unglamorous majority of the work, and it's also the part that keeps me from having to reconstruct the same judgment call three months later when I've forgotten I already made it.

The more interesting part is noticing when Claude's output drifts from that judgment — and it's the same instinct I'd use to notice a layout is off, just pointed at a different target. When something comes back that doesn't fit, I don't just fix the one output. I try to figure out *why* it drifted, and then I adjust the system: the doc, the rule, the hook that fires before or after a given kind of work. The goal isn't to catch every mistake once. It's to make the same mistake stop happening.

## What I think this actually is

I sat with the "not context architecture, then what" question for a while, and I think I landed on something more useful than a new name for a new thing. It's an old name for the same thing, pointed somewhere it hasn't usually pointed.

Information architecture has always meant organizing what a system knows so an audience can find, understand, and correctly use the right piece of it. The audience was always assumed to be a person — someone navigating a site, a product, a system. Nothing about that definition actually requires the reader to be human. It just always was one, because there wasn't another kind of reader worth designing for.

There is now. A model working through a context window has the same problem a person has navigating an unfamiliar system: find the right piece of information, understand it, apply it correctly, without drowning in everything that isn't relevant. When Claude's output drifts from established judgment, that's not a mysterious AI failure. It's a findability failure — the same failure mode IA has always diagnosed, just with a different kind of reader on the other end. The fix is the same too: clearer labeling, the right hierarchy, cutting what doesn't belong, making sure the right thing is discoverable exactly when it's needed. None of that requires a retrieval pipeline or a graph database. Most of the time it's closer to a single living `DESIGN.md` than an engineered system — just enough structure that the model doesn't stray, not an attempt to formally model everything it might ever need to know.

I've spent most of my 25 years in design practicing information architecture in its traditional form. What I've been doing for the last year and a half is the same discipline, aimed at a second audience that didn't exist when the field was named.

## Is this actually rare, or am I just noticing it

Worth being honest about this instead of asserting it: I don't know if the underlying mechanism is rare. Capturing decisions, noticing drift, correcting the system — versions of that are already common practice among people building seriously with AI, especially engineers who've been living inside CLAUDE.md and AGENTS.md files for a while now. I'm not claiming to have invented that.

What I think is less common is arriving at it from IA specifically, with the years of practice that come with it, rather than arriving at it from the engineering side without the vocabulary or theory behind why it works. Plenty of people are converging on "labeling matters, hierarchy matters, findability matters" right now. Most of them are getting there for the first time. I've had a couple of decades of pattern recognition on exactly those problems, for a different audience, and I think that's the actual difference — not a new practice, but an old one recognizing where it already belonged.

What I can point to instead of asserting it: the design team is fully off Figma for prototyping, the tooling spread to the rest of the product org on its own, and that adoption curve is the reason I'm now on our AI Adoption Tiger Team. That's evidence the practice works. Whether it's rare is still an open question, and I'd rather leave it open than oversell it.
