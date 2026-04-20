# Meta Specifications

*Explanatory document for the use of `specs/`*
*See `Geordi7/specsify` on github*

`specs/` contains specifications and user test scripts as markdown (`.md`) files.
- *specifications* describe *intended* behaviour, design decisions, and standards expected to be followed
- *directives* (like `agents.md` or `claude.md`) describe how to work on the project
- *code comments* should be reserved for local non-obvious logic

In these documents an *actor* is either a human or an AI agent.

## Top-level structure

- `00-meta.md`         - this file, describes how the specs work
- `01-requirements.md` - primary design drivers for the project
- `02-architecture.md` - technical design and key decisions: components, stacks, interfaces, deployment
- `03-data-model.md`   - structure of data and key persistence decisions
- `04-design.md`       - external surface (UI or API) design and key decisions

## Expansion and Refinement

Filenames use `##-lower-kebab-case` - Lowercase, hyphen-separated, ASCII only. No spaces.

Additional domains use `##-other-domain.md` some suggestions:
  `05-testing-standard.md`
  `06-security-standard.md`
  `07-user-experience.md`

Sub-domains use `##-##-sub-name.md` some examples:
  `03-01-primary-database-model.md`
  `03-02-memcache-model.md`

Test scripts use `##-T##-test-description.md` (see [## Test Scripts] below)

## QA and Observations

QA activities record observations **in-situ** in the relevant specification file using block quotes. **Block quotes are reserved exclusively for QA observations - no other use is permitted.** To quote external material, use fenced code blocks for verbatim text or inline italics with attribution for short phrases.

Observations are placed **immediately after the section or statement they refer to**, never floating at the top of a file. Multiple observations on the same thing stack in chronological order. Attribution is deliberately omitted - git blame is the source of truth.

### Observation conventions

Everything working as expected, optional testing details or comments

  > OK

or

  > OK
  > tested by pressing all the buttons
  > I particularly liked the confetti at the end

Mostly working:

  > PARTIAL
  > ...observations

Not working:

  > FAILURE
  > ...observations

or

  > FAILED
  > ...observations

Change Request:

  > CR
  > ...observations

Specification Adjustment (no change to the implementation expected):

  > REVIEW
  > ...observations

*The examples provided here are INDENTED so that they do not show up in searches for observations. Natural observations MUST NOT be indented.*

### Resolution

Implementing actors search for block quotes and resolve them. `OK` observations exist briefly so other actors can see "this was tested" in the working tree; once an implementing actor has acknowledged them they are removed.

- **OK** - ephemeral. Acknowledge and remove.
- **PARTIAL** / **FAILURE** - fix the code; if the observation reveals the spec is wrong, update the spec text too. Then remove the observation.
- **CR** - update the spec (and tests where applicable) to reflect the new intent, then update the code to match, then remove the observation.
- **REVIEW** - update the spec or tests to better reflect the intent

No audit trail is kept in the specifications files - git history is authoritative.

It is good practice to start at the commit to be tested and add a commit with only the observations made by the testing actor, the commit message should reflect the scope of testing performed. If project management intends for branching on observations, then it must be explicitly called out in a *directive* file.

### Operational Tools

Collect all observations using this command:

`grep -RIn --include='*.md' -E '^>' specs/`

Collect all observations WITH context using this command:

`grep -RIn -C 10 --include='*.md' -E '^>' specs/`

Surface incorrectly indented observations using this command: (this will also show the examples in this file)

`grep -RIn --include='*.md' -E '^[[:space:]]*>' specs/`

## Test Scripts

Any specification may be tested and have observations added as above. Additionally, specific **manual test scripts** may be added using the convention `##-T##-test-description.md`:

    02-T01-installation-verification.md

These are written for a testing actor and contain step-by-step instructions and acceptance criteria. Automated test suites live alongside the code they test (e.g., in `tests/` or `src/tests/`) and are not governed by this naming convention - they may, however, reference manual test specs.
