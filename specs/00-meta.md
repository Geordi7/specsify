# Meta Specifications

*Explanatory document for the use of `specs/`*
*See `Geordi7/specsify` on github*

`specs/` contains specifications and user test scripts as markdown (`.md`) files.
- *specifications* describe *intended* behaviour, design decisions, and standards expected to be followed
- *directives* (like `agents.md` or `claude.md`) describe how to work on the project
- *code comments* should be reserved for local non-obvious logic and references to specifications

In these documents an *actor* is either a human or an AI agent.

## Structure

Specs are defined across a tree of files located in this directory. A directory listing (ls) must be sufficient to get a sense of how the project is specified. The goal is to have a coherent traversible set of specifications that an agent can pull from to get the context it needs. Specs are organized into top level 'domains' and 'refinements' and 'test suites' below them.

**CRITICAL:** Spec files must never exceed 150 lines. Once a domain grows beyond what is manageable in 150 lines, it must be split into refinements.

**CRITICAL:** Root domain specs for refined domains must never exceed 50 lines. A domain with refinements must have inline summaries for the refinements in its root domain file, and leave all detail to the refinements. Keep only *the most critical or globally applicable information* in the root.

### Base

Specs are defined in terms of domains which are numbered starting at zero. Throughout this document `##` denotes a zero-padded two-digit number (e.g., `02`, `03`). Every domain has a root file and may have refinements, the two required domains are:

| Domain File | Purpose |
|-------------|---------|
| `00-meta.md` | this file, describes how to operate in a specs driven project |
| `01-requirements.md` | primary design drivers for the project |

### Expansion

Thereon domains are defined as needed using the `##-lower-kebab-case` convention. Most projects will need these:

| Domain File | Purpose |
|-------------|---------|
| `02-architecture.md` | technical design and key decisions: components, stacks, interfaces, deployment |
| `03-data-model.md` | structure of data and key persistence decisions |

And other recommended domains are:

| Domain File | Purpose |
|-------------|---------|
| `##-security-standard.md` | Capture explicit security related configuration constraints or requirements |
| `##-design.md` | Capture external surface (UI or API) constraints and design |
| `##-testing.md`| Capture testing strategy and tools (not for specific tests) |
| `##-user-experience.md` | Capture constraints and design for intended user experience |

### Refinement

Once a domain is sufficiently large it should be split into refinements using the `##-##-sub-domain.md` convention. These can be further refined using `##-##-##-sub-sub-domain.md` and so on... Once a domain is refined parent files should become minimal summaries for their refinements so as not to pollute agent contexts. Some examples:

Given `02-architecture.md` which contains short descriptions of the following:
- `02-00-modules-components.md`
- `02-01-coding-standard.md`
- `02-02-persistence-layer.md`
- `02-03-application-nodes.md`

### Test Suite Files

Manual test suites follow their own naming convention — see [Testing](#testing) below.

## Testing

### Test Suites

Any specification may be tested directly against its prose. Additionally, specific **manual test suites** for test actors may be added using the convention `{}-T##-test-description.md`, where `{}` is the full address of the corresponding domain or refinement. For example, a test suite for `02-01-coding-standard.md` would be `02-01-T00-naming-rules.md`, and for the root `02-architecture.md` it would be `02-T00-smoke-test.md`. These should complement automated and agent tests, not replace them.

Do not liberally create test suites, expect that testers can perform most tests just by being prompted with the relevant specifications. Test suites are intended for functionality which is ultra-critical or requires precise actions in order to properly test.

### Automated Tests

Tests at any level — unit, integration, end-to-end — may be written against any part of the project. Whether a given test is warranted is the implementing actor's decision, weighed on risk, complexity, and the criticality of the requirement it serves. Where a test is warranted, it is written before the code that satisfies it.

Every new or changed test must be **seen failing first**. In particular, if a test is written against already existing functionality, exercise it against a deliberately broken mock of that functionality FIRST, and iterate the mock until every assertion in the test has been shown to fail at least once. The mock lives separately from the real code — a stub, fake, or injected double — and the real code is never edited to produce the failure. Discard the mock once the test is verified; it must never reach a commit.

In general:

- Write the test, run it, keep the failure output.
- The failure must be the *intended* one — an assertion on real behaviour, not an import, syntax, collection, or fixture error. Those mean the test is broken.
- A test that never fails proves nothing. Fix the test, not the code.
- Then implement (or point the test back at the real code), re-run, confirm it passes and breaks nothing else.
- Bug fixes start with a test that reproduces the bug.

State the observed failure and the subsequent pass when reporting verification. An actor who cannot show a test failing has not tested it.

## Operations

Agents operating in this project should always include this document in its entirety in their context window. Add relevant include-directives in AGENTS.md or equivalent. Agents can use the following tools to better navigate the specs:

### Specification Review

Use `ls specs` to get an map of the specification domains, those documents represent the intent of the project. Do not review `00-meta.md`, its contents should already be represented in your context. Never change `00-meta.md`, if you find it problematic discuss potential changes with a human operator.

### Change Process

Before starting, scope the work: if the change touches more than one separable concern, divide it into independent activities now — splitting at commit time is too late.

For each activity:

- Review relevant specifications to form an implementation plan.
- Determine which specifications need to change — always consider requirements first.
- Update specifications as needed. Refine domains that exceed 150 lines; pare parent files to < 50 lines once refined.
- Make changes to project files.
- Verify (automated tests — see [Automated Tests](#automated-tests) — agent tests, UAT, etc.) and correct as needed.
- Commit specs and project files together. The commit message is a label — one short sentence, no rationale. The rationale lives in the spec changes. If you cannot label the commit in one sentence, the spec changes are not clear enough yet; clarify them before committing. A change is not complete until it is committed.
