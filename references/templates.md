# C++ Templates and Generic Programming

## Use templates when they add reusable type-level behavior

Templates are useful for generic algorithms, containers, policies, and compile-time constraints. Avoid template abstractions that make a simple operation harder to read.

## Concepts

Constrain templates with concepts so requirements appear at the interface:

```cpp
#include <concepts>

template <std::integral T>
T clamp_nonnegative(T value) {
    return value < 0 ? 0 : value;
}
```

Use standard concepts where they express the requirement. For custom concepts, name the capability rather than the implementation detail.

## Type deduction

Use `auto` when the type is evident from the initializer or is intentionally generic. Use an explicit type when it communicates an important interface contract. Be careful with proxy references and initializer-list deduction.

## Perfect forwarding

Use forwarding references and `std::forward<T>` only when a wrapper must preserve the value category:

```cpp
template <class F, class... Args>
decltype(auto) invoke_logged(F&& function, Args&&... args) {
    log_call();
    return std::invoke(
        std::forward<F>(function),
        std::forward<Args>(args)...
    );
}
```

Do not forward the same argument more than once if the callee may consume it.

## Compile-time computation

Use `constexpr` and `consteval` when compile-time evaluation makes the API safer or faster. Keep compile-time diagnostics understandable. Avoid turning ordinary runtime logic into complicated metaprogramming without a clear benefit.

## Specialization

Prefer overloads, concepts, or customization points before partial or explicit specialization. Specialization must obey the language rules and remain visible where instantiation requires it.

## Practical checks

- Test multiple types, including boundary and non-copyable types.
- Keep error messages understandable.
- Watch compile times and binary size.
- Avoid unconstrained catch-all templates that hijack unrelated overloads.
