# Modern C++ Practices

## Language version

Use the language version already supported by the project. Adopt C++20 or later features where the toolchain and deployment targets support them. Do not raise the minimum standard without checking consumers and CI.

## Express intent with types

- Prefer `enum class` over unscoped enums.
- Use `std::optional<T>` for a value that may be absent.
- Use `std::variant` for a closed set of alternatives.
- Use `std::expected<T, E>` where the selected standard library supports it, or a project-approved equivalent.
- Use strong types for values that must not be mixed, such as user IDs and byte counts.

## Const correctness

Make values and member functions `const` when mutation is not required. Prefer immutable inputs and narrow mutation boundaries. Do not add `const` in a way that forces unsafe casts or obscures ownership.

## Algorithms and ranges

Prefer standard algorithms over hand-written index loops when they clarify the operation:

```cpp
std::ranges::sort(users, {}, &User::last_name);
```

Use ranges and views when they keep transformations readable. Avoid deeply nested lazy pipelines that are harder to debug than an explicit loop.

## Error handling

Use exceptions for exceptional failures and return types for expected outcomes, following the surrounding codebase. Never silently swallow an error. Include context when propagating failures and avoid throwing from destructors.

## Interfaces

- Make classes small and cohesive.
- Prefer composition over inheritance unless substitutability is real.
- Keep implementation details out of public headers where possible.
- Mark intended overrides with `override`.
- Use `final` only when the design benefits from preventing extension.
- Keep constructors explicit when implicit conversion would be surprising.

## API and ABI stability

For public libraries, consider symbol visibility, inline changes, template exposure, and binary compatibility. A source-compatible change is not automatically ABI-compatible.
