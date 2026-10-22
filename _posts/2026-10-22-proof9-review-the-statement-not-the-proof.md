---
layout: post
title: "PROOF9: Review the Statement, Not the Proof"
date: 2026-10-22 15:00:00 -0700
categories: [Development, AI-Assisted Coding]
tags: [ai, development, code-review, testing, open-source, claude, codeframe]
description: "Coding agents write more code than anyone can read. PROOF9 moves code review from diffs to proof obligations: every bug becomes a scoped requirement that a tool re-verifies on every change."
toc: true
---

When a coding agent says "done," what do you actually know?

You know the agent believes it's done. You may know the tests it chose to run passed. You don't know whether the bug you fixed last month is still fixed, whether the change touched something three directories away that used to work, or whether "all tests pass" means "nothing ran."

For a human-paced team, code review covered that gap. Someone read the diff, held the system in their head, and said "this looks right." That doesn't scale when an agent can open twenty PRs before lunch. Reading every line of agent output is the new bottleneck, and skimming it is how regressions ship.

PROOF9™ is my answer to that problem. It's the verification layer in [CodeFRAME®](https://github.com/frankbria/codeframe), and it now also runs as a Claude Code plugin.

## What PROOF9 is

PROOF9 is a **ledger of requirements, each carrying proof obligations, verified by tools across nine evidence gates.**

The loop is simple:

1. Something breaks: in production, in QA, while dogfooding, or because an agent "fixed" something it shouldn't have.
2. You capture it: `cf proof capture` (or `/proof <what went wrong>` inside Claude Code). That creates a requirement (`REQ-0001`) with a scope (the files, routes, APIs or components it concerns) and one or more obligations, plus draft test stubs.
3. From then on, every change that touches that scope must produce evidence that the obligations still hold. The requirement can't be silently dropped. It can be waived, but the waiver records who, why and until when.

The nine gates are the kinds of evidence a requirement can demand:

| Gate | What it proves |
|---|---|
| UNIT | Logic is correct |
| CONTRACT | API and integration contracts hold |
| E2E | User journeys work end to end |
| VISUAL | The UI renders correctly |
| A11Y | It's accessible |
| PERF | It's within its performance budget |
| SEC | No known vulnerability class is reintroduced |
| DEMO | The feature demonstrably works |
| MANUAL | A human verified it (closed only by a tracked, expiring waiver) |

A few properties do most of the work, and each one exists because I watched something go wrong without it:

- **Verification is a tool's exit code, not a model's opinion.** Each obligation names its tests, and each named test runs in its own subprocess. A test that doesn't exist fails the obligation instead of passing quietly.
- **A run that verified nothing is not a pass.** If no gate ran, PROOF9 says "verified nothing" and `cf proof run` exits non-zero. An obligation with nothing runnable is recorded as *unverifiable*, never as green.
- **Evidence is checksummed and bound to the run and gate that produced it.** You can't transplant a passing artifact from one run into another.
- **Obligations are scoped.** A requirement is re-checked only when a change intersects its scope, so the ledger can grow to hundreds of requirements without every commit running everything.

The short version: **every bug you fix becomes a permanent, machine-checked requirement.** Quality compounds instead of resetting with every release.

## Why we need it

Traditional quality processes are amnesiac. We test what someone remembered to write a test for. If a bug slips through and gets fixed, the fix might come with a test, or it might not. If the test exists, nothing stops a later change, from a person or an agent, from deleting it, skipping it, or "updating" it until it passes.

Agents make that worse in a specific way. They optimize for the stopping condition you gave them. If the stopping condition is "tests pass," then an agent under pressure will find the cheapest path to green, and sometimes that path runs through the test rather than the code. If the stopping condition is "the reviewer approves," it will produce diffs that *look* approvable.

So the stopping condition has to be something the agent can't argue with: **these requirements, scoped to this change, have evidence.**

## How is this different from acceptance criteria?

Acceptance criteria and PROOF9 requirements look similar on the page. Both say "the system must do X." They differ in four ways:

1. **Lifetime.** Acceptance criteria live on a story. They're checked once, when the story is accepted, and then the ticket is closed and the criteria are effectively gone. A PROOF9 requirement lives in the repository and is re-checked forever, on every change that touches its scope.
2. **Who checks.** Acceptance criteria are usually verified by a person reading a demo or a PR. PROOF9 obligations are verified by a test runner's exit code.
3. **Direction.** Acceptance criteria are forward-looking: what the new feature should do. Most PROOF9 requirements are backward-looking: what has already broken and must not break again. (You can also lock in what works today with `cf proof ratchet`, which turns every currently passing test into a requirement.)
4. **Scope.** Acceptance criteria are attached to a unit of *work*. PROOF9 requirements are attached to a region of the *system*, which is what lets the right ones fire when an unrelated-looking change wanders into that region.

A good acceptance criterion is a natural source of a PROOF9 requirement. The difference is that the requirement outlives the ticket.

## How is it different from TDD?

TDD is a practice for *writing* code: red, green, refactor. It's local to the moment of development and to the developer doing it. It produces tests as a side effect.

PROOF9 is a ledger that governs *which* evidence must exist and run, independent of who wrote the code or how. You can do TDD inside PROOF9, and the capture flow nudges you toward it: the draft tests it writes are meant to be filled in so they fail on the bug and pass on the fix. But the ledger doesn't care about the process, only the evidence.

The distinction that matters most is this. In TDD, the test file *is* the commitment, so editing the test edits the commitment. In PROOF9, the commitment sits in the ledger above the test. The obligation names the test it expects. Delete the test and the obligation fails. Weaken it and you're changing code the requirement depends on, which is exactly what a review should look at (more on that below).

TDD makes the *developer* disciplined. PROOF9 makes the *repository* disciplined, which matters a lot more when most of the developers are agents.

## What's a proof obligation?

The term comes from formal methods. In the B method, Event-B, SPARK, Dafny or Frama-C, a *proof obligation* (also called a verification condition) is a logical statement that must be discharged before the program is accepted as correct. "The loop invariant holds on entry." "This index is always in bounds." The tool generates the obligations from the specification and the code, and a prover or a person must discharge each one.

PROOF9 borrows the shape and weakens the logic. A PROOF9 obligation is a claim that must be discharged by *evidence from a named gate*: "REQ-0042 is satisfied when `test_sec_req_0042_*` passes and bandit runs clean." That's empirical, not deductive. A passing test is evidence, not a proof. But the structure is the same: a requirement generates obligations, obligations must be discharged, and undischarged obligations block acceptance.

## Does it map onto something engineers use today?

Yes, and more closely than the name suggests. If you've worked in systems engineering or any regulated industry, you already know this pattern:

- **Requirements traceability (the V-model).** Every requirement traces down to a verification activity, and every verification activity traces up to a requirement. DO-178C in avionics and ISO 26262 in automotive are built on this. PROOF9 is a lightweight traceability matrix that a CI-speed tool keeps current.
- **Compliance controls.** A SOC 2 control is a requirement with evidence attached and an auditor who checks the evidence, not the people. Waivers with owners and expiry dates are exactly how exceptions work in a mature control environment.
- **Required status checks and CODEOWNERS.** Branch protection already says "this check must pass to merge." PROOF9 makes the set of required checks grow automatically from your defect history and scopes them to the change.
- **Regression suites, error budgets and contract tests.** Each is a piece of the idea: regression tests remember defects, SLOs make a promise checkable, and contract tests (Pact and similar) bind a requirement to an interface boundary.

What's missing in most software teams is the connection: a defect automatically becomes a scoped, tool-verified, attributable obligation, and merging is gated on it.

## How code review and merging change

This is the part I find most interesting.

Mathematicians who use Lean have already worked out how to trust a proof they didn't read. You don't review the 4,000 lines of tactic script. The kernel checks those. You review the **statement**: did the formal theorem actually say what we meant? All the human trust concentrates in a few lines of specification, and the rest is mechanical.

PROOF9 moves code review in the same direction. When the gate verifies that every in-scope obligation is discharged, the reviewer's job shifts from "is every line of this diff correct?" to a smaller set of higher-value questions:

1. **Are the obligations right?** Does each requirement actually capture the defect or intent, and do its tests fail on the bug and pass on the fix? This is spec review, and it's where human judgment matters most.
2. **Is the evidence real?** Did gates actually run, or did the run verify nothing? Did this PR touch a test that a requirement depends on? That's the moment to slow down.
3. **Are the waivers justified?** Every waiver has a reason, an owner and, ideally, an expiry. Reviewing waivers is reviewing the debt you're choosing to carry.
4. **Is the scope right?** A requirement scoped too narrowly won't fire when it should. One scoped too broadly slows everything down.
5. **Does the design still make sense?** Architecture, naming and maintainability still benefit from human eyes. But that becomes a sampling activity, not a gate on every line.

Merging changes with it. In CodeFRAME, `cf pr merge` is blocked while any requirement in the PR's scope is open and not waived. Every PR carries a proof report showing which obligations passed, which were waived, and what evidence was produced. A human can override, but the override records the actor and the reason. The merge decision becomes "the obligations in scope are discharged," not "someone clicked approve after scrolling."

One consequence follows directly: **the gate itself has to be protected.** If an agent can edit the verifier, it can certify its own work. Put the PROOF9 code and the merge path in CODEOWNERS, so changes there always need a human review, even when everything else merges on green.

## What PROOF9 doesn't do

It's worth being plain about the limits:

- A requirement is only as good as its tests. A weak test discharges an obligation it shouldn't. That's why reviewing obligations matters more than reviewing diffs.
- Several gates (E2E, VISUAL, A11Y, PERF, DEMO) depend on you having tests of those kinds to name. Without them, the obligation is honestly reported as unverifiable, which is better than a false pass but is not evidence.
- It doesn't replace your CI. It produces evidence that CI and a merge gate can consume.

## Try it

In a Claude Code session:

```
/plugin install proof9 --marketplace frankbria/codeframe
```

It needs the CLI (`uv tool install codeframe-ai`, version 0.9.7 or later). Then `/proof <what went wrong>` captures your first requirement, `/proof ratchet` locks in what passes today, and every commit Claude makes is checked against the ledger first: a warning by default, or a block if you set `mode` to `block`.

The rest is on [GitHub](https://github.com/frankbria/codeframe).

The agent says it fixed the bug. The gate says whether it did. Believe the gate.

---

<small>CodeFRAME® is a registered trademark and PROOF9™ is a trademark of Noatak Enterprises, LLC, dba Bria Strategy Group.</small>
