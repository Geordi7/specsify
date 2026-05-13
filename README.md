# Specsify

A lightweight convention for organizing project knowledge into conveniently sized chunks so that AI agents can collect the information they need without bloating their context windows, allowing agents to operate consistently, apply correct judgment, and produce consistent and coherent output and 

## The idea

Projects accumulate decisions: what requirements drive the design, what architectural choices have been made, what standards apply. Without a home, those decisions live scattered across conversations, commit messages, and people's heads — invisible to agents, and redundant to explain each time.

Specsify gives them a home: a `specs/` directory of numbered markdown files that an agent can traverse, understand, and update as part of its normal change process.

## Adopting it

**1. Copy `specs/00-meta.md` into your project.**

That file is the entire framework. Everything else follows from it.

```
your-project/
└── specs/                  ← copy from this repo
    ├── 00-meta.md
    └── 01-requirements.md  ← fill this in a bit if you want
```

**2. Write your `specs/01-requirements.md`.**

This is the starting point for any project: what are the primary drivers? What is this thing for, and what must it do? Keep it under 150 lines; add more spec domains as the project grows.

**3. Tell your agent about the specs system.**

Tell your agent to read `specs/00-meta.md` and to inform its agent directive file (`AGENTS.md` or `CLAUDE.md`).

Or add this manually:

```markdown
## Specs

This project uses the specsify convention. `specs/00-meta.md` describes how to operate
in a specs-driven project. Its key directives:

- Run `ls specs` for a domain overview before starting work
- Follow the change process: scope → review specs → update specs → change code → verify → commit
- Commit messages are labels only (one sentence). Rationale goes in the spec changes.
- Record QA observations as block quotes directly in the relevant spec file
- Resolve block-quote observations before marking work complete
- Never modify `00-meta.md` without explicit instruction
```

## What agents do with this

An agent operating in a specsify project will:

1. Run `ls specs` to understand the domain landscape
2. Read the relevant specs before planning changes
3. Update specs *before or alongside* code changes (requirements first)
4. Commit specs and code together — the commit message is a label, not a rationale
5. Record QA findings as block-quote observations in the spec, not in separate documents
6. Resolve observations before considering a change complete

The result is a project where the specs stay current, every commit is traceable to intent, and any agent (or human) picking up the work can get oriented quickly.

## What this repo is

This repo contains `specs/00-meta.md` which contains everything  — the single file you copy. The `01-requirements.md` stub is illustrative, but you should copy that one too.

There is no tooling: I could make a claude skill but honestly it's just copying two files into your project and then telling your agent to adopt this methodology.
