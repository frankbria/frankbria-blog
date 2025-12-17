---
layout: default
title: "Noodling Your Way Out of Vibe 'Spaghetti Code'"
description: "How to Push Through the Rough Middle of AI-Assisted Development"
---


# Noodling Your Way Out of Vibe "Spaghetti Code"

**How to Push Through the Rough Middle of AI-Assisted Development**

---

You've done the exciting part. You wrote specs. You vibe-coded your way through the initial build. You've got APIs, a web interface, test coverage that looks impressive. The architecture diagram on your wall makes you feel like a real engineer.

And yet... nothing actually works.

Welcome to the **rough middle**—the phase of AI-assisted development where your codebase has become a tangled mess of half-implemented features, stubbed functions, mocks that were never replaced, and APIs that return hardcoded data. Your tests pass because they're testing the mocks. Your UI looks great because it's displaying placeholder content.

This is where most AI-assisted projects go to die. Not because the developer lacked skill or vision, but because they got lost in the complexity they created.

I've been there. Multiple times. Here's the systematic process I use to noodle my way out of the spaghetti.

---

## Why the Rough Middle Exists

Before diving into the solution, it's worth understanding *why* this happens so consistently with AI-assisted development.

When you're vibe coding with an agent, you're optimizing for momentum. The agent is optimizing for giving you working code *right now*. Neither of you is optimizing for completeness. The agent will:

- Stub out complex functions with `// TODO: implement later`
- Create mocks to make tests pass
- Return hardcoded data to make the UI look functional
- Skip edge cases to keep moving
- Implement the happy path and ignore error handling

None of this is malicious. It's rational behavior for maintaining flow. But after dozens of sessions, you've accumulated hundreds of these small shortcuts into a codebase that *looks* complete but *is* hollow.

The core problem: **You've lost visibility into what's real and what's theater.**

---

## The Solution: Systematic Gap Discovery and Elimination

The process I'm about to describe takes a chaotic codebase and transforms it into a structured backlog of developer-ready issues. By the end, you'll have complete visibility into what needs to be done, and a repeatable system for getting it done.

### Step 1: Set Up Your External Review Pipeline

Sign up for two services:

1. **[Traycer AI](https://traycer.ai)** — Automatically generates implementation plans when you create GitHub issues
2. **[CodeRabbit](https://coderabbit.ai)** — AI-powered code review on every PR

Connect both to your GitHub repository. Yes, the repo needs to be public for the free/base tiers. Don't worry—nobody is looking at your repo unless you're lucky enough to have built something people care about.

**Why these two specifically?**

Traycer solves the "what do I actually need to do?" problem by converting vague issues into concrete implementation steps. CodeRabbit solves the "what did I miss?" problem by catching logic bugs your coding agent will happily ignore.

The base plans together run under $60/month. Even one month is enough to push through the rough middle on most projects.

### Step 2: Get Fresh Eyes on the Codebase

Your main coding agent has context pollution. It's been with you through every shortcut, every "we'll fix this later," every mock. It's complicit.

Open the codebase with a **different** coding agent. I use Claude Code for my primary development, so I'll open the project in something like Mistral's Le Chat or Cursor with a different model. Even if it's using the same underlying model (many use Anthropic's Claude on the backend), the different system prompt and fresh context creates genuinely different analysis.

The goal isn't to switch agents permanently. It's to get analysis from something that hasn't been marinating in your accumulated technical debt.

### Step 3: Generate the User Documentation

Before finding gaps, you need to establish what *should* exist. Ask your fresh agent to document the application from the user's perspective.

**Prompt:**

```
Analyze this entire codebase and create comprehensive user documentation.

Write this as if you're creating an official user manual for someone who just downloaded the application. Document:

1. What the application does (the core value proposition)
2. How to get started (onboarding flow)
3. Every feature the user can access
4. How each feature works, step by step
5. What inputs are required and what outputs to expect
6. Any configuration options or settings
7. Common workflows and use cases

Be thorough. If you see UI components, document what they do. If you see API endpoints, document what a user would accomplish by triggering them. If you see database schemas, infer what data the user is managing.

Write this for a real user, not a developer. Focus on outcomes and workflows, not implementation details.
```

Save this document. It becomes your north star—the idealized version of what your application should do.

### Step 4: Generate the Gap Analysis

Now ask the same agent to be ruthlessly critical.

**Prompt:**

```
Now analyze this codebase for gaps, shortcomings, and incomplete implementations. 

I need you to identify everything that's preventing this application from being functional, successful, and genuinely useful to users. Examine from three perspectives:

**Technical Perspective:**
- Functionality that claims to exist but isn't actually implemented
- Mocked data or stubbed functions that were never completed
- API endpoints that return hardcoded responses
- Tests that pass because they're testing mocks, not real behavior
- Error handling that's missing or incomplete
- Database operations that aren't actually persisting data
- Authentication/authorization that's bypassed or incomplete
- Missing integrations that the UI suggests should exist

**Business Perspective:**
- Features that would differentiate this product but don't work
- User workflows that dead-end or break
- Missing functionality that competitors would have
- Monetization or conversion paths that aren't implemented
- Analytics or tracking that's referenced but not connected

**User Perspective:**
- UI elements that don't do anything when clicked
- Forms that don't submit or don't save
- Features promised in the UI that don't function
- Confusing flows where the user would get stuck
- Missing feedback (loading states, success messages, error handling)
- Accessibility issues that would prevent usage

Reference the user documentation we just created. For each gap, explain:
1. What should exist (based on the user documentation)
2. What actually exists (or doesn't) in the code
3. Why this gap matters
4. What's blocking the fix (if there are dependencies)

Be exhaustive. I'd rather have 50 gaps documented than miss something critical.
```

This document is your map out of the spaghetti.

### Step 5: Convert Gaps to Developer-Ready GitHub Issues

Now return to your primary coding agent (or stay with the fresh one—doesn't matter). You're going to transform that gap document into actionable GitHub issues.

**Prompt:**

```
I have two documents:
1. [User documentation - reference or paste]
2. [Gap analysis - reference or paste]

Your task: Create GitHub issues for every gap identified.

Critical requirements for each issue:

1. **Scope for a single developer** — Each issue should be something one developer could complete in a focused work session. If a gap requires multiple distinct changes, split it into multiple issues.

2. **Frame as developer instructions** — Write the issue so a developer (human or AI) could pick it up and implement it without additional context. Include:
   - Clear description of what's broken or missing
   - Expected behavior (reference the user documentation)
   - Current behavior
   - Specific files or components likely involved
   - Acceptance criteria (how to know it's done)

3. **Include technical guidance** — Mention relevant patterns already in the codebase, similar implementations to reference, or architectural considerations.

4. **Prioritize by dependency** — Note if an issue blocks other issues. These should be tackled first.

Success criteria: When a developer completes ALL these issues, the application should be fully functional, deliver real value to users, and be ready for production use. Nothing should be mocked, stubbed, or incomplete.

Output each issue in this format:

---
**Title:** [Clear, specific title]

**Description:**
[Full issue description following the requirements above]

**Labels:** [bug/feature/enhancement, priority, area of codebase]

**Blocked by:** [Other issue titles if applicable]
---
```

Create all these issues in GitHub. Don't try to prioritize or filter at this stage—create them all. You'll work through them systematically.

### Step 6: Let Traycer Plan Each Issue

Once issues are created, Traycer AI automatically kicks off planning for each one. You'll see implementation plans appear in the issue comments.

**Rate Limit Note:** If you've created many issues, you may hit Traycer's rate limit. When this happens:
1. Wait for the timeout to expire
2. Assign the issue to yourself
3. Traycer will resume planning

Each plan includes an "AI Instructions" section—a prompt optimized for feeding to a coding agent. This is gold.

### Step 7: Work Through Issues Systematically

For each issue:

1. Open your coding agent in the repository
2. Copy the AI Instructions from Traycer's plan
3. Add context about branching:

**Prompt wrapper:**

```
[Paste Traycer's AI Instructions here]

Implementation requirements:
- Create a feature branch named `fix/[issue-number]-[brief-description]`
- Make atomic commits with clear messages
- Write or update tests to verify the fix
- When complete, prepare a pull request with:
  - Summary of changes
  - How to test
  - Reference to the issue number (Fixes #XX)
```

4. Let your agent implement the fix
5. Create the PR

### Step 8: Code Review with CodeRabbit

When you open the PR, CodeRabbit automatically reviews it. This catches:

- Logic bugs your agent introduced while fixing other bugs
- Edge cases that weren't handled
- Inconsistencies with the rest of the codebase
- Security issues
- Performance concerns

CodeRabbit comments directly on the PR with specific issues and suggested fixes.

**Rate Limit Note:** You may hit CodeRabbit's review limit. To re-trigger a review after the timeout:

```
@coderabbitai review
```

### Step 9: Fix CodeRabbit Findings

For each CodeRabbit comment that needs addressing:

1. Look for the "AI Instructions" in the comment (if available)
2. If not present, copy the full comment text

**Prompt for fixes:**

```
CodeRabbit found the following issue in my PR:

[Paste the CodeRabbit comment]

Please fix this issue. Maintain consistency with the existing implementation and ensure any tests still pass.
```

**Pro tip:** If you have many findings to address, pause CodeRabbit while you work:

```
@coderabbitai pause
```

Fix all the issues, commit them, then resume:

```
@coderabbitai resume
```

This prevents re-review on every commit and saves your rate limit.

### Step 10: Iterate Until Done

Repeat steps 7-9 for each issue. The beautiful thing about this process:

- Every issue is already scoped and planned
- Every PR gets independent review
- You're making measurable progress through a finite list
- The gap analysis ensured nothing is forgotten

When all issues are closed, deploy and do a full manual test. You'll likely find some integration issues between features that were fixed independently. Create new issues for these, run them through the same process.

---

## Optimizing the Process: My Custom Tooling

I've built some personal tooling to make this workflow even more efficient. I won't go into implementation details here, but these are worth mentioning:

### `/chain` Command

I have a custom Claude Code command called `/chain` that takes a complex prompt and automatically parallelizes it across multiple agents and skills. It scans my available agents, subagents, skills, and commands from a local database and assigns work based on context efficiency.

This is especially useful for the gap analysis phase—I can have multiple specialized agents examine different aspects of the codebase simultaneously.

[Link: How I built /chain →](#)

### `reviewing-code` Skill

Before any significant commit—especially PRs—I run my `reviewing-code` skill. It's a systematic code review that catches both obvious issues and subtle bugs that survive normal testing.

Think of it as a pre-flight check before sending code to external review.

[Link: The reviewing-code skill →](#)

### Optimized CLAUDE.md

My `CLAUDE.md` file contains extensive instructions for parallelization, testing strategies, and when to invoke specific skill files. This ensures consistent quality across sessions without having to re-explain preferences every time.

[Link: CLAUDE.md optimization guide →](#)

---

## Why This Works

The core insight: **AI agents are excellent at focused, well-scoped tasks and terrible at managing complex, ambiguous state.**

When your codebase is in the rough middle, you're asking your agent to simultaneously:
- Remember what's real vs. mocked
- Track dependencies between incomplete features
- Prioritize across dozens of issues
- Maintain quality while moving fast

That's too much. The agent will thrash, forget, and create new problems while solving old ones.

This process works because it:

1. **Externalizes state** — The gap document and GitHub issues become your source of truth, not the agent's context window
2. **Scopes aggressively** — Each issue is one developer, one task, clear acceptance criteria
3. **Adds independent review** — Traycer and CodeRabbit catch what your agent misses
4. **Creates momentum** — Closing issues feels good and provides measurable progress
5. **Uses fresh perspective** — The different agent for analysis breaks the context pollution

---

## The Meta-Lesson

The rough middle isn't just a technical problem—it's a project management problem. AI-assisted development creates code faster than humans can maintain mental models of it.

The solution isn't to slow down or to become a better programmer. The solution is to externalize your mental model into documents, issues, and automated review pipelines.

Your coding agent is a powerful collaborator. But it needs guardrails, structure, and fresh perspectives—just like any team member would.

Build the system. Trust the process. Noodle your way out.

---

*Have questions about this process? Found a better approach? [Let me know on X](#) or [LinkedIn](#).*

---

**About the Author**

Frank is a Principal Product Consultant specializing in data systems and governance, with 15+ years in fintech. He hosts [The 6 to 7 Figures Show](#) podcast and is the author of "Seven Billion Banks." When not untangling spaghetti code, he composes orchestral music and manages an overcomplicated home lab.
