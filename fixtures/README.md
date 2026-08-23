# PipeIgnore Conformance Fixtures 1

These fixtures are language-agnostic inputs for implementing tests against the
PipeIgnore 1 specification family.

## Layout

- `core/cases.json`: Core parser/evaluator cases using injected matcher/filter
  results.
- `matcher/cases.json`: Git-ignore-compatible matcher cases.
- `filters/cases.json`: Standard Filters cases using the filesystem tree under
  `fs/`.
- `filters-exec/cases.json`: Execution Filters cases using an abstract/mock
  process runner.
- `fs/`: real filesystem fixtures for Standard Filters.

## General conventions

`expected: true` means the predicate accepts or the matcher matches.
`expected: false` means it rejects or does not match.

`expectedError` means the operation must fail distinctly from returning `false`.
Concrete exception/class names are language-specific; the values in these
fixtures identify the conceptual error category.

Paths in `filters/cases.json` are relative to `filesystemRoot`.

Core fixtures deliberately inject matcher/filter outcomes. They test Core
without depending on the canonical Matcher or Standard Filters implementation.

Execution fixtures deliberately do not use real external commands. The test
harness should inject/mock process execution and assert argv, command strings,
exit handling, and launch failures.

Cases marked `requiresSpecClarification` expose an ambiguity identified during
the August 2026 specification review. They should become ordinary conformance
cases once the corresponding normative text is added.
