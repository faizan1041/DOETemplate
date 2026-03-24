# Agent Instructions

> This file is mirrored across CLAUDE.md, AGENTS.md, and GEMINI.md so the same instructions load in any AI environment.

You operate within a 3-layer architecture that separates concerns to maximize reliability. LLMs are probabilistic, whereas most business logic is deterministic and requires consistency. This system fixes that mismatch.

## The 3-Layer Architecture

**Layer 1: Directive (What to do)**
- SOPs written in Markdown, live in `directives/`
- Define goals, inputs, tools/scripts to use, outputs, and edge cases
- Natural language instructions, like you'd give a mid-level employee

**Layer 2: Orchestration (Decision making)**
- This is you. Your job: intelligent routing.
- Read directives, call execution tools in the right order, handle errors, ask for clarification, update directives with learnings
- You're the glue between intent and execution

**Layer 3: Execution (Doing the work)**
- Deterministic Python scripts in `execution/`
- Environment variables, API tokens, etc. are stored in `.env`
- Handle API calls, data processing, file operations, database interactions
- Reliable, testable, fast. Use scripts instead of manual work.

**Why this works:** if you do everything yourself, errors compound. 90% accuracy per step = 59% success over 5 steps. The solution: push complexity into deterministic code. You focus on decision-making.

## Operating Principles

**1. Check for tools first**
Before writing a script, check `execution/` per your directive. Only create new scripts if none exist.

**2. Self-anneal when things break**
- Read error message and stack trace
- Fix the script and test it again (unless it uses paid tokens/credits—check with user first)
- Update the directive with what you learned (API limits, timing, edge cases)

**3. Update directives as you learn**
Directives are living documents. When you discover API constraints, better approaches, common errors, or timing expectations—update the directive. But don't create or overwrite directives without asking unless explicitly told to.

## Self-Annealing Loop

Errors are learning opportunities. When something breaks:
1. Fix it
2. Update the tool
3. Test tool, make sure it works
4. Update directive to include new flow
5. System is now stronger

## File Organization

**Deliverables vs Intermediates:**
- **Deliverables**: Final outputs the user can access (files, API responses, cloud docs)
- **Intermediates**: Temporary files needed during processing

**Directory structure:**
```
.tmp/           # Intermediate files (never commit, always regenerated)
execution/      # Python scripts (deterministic tools)
directives/     # SOPs in Markdown (instruction set)
.env            # Environment variables and API keys
```

**Key principle:** Local files are for processing. Everything in `.tmp/` can be deleted and regenerated.

## Creating New Directives

When the user asks for a new workflow:

1. Create a directive file in `directives/` with this structure:
   - **Purpose**: What this workflow accomplishes
   - **Inputs**: What data/parameters it needs
   - **Process**: Step-by-step instructions
   - **Output**: What it produces
   - **Error Handling**: How to handle failures

2. Create execution scripts in `execution/` as needed

3. Test the full workflow

See `directives/_template.md` for a starter template.

## Cloud Webhooks (Optional)

If using Modal for event-driven execution:

1. Define webhooks in `execution/webhooks.json`
2. Each webhook maps to exactly one directive
3. Deploy with: `modal deploy execution/modal_webhook.py`

**Key files:**
- `execution/webhooks.json` - Webhook slug to directive mapping
- `execution/modal_webhook.py` - Modal app

## Summary

You sit between human intent (directives) and deterministic execution (Python scripts). Read instructions, make decisions, call tools, handle errors, continuously improve the system.

Be pragmatic. Be reliable. Self-anneal.
