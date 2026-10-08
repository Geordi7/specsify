# Specsify

A lightweight convention for organizing project knowledge into conveniently sized chunks so that AI agents can collect the information they need without bloating their context windows, allowing agents to operate more reliably, apply correct judgment, and produce consistent and coherent output.

## The idea

Projects accumulate decisions: what requirements drive the design, what architectural choices have been made, what standards apply. Without a home, those decisions live scattered across conversations, commit messages, and people's heads — invisible to agents, and redundant to explain each time.

Specsify gives them a home: a `specs/` directory of numbered markdown files that an agent can traverse, understand, and update as part of its normal change process.

Specs state what the project is and does, positively: "buttons are red". Threads explain how the specs got that way, including what was ruled out: "buttons will not be blue". Specs alone drive the code; threads are the trail of reasoning for when a spec needs to be reconsidered.

You don't edit specs yourself: the agent maintains them, and you (the *operator*, in `00-meta.md`) steer through chat and threads. When an agent hits a question only you can answer, it doesn't ask in chat, where the answer gets buried and lost when the session ends. It opens a small file in `specs/threads/`, one question per file. You answer in the file, whenever suits you. Once the decision is applied, the thread is committed with the change, so the reasoning and the rejected alternatives stay next to the specs they shaped.

## Adopting it

**1. Copy `specs/**/*` into your project.**

That directory is the entire framework. Everything else follows from it.

```
your-project/
└── specs/                  ← copy from this repo
    ├── 00-meta.md
    ├── 01-requirements.md  ← stub, fill this in a bit if you want
    └── threads/
        └── T-0001-thread-format-example.md
```

Don't forget `specs/threads/T-0001-thread-format-example.md`: agents copy its format when they raise questions, and it doubles as a worked example of a thread for you.

**2. Tell your agent about the specs system.**

Tell your agent to read `specs/00-meta.md` and to inform its agent directive file (`AGENTS.md` or `CLAUDE.md`).

Then tell it what you want to build. I have tested this extensively with Claude-Code, the agent maintains the specifications and references them to prevent specification drift and implementation sloppiness. It also massively accelerates accuracy on fresh sessions (they typically feel as on-point as long running contexts).

The agent often forgets to commit on every change, but after prompting it to do so the first time it 'twigs' and it is then consistent.

If you want to manually inform `AGENTS.md`, add this:

```markdown
## Specs

This project uses the specsify convention: read `specs/00-meta.md` in full at the start of every session and follow it.
```

In `CLAUDE.md`, write `@specs/00-meta.md` instead, which imports the whole file into context automatically.

## What agents do with this

An agent operating in a specsify project will:

1. Run `ls specs` to understand the domain landscape
2. Read the relevant specs before planning changes
3. Update specs *before or alongside* code changes (requirements first)
4. Commit specs and code together — the commit message is a label, not a rationale
5. Write each test first and watch it fail before writing the code that passes it
6. Raise questions as threads instead of blocking on chat, and keep working on whatever doesn't depend on the answer
7. Read the threads behind a spec before changing it, so settled decisions aren't reopened by accident

The result is a project where the specs stay current, every commit is traceable to intent, and any agent (or human) picking up the work can get oriented quickly.

## How threads work

1. The agent hits a decision it shouldn't make alone, so it opens `A-0007-cache-strategy.md` and tells you in chat.
2. You open the file and reply under its question as plain text. Terse is fine.
3. The agent restates its reading of your reply and you confirm it.
4. You say it's decided (confirming a reading isn't deciding). The agent renames the file to `R-…`, writes its outcome, applies the decision to specs, implements and verifies it, renames the file to `T-…` and commits everything together.

The conversation lives at the bottom of the file. Agent turns are blockquotes, and your turns are plain text:

```markdown
## Thread
> Q1. Keep this thread permanently as that example?
> Q2. Keep it in specs/threads/ alongside real threads, or in a separate examples/ directory?

yes
with the others

> Q1: adopted.
> Q2: I read "with the others" as: keep it in specs/threads/ and reject the separate directory. Confirm?

confirmed
decided
```

The filename prefix is the state (`A`ctive, `D`eferred, `R`esolved, `T`erminal, cancelled `X`), so `ls specs/threads` shows what's waiting on you. Tell the agent "threads updated" and it sweeps them.

## What this repo is

This repo holds the `specs/` directory you copy: `00-meta.md` (the framework), the `01-requirements.md` stub, and the `T-0001` example thread.

There is no tooling: I could make a claude skill but honestly it's just copying `specs/` into your project and then telling your agent to adopt this methodology.
