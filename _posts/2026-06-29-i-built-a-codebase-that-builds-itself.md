---
layout: post
title: "I Built a Codebase That Builds Itself"
date: 2026-06-29 17:00:00 -0400
categories: [Development, AI-Assisted Coding]
tags: [ai, development, automation, open-source, claude, cli]
description: "code-evolve is a CLI that drops into any repo and builds your project autonomously, session after session. Here's what it is and why I built it."
toc: true
---

A while back I wrote about the [rough middle of AI-assisted development]({% post_url 2025-12-17-noodling-out-of-spaghetti-code %})—the phase where your codebase looks complete but is hollow, full of stubs and mocks and happy-path theater. That post was about cleaning up after the agent.

This one is about a different itch: what if the agent didn't wait for me at all?

Most AI coding tools are reactive. You prompt, they respond, you prompt again. The momentum lives in your hands, which means the moment you step away, everything stops. I wanted something that kept going—that read what I wanted, looked at what already existed, decided what to do next, did it, and checked its own work. So I built it. It's called **code-evolve**, it's on npm, and it's open source.

```bash
npx code-evolve init
```

## What it actually does

You give it two things: a **vision** (the what and why) and a **spec** (a prioritized checklist of features). Then it runs a loop:

1. Read the vision, spec, and its own journal of past sessions.
2. Assess what exists versus what's specified.
3. Pick the next thing—fix a broken build first, then bootstrap, then the next feature, then bugs.
4. Implement it, run the build and tests, verify it actually works.
5. Write an honest journal entry about what it learned.
6. Commit, and tag the session.

Then it does it again. On a schedule. Every few hours if you want, locally via cron or in GitHub Actions.

The journal is the part I'm most attached to. It's the agent's memory across sessions—append-only, so it can't quietly bury a failure and pretend the session went fine. It reads its own history to avoid repeating mistakes. Over a couple of weeks, the journal becomes a surprisingly honest diary of a project building itself.

## The guardrails matter more than the magic

Anyone can wire an agent into a `while` loop. The hard part is making it safe to walk away from. A few decisions I won't compromise on:

- **Build-or-revert.** Every change has to pass build and tests. If it can't fix a failure in three tries, the whole session reverts to where it started. A bad night can't corrupt your repo.
- **Protected files.** The orchestrator, the agent's identity, and the workflows can't be edited by the agent. It can't rewrite its own constitution.
- **Untrusted input.** It reads GitHub issues for feature requests, but issue content is treated as hostile—boundary markers, comment stripping, truncation. It analyzes intent and writes its own code; it never runs code from an issue.

It detects your stack on its own, too—TypeScript, Python, Rust, Go, JVM, .NET, Ruby, PHP, Deno, monorepos—and figures out how to build, test, and lint without being told. Drop it into a brand-new `git init` or a mature ten-year-old repo; both work.

## The honest part

This is AI-generated code, and I'm not going to pretend otherwise. The right mental model is a tireless junior developer who opens a pull request every few hours: assume good intent, verify thoroughly. Before anything it builds goes to production, read the diff, run your own security review, and check the dependencies it pulled in. The tool is an accelerator, not a replacement for judgment. The README says this in plainer words, and I mean it.

It runs on Claude Code by default—that's the path I've exercised the most—with support for Codex, OpenCode, and Ollama if you'd rather use those.

## Try it

```bash
npx code-evolve init      # scaffold .evolve/
code-evolve setup         # guided: agent, vision, spec, schedule
```

- npm: [code-evolve](https://www.npmjs.com/package/code-evolve)
- source: [github.com/frankbria/code-evolve](https://github.com/frankbria/code-evolve)

It's `0.2.0`—early, but real. If you point it at something and it does something delightful or something dumb, I want to hear about it. The journal will have already written down which one it was.
