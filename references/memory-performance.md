# C++ Memory and Performance

## Ownership first

- Use automatic storage for values with lexical lifetime.
- Use `std::unique_ptr` for exclusive dynamic ownership.
- Use `std::shared_ptr` only when ownership is genuinely shared.
- Use `std::weak_ptr` to observe shared objects without extending their lifetime.
- Prefer values and references for non-owning access when lifetime is clear.

Raw pointers and references are normally non-owning. Document any API that transfers ownership or retains a reference beyond the call.

## Allocation strategy

Reduce allocations by choosing appropriate data structures, reserving capacity, and reusing buffers where this improves measured workloads. Do not introduce custom allocators or object pools before profiling shows allocation overhead is material.

```cpp
std::vector<Record> records;
records.reserve(expected_count);
for (const auto& input : inputs) {
    records.emplace_back(convert(input));
}
```

## Cache locality

Contiguous storage such as `std::vector` is often faster to traverse than node-based containers because it improves locality. Choose a container based on required operations, invalidation rules, and workload, not a blanket claim that one container is always fastest.

## Move semantics

Use moves to transfer resources when the source is no longer needed. Do not add `std::move` mechanically: it can inhibit copy elision or turn a later read into a moved-from access. Return local objects by value and let modern compilers optimize the return.

## Measure before optimizing

1. Capture a representative baseline.
2. Profile CPU, allocation, and memory behavior.
3. Identify the dominant cost.
4. Change one factor at a time.
5. Re-run correctness tests and the same benchmark.
6. Report the hardware, compiler, build flags, input size, and variance.

## Undefined behavior and safety

Out-of-bounds access, use-after-free, data races, invalidated iterators, and uninitialized reads can invalidate benchmark results as well as program correctness. Use AddressSanitizer, UndefinedBehaviorSanitizer, and static analysis during development.

Avoid relying on object layout, pointer arithmetic across unrelated objects, or undocumented ABI behavior.
