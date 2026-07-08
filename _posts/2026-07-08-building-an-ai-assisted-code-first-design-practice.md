---
layout: post
title: "Building an AI-assisted, code-first design practice"
date: 2026-07-08 07:00:00 -0700
categories: design-engineering
---

For the last year and a half I've been rebuilding how design happens at SmarterDx. First the design system, then the prototyping workflow, then most of the design team's day-to-day tooling. None of it was a mandate. It started because Figma-as-source-of-truth was quietly breaking down, and I wanted to see what building the design system in code, with Claude Code, would actually look like.

## The problem with Figma as source of truth

Every design system built Figma-first has the same failure mode eventually. The components in Figma drift from the components in code. Someone ships a fix in Storybook and forgets to update the file. Someone updates a token in Figma and it takes two sprints to land in the codebase. You end up maintaining two systems and trusting neither.

We were a small team building a design system from zero for a clinical product. No legacy Figma library to protect, no reason to keep design and code as separate artifacts.

## Component Lab

So I started building the system directly in code. React Aria for behavior, PandaCSS for styling, tokens in DTCG format, all living in `packages/` as the actual source of the design system. No Figma files. I work with Claude Code and our front-end stack directly to design primitives, tokens, and components. What gets published to the design system *is* the real component, not a picture of one.

Component Lab is the presentation layer on top of that. A Vite app where every component gets a showcase page: every variant, every state, every token it consumes, documented and rendered inline using the real component, not a mockup of it. It's where decisions get made and where they stay visible, for anyone who wants to see what exists and why.

This changes what "design review" means. There's no translation step between what a designer approved and what engineering ships, because they're the same artifact.

The one thing that approach gives up is a visual surface non-engineers can browse and comment on, which is most of what Figma is actually used for day to day. So I wrote a package that paints the components back into Figma automatically: primitives, tokens, and semantic architecture, generated 1:1 from what's actually in Storybook. Figma becomes a read view of the real system instead of a second source of truth someone has to keep in sync by hand.

## From design system to prototyping environment

Once the components existed as real, importable code, the obvious next step was prototyping with them directly instead of in Figma. I built a monorepo on top of our front-end microservice that designers can open with Claude Code and start prototyping in, using our actual production components instead of approximations of them.

That took more than just exposing the components. I wrote a set of Claude Code skills and documentation to handle the parts that would normally stop a designer cold: managing the git workflow, scaffolding new prototypes, keeping people out of trouble in a real codebase. The goal was to make picking this up feel closer to opening a new Figma file than onboarding onto an engineering repo.

## What happened after

The design team is now fully off Figma for prototyping. Everything ships as working code in the browser, built with real design system components, from day one. That was the part I set out to do.

What I didn't plan for was the rest of the product org picking it up. It's now used for feature prototypes going into market validation, and for building internal dashboards, including ones tracking the KPIs for this exact initiative. People outside product design are using it for AI-assisted building generally, not just prototyping.

That adoption curve is also why I'm now on our AI Adoption Tiger Team, working cross-functionally on governance and rollout for AI tooling org-wide. A different problem than the one I started with, but it grew directly out of it.

## What's next

Right now I'm exploring an OOUX/ORCA build for the prototype lab, aimed at closing the gaps still there in hand-off: versioning, diffing, annotations and notes, prototype metadata, SSO/auth, external prototype linking. Basically the feature set that would let this fully replace the design-to-engineering hand-off workflows most teams are still stuck with, not just the prototyping step but the whole pipeline around it.
