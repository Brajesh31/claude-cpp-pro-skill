# C++ Implementation Playbook

Use this checklist when implementing or reviewing a C++ change.

## Before coding

- Confirm the C++ standard, compiler, platform, and ABI constraints.
- Identify ownership, object lifetime, and error-handling conventions.
- Check existing abstractions and avoid introducing a competing style.
- Define observable behavior and edge cases before optimizing.

## Implementation sequence

1. Define narrow interfaces and value types.
2. Establish ownership with RAII.
3. Implement the simplest correct behavior.
4. Add tests for normal, boundary, and failure cases.
5. Add concurrency only when the problem requires it.
6. Profile representative workloads before optimization.
7. Document non-obvious invariants and operational assumptions.

## Review checklist

- No raw owning pointers or unmanaged resources.
- No dangling references, invalidated iterators, or accidental copies of large values.
- No data races or unclear thread shutdown.
- No ignored return values or swallowed errors.
- No unbounded allocations or unchecked input sizes.
- No premature micro-optimizations.
- Build warnings and sanitizer checks are addressed.

## Example change record

```text
Problem:
Behavior expected:
Ownership/lifetime:
Failure modes:
Tests:
Performance baseline:
Compatibility impact:
```
