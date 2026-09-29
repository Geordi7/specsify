# Specsify

A lightweight convention for organizing project knowledge into conveniently sized chunks so that AI agents can collect the information they need without bloating their context windows, allowing agents to operate consistently, apply correct judgment, and produce consistent and coherent output.

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

**2. Tell your agent about the specs system.**

Tell your agent to read `specs/00-meta.md` and to inform its agent directive file (`AGENTS.md` or `CLAUDE.md`).

Then tell it what you want to build. I have tested this extensively with Claude-Code Sonnet and Opus, the agent consistently maintains the specifications and references them prevent specification drift and implementation sloppiness. It also massively accelerates accuracy on fresh sessions (they typically feel as on-point as long running contexts).

The agent usually forgets to commit on every change, but after prompting it to do so the first time it 'twigs' and it is then consistent.

If you want to manually inform `AGENTS.md` you can add this:

```markdown
## Specs

This project uses the specsify convention. `specs/00-meta.md` describes how to operate
in a specs-driven project. Its key directives:

- Run `ls specs` for a domain overview before starting work
- Follow the change process: scope → review specs → update specs → change code → verify → commit
- Commit messages are labels only (one sentence). Rationale goes in the spec changes.
- Write tests first and see them fail for the intended reason before implementing
- Never modify `00-meta.md` without explicit instruction
```

## What agents do with this

An agent operating in a specsify project will:

1. Run `ls specs` to understand the domain landscape
2. Read the relevant specs before planning changes
3. Update specs *before or alongside* code changes (requirements first)
4. Commit specs and code together — the commit message is a label, not a rationale
5. Write each test first and watch it fail before writing the code that passes it

The result is a project where the specs stay current, every commit is traceable to intent, and any agent (or human) picking up the work can get oriented quickly.

## What this repo is

This repo contains `specs/00-meta.md` which contains everything  — the single file you copy. The `01-requirements.md` stub is illustrative, but you should copy that one too.

There is no tooling: I could make a claude skill but honestly it's just copying two files into your project and then telling your agent to adopt this methodology.
