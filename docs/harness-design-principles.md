# Harness Design Principles for Long-Running AI Agents

Principles and patterns for designing effective multi-agent harnesses, distilled
from Anthropic's and OpenAI's published engineering practices (March 2026).

This document serves two audiences:

- **Humans** designing multi-agent systems and orchestration harnesses
- **AI agents** operating within those systems as builders, reviewers, or planners

Sources:

- [Anthropic — Harness design for long-running application development](https://www.anthropic.com/engineering/harness-design-long-running-apps)
- [OpenAI — Harness engineering: leveraging Codex in an agent-first world](https://openai.com/index/harness-engineering/)

---

## 1. Multi-Agent Architecture

A single agent working alone hits predictable ceilings: it loses coherence on
long tasks, praises its own mediocre work, and under-scopes when given a bare
prompt. Splitting responsibilities across specialized agents addresses each
failure mode directly.

### The Planner / Generator / Evaluator Pattern

| Agent | Role | Why it exists |
|---|---|---|
| **Planner** | Expands a short prompt into a full product spec | Without it, the generator under-scopes and starts building before thinking |
| **Generator** | Implements features one sprint at a time | Does the actual building work, focused on execution |
| **Evaluator** | Tests the running application and grades against criteria | Agents are unreliable judges of their own work; external feedback is essential |

Key dynamics:

- **Sprint contracts.** Before each unit of work, the generator and evaluator
  negotiate what "done" looks like. The generator proposes what it will build
  and how success will be verified; the evaluator reviews the proposal. They
  iterate until they agree. This bridges the gap between high-level specs and
  testable implementation.
- **Planner restraint.** The planner should focus on product context and
  high-level technical design, not granular implementation details. If the
  planner over-specifies and gets something wrong, errors cascade downstream.
  Constrain deliverables, let agents figure out the path.
- **Communication via files.** Agents communicate through files: one writes,
  another reads and responds. This creates an auditable trail and avoids
  depending on shared conversational context.

### Humans Steer, Agents Execute

The human role shifts from writing code to designing environments, specifying
intent, and building feedback loops. When an agent struggles, treat it as a
signal: identify what is missing (tools, guardrails, documentation) and feed it
back into the system. The fix is almost never "try harder."

---

## 2. Context Management

Context is the scarcest resource in long-running agent work. Mismanage it and
the agent either loses coherence, optimizes for the wrong thing, or wraps up
prematurely.

### Context Resets Over Compaction

When the context window fills, there are two strategies:

- **Compaction** summarizes earlier parts of the conversation in place so the
  same agent can continue on a shortened history. This preserves continuity but
  doesn't give the agent a clean slate. "Context anxiety" (the agent wrapping up
  prematurely as it approaches its perceived context limit) can persist.
- **Context resets** clear the window entirely and start a fresh agent with a
  structured handoff artifact carrying the previous agent's state and next
  steps. This eliminates context anxiety at the cost of orchestration
  complexity.

Context resets are generally more effective for long tasks. The handoff artifact
must contain enough state for the next agent to pick up cleanly.

> As models improve at long-context handling, context resets may become less
> necessary. Test whether your current model still needs them.

### AGENTS.md as Table of Contents

Do not put everything into a single giant instruction file. A monolithic manual:

- Crowds out the actual task, code, and relevant docs from context
- Becomes "non-guidance" — when everything is important, nothing is
- Rots instantly and becomes a graveyard of stale rules
- Resists mechanical verification (coverage, freshness, cross-links)

Instead, treat `AGENTS.md` as a short (~100 lines) map with pointers to deeper
sources of truth:

```
AGENTS.md            <- entry point, table of contents only
ARCHITECTURE.md      <- top-level map of domains and package layering
docs/
  design-docs/       <- indexed design documentation
  exec-plans/
    active/          <- current execution plans with progress logs
    completed/       <- finished plans for reference
  product-specs/     <- product requirements
  references/        <- external library/API docs in agent-readable format
```

This enables **progressive disclosure**: agents start with a small, stable entry
point and are taught where to look next, rather than being overwhelmed up front.

---

## 3. Repository as System of Record

**What the agent cannot see does not exist.** From the agent's point of view,
anything not accessible in-context while running is effectively unknown.

### Encode All Knowledge in the Repo

Knowledge that lives in chat threads, Google Docs, meeting notes, or people's
heads is invisible to agents. To make it real:

- Push architectural decisions into versioned markdown
- Capture design rationale as design docs, not just decisions
- Record execution plans with progress and decision logs
- Version known technical debt alongside active plans
- Treat documentation as code: review it, lint it, keep it fresh

That Slack discussion where the team aligned on an architectural pattern? If it
isn't discoverable to the agent, it's illegible in the same way it would be
unknown to a new hire joining three months later.

### Mechanical Freshness Enforcement

Documentation rots. Fight this mechanically:

- Dedicated linters and CI jobs validate that the knowledge base is up to date,
  cross-linked, and structured correctly
- A recurring "doc-gardening" agent scans for stale documentation that no longer
  reflects real code behavior and opens fix-up pull requests
- Verification status is tracked alongside each document

---

## 4. Making Work Legible to Agents

Agent effectiveness depends on what the agent can observe and interact with.
The more of the system you can expose in a form the agent can inspect, validate,
and modify, the more leverage you get.

### Give Agents Access to the Running Application

- **Playwright MCP / Chrome DevTools Protocol.** Let agents navigate, click,
  screenshot, and interact with the live UI. An evaluator that interacts with
  the running app catches bugs that static code review misses.
- **Observability stack.** Expose logs, metrics, and traces to agents via
  queryable APIs (LogQL, PromQL, TraceQL). Prompts like "ensure startup
  completes in under 800ms" become tractable when agents can query the data.
- **Ephemeral environments.** Each agent works on a fully isolated instance of
  the app, including its own logs and metrics, torn down when the task
  completes.

### Favor Agent-Friendly Technologies

- Prefer technologies that are composable, API-stable, and well-represented in
  training data. "Boring" technologies are often easier for agents to model.
- In some cases it's cheaper to reimplement a subset of functionality than to
  work around opaque upstream behavior from external libraries.
- Pull more of the system into a form the agent can inspect directly.

### Optimize for Agent Legibility

The resulting code does not always need to match human stylistic preferences.
As long as the output is correct, maintainable, and legible to future agent
runs, it meets the bar. Structure code for agent understanding first.

---

## 5. Quality Assurance

### Self-Evaluation Is Unreliable

When asked to evaluate their own work, agents reliably skew positive — even when
quality is obviously mediocre to a human observer. This is especially pronounced
for subjective tasks (design, UX), but applies even to tasks with verifiable
outcomes.

**Separate the judge from the builder.** The separation alone doesn't eliminate
leniency, but tuning a standalone evaluator to be skeptical is far more
tractable than making a generator critical of its own work.

### Turn Subjective Quality Into Grading Criteria

"Is this good?" is hard to answer consistently. "Does this follow our principles
for good design?" gives agents something concrete to grade against. Define
criteria that encode design principles and preferences:

| Criterion | What it measures |
|---|---|
| **Design quality** | Does it feel like a coherent whole or a collection of parts? |
| **Originality** | Evidence of deliberate creative choices vs. template defaults and AI-generated patterns? |
| **Craft** | Technical execution: typography, spacing, color harmony, contrast |
| **Functionality** | Can users understand the interface and complete tasks without guessing? |

Weight criteria by where the model is weakest. If craft and functionality come
naturally but design and originality don't, weight the latter more heavily.

### Calibrate the Evaluator

- Use **few-shot examples** with detailed score breakdowns to align the
  evaluator's judgment with your preferences
- Set **hard score thresholds** — if any criterion falls below its threshold,
  the sprint fails and the generator gets specific feedback
- Explicitly **penalize known anti-patterns** (e.g., generic "AI slop" patterns
  like purple gradients over white cards)
- **Tune iteratively**: read evaluator logs, find where its judgment diverges
  from yours, update the prompt

### Enforce Invariants Mechanically

Documentation and prompting set intent. Mechanical enforcement makes it stick:

- **Custom linters** that inject remediation instructions into agent context via
  error messages
- **Structural tests** that validate architectural boundaries (dependency
  directions, layer constraints)
- **Naming conventions, file size limits, logging format** — all enforced
  statically
- **Parse data at the boundary** — validate inputs rather than trusting guessed
  shapes

Enforce boundaries centrally, allow autonomy locally. You care deeply about
correctness and reproducibility. Within those boundaries, agents have freedom in
how solutions are expressed.

---

## 6. Managing Entropy

Agents replicate patterns that already exist in the repository — including bad
ones. Over time, this inevitably leads to drift. Without active intervention,
quality degrades continuously.

### Golden Principles

Encode opinionated, mechanical rules directly into the repository:

- Prefer shared utility packages over hand-rolled helpers to keep invariants
  centralized
- Don't probe data "YOLO-style" — validate boundaries or rely on typed SDKs
- Keep patterns consistent so agents can reliably extend them

### Continuous Garbage Collection

Technical debt is a high-interest loan. Pay it down continuously in small
increments rather than letting it compound:

- Run recurring background tasks that scan for deviations from golden principles
- Update quality grades per domain and architectural layer
- Open targeted refactoring pull requests (most reviewable in under a minute)
- Human taste is captured once, then enforced continuously on every line of code

This functions like garbage collection. Catch and resolve bad patterns daily
rather than letting them spread for weeks.

### Throughput Changes the Merge Philosophy

When agent throughput far exceeds human attention:

- Corrections are cheap; waiting is expensive
- Pull requests should be short-lived
- Test flakes are often better addressed with follow-up runs than blocking
  progress indefinitely
- Minimal blocking merge gates — but only when the system has sufficient
  automated validation to make this safe

---

## 7. Harness Evolution

Every component in a harness encodes an assumption about what the model cannot
do on its own. Those assumptions are worth stress-testing regularly.

### Simplify as Models Improve

- Remove one component at a time and review the impact on output quality
- Don't strip everything at once — it becomes impossible to tell which pieces
  were load-bearing
- When a new model lands, re-examine the harness: strip away what's no longer
  needed, add new pieces to achieve capabilities that weren't possible before

### Principles for Iteration

| Principle | Implication |
|---|---|
| Find the simplest solution; increase complexity only when needed | Don't over-engineer the harness preemptively |
| The evaluator's value depends on task difficulty relative to model capability | Skip it for tasks well within the model's comfort zone; keep it for frontier tasks |
| Sprint decomposition may become unnecessary as models handle longer contexts | Test whether your model still needs it |
| The space of interesting harness combinations doesn't shrink as models improve — it moves | Keep experimenting with novel combinations |

### Read the Traces

Experiment with the model you're building against. Read its traces on realistic
problems. Tune performance to achieve desired outcomes. The logs will tell you
where the agent struggles and where the harness needs to intervene.

---

## 8. Increasing Autonomy

As more of the development loop is encoded into the system (testing, validation,
review, feedback handling, recovery), agents can drive increasingly complete
workflows:

1. Validate the current state of the codebase
2. Reproduce a reported bug
3. Record evidence of the failure
4. Implement a fix
5. Validate the fix by driving the application
6. Record evidence of the resolution
7. Open a pull request
8. Respond to agent and human feedback
9. Detect and remediate build failures
10. Escalate to a human only when judgment is required
11. Merge the change

This level of autonomy depends heavily on the specific tooling and structure
of the repository. It does not generalize without similar investment.

### The Feedback Loop

The core cycle for increasing autonomy:

```
Identify failure mode
    -> Ask "what capability is missing?"
    -> Make it legible and enforceable for the agent
    -> Encode it into the repository (tools, docs, lints, tests)
    -> Agent can now handle that case
    -> Move to the next failure mode
```

Human engineers always step into failures and ask: "what capability is missing,
and how do we make it both legible and enforceable for the agent?"

---

## 9. Quick Reference Checklist

### Architecture
- [ ] Split responsibilities: planner, generator, evaluator
- [ ] Planner focuses on product context, not implementation details
- [ ] Generator and evaluator negotiate sprint contracts before building
- [ ] Agents communicate through files, not shared conversational context

### Context
- [ ] AGENTS.md is a short map (~100 lines), not an encyclopedia
- [ ] Knowledge base lives in structured `docs/` with indexes
- [ ] Use context resets with structured handoffs for long tasks
- [ ] Validate docs mechanically (linters, CI, doc-gardening agents)

### Legibility
- [ ] Agents can interact with the running application (Playwright, DevTools)
- [ ] Observability data is queryable by agents (logs, metrics, traces)
- [ ] Ephemeral environments per agent task
- [ ] All knowledge is in-repo and versioned

### Quality
- [ ] Evaluator is separate from generator
- [ ] Grading criteria are concrete and weighted by model weakness
- [ ] Evaluator is calibrated with few-shot examples and hard thresholds
- [ ] Invariants are enforced by linters and structural tests, not just docs

### Entropy
- [ ] Golden principles are encoded in the repo
- [ ] Recurring cleanup agents scan for deviations
- [ ] Quality grades are tracked per domain
- [ ] Tech debt is paid continuously, not in bursts

### Evolution
- [ ] Harness components are stress-tested for necessity on each model upgrade
- [ ] Components are removed one at a time, not all at once
- [ ] Agent traces are read and analyzed regularly
- [ ] Simplest effective harness is the goal

---

*Last updated: 2026-03-30*
