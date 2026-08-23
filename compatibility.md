# PipeIgnore Compatibility

- **Status:** Draft
- **Compatibility document version:** 1
- **PipeIgnore language generation:** 1

This document records compatibility relationships between the PipeIgnore
specifications and the canonical implementation modules. It is informative
unless a normative specification explicitly references a requirement here.

## Specification compatibility

| Component                    | Version | Depends on                        | Required by Core | Included by high-level `pipeignore` by default |
| ---------------------------- | ------: | --------------------------------- | ---------------- | ---------------------------------------------- |
| PipeIgnore Core              |       1 | A Matcher conforming to Matcher 1 | N/A              | Yes                                            |
| PipeIgnore Matcher           |       1 | Git ignore pattern semantics      | Yes              | Yes, canonical matcher                         |
| PipeIgnore Standard Filters  |       1 | Core 1 filter contract            | No               | Yes                                            |
| PipeIgnore Execution Filters |       1 | Core 1 filter contract            | No               | No, opt-in                                     |

PipeIgnore Core 1 requires a matcher that conforms to the PipeIgnore Matcher 1
contract and semantics. It does not require the canonical `pipeignore-matcher`
package specifically.

Standard Filters and Execution Filters are independently optional from the
perspective of Core.

## Canonical implementation modules

| Module                    | Role                                         | Canonical dependencies                                                                                      |
| ------------------------- | -------------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| `pipeignore-core`         | Parser, AST/IR, rule and pipeline evaluator  | None of the other PipeIgnore implementation packages; matcher and filters are injected                      |
| `pipeignore-matcher`      | Canonical Matcher 1 implementation           | Implementation-specific third-party libraries MAY be used                                                   |
| `pipeignore-filters`      | Canonical Standard Filters 1 implementation  | No dependency on Core is required if the filter contract can be implemented independently                   |
| `pipeignore-filters-exec` | Canonical Execution Filters 1 implementation | Optional and security-sensitive                                                                             |
| `pipeignore`              | Batteries-included high-level API and CLI    | `pipeignore-core`, `pipeignore-matcher`, `pipeignore-filters`; Execution Filters are not enabled by default |

The high-level `pipeignore` package supplies the canonical matcher and standard
filters as defaults. Callers may replace either dependency at construction.

Supplying a custom `filters` collection replaces the default standard filter
collection unless a language-specific API explicitly documents a merge helper.

## Document version model

The following modelines are equivalent:

```text
# syntax: pipeignore
# syntax: pipeignore/1
```

An omitted version means PipeIgnore Core language version 1.

PipeIgnore 1 does not provide document-level syntax for independently selecting
Matcher, Standard Filters, or Execution Filters versions.

A future PipeIgnore language version may revise these compatibility
relationships.

## Git compatibility

A PipeIgnore document containing only ordinary Git-ignore rules is intended to
retain Git-ignore rule semantics.

A document containing PipeIgnore pipeline syntax is not a `.gitignore` document
and MUST NOT be assumed to be safely consumable by Git or by a Git-only parser.

PipeIgnore Matcher 1 uses Git's ignore-pattern behavior as its compatibility
target. Shell globbing is not an acceptable substitute.

Differential tests against `git check-ignore` are recommended for canonical
matcher implementations. Conformance fixtures remain fixed inputs for a given
PipeIgnore Matcher specification version so an external Git release cannot
silently redefine a released PipeIgnore conformance suite.

## API compatibility

All implementations agree conceptually on:

```text
Matcher:
    pattern + path + context -> boolean

Filter:
    path + arguments + context -> boolean

Core:
    document + path + matcher + filters -> ignored / included
```

Language-specific APIs MAY be idiomatic, synchronous, asynchronous, functional,
or object-oriented, provided their observable behavior remains equivalent.

Returning paths from matcher/filter library APIs is not part of the predicate
contract. Path passthrough belongs to CLI stream adapters.

## CLI compatibility

Canonical predicate CLIs should preserve the original input path representation
when emitting accepted paths.

For stream operation, exit status represents processing success rather than
whether output was produced.

CLIs that process arbitrary paths should provide a NUL-delimited mode.

## Security compatibility

`pipeignore-filters-exec` is opt-in.

Installing or importing Execution Filters MUST NOT implicitly enable `exec` or
`shell` in a PipeIgnore instance.

Unknown Core filters MUST NOT fall back to executable lookup or shell
evaluation.

Applications MAY support Standard Filters while refusing Execution Filters and
remain fully conforming to PipeIgnore Core 1.

## Fixture compatibility

The shared fixture format is versioned independently using `fixtureVersion`.

Fixture version 1 contains:

- Core fixtures with mocked matcher/filter behavior;
- Matcher fixtures;
- Standard Filters fixtures backed by a portable filesystem tree;
- Execution Filters fixtures backed by a mock process runner.

A language implementation may translate fixture data into idiomatic test
structures but should not change expected semantics.

Cases carrying `requiresSpecClarification: true` are proposed conformance cases,
not yet normative. They identify specification gaps that should be resolved
before PipeIgnore 1.0 leaves Draft status.
