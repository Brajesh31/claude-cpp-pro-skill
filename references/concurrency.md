# C++ Concurrency

## Start with the simplest correct model

Prefer, in order:

1. A normal function call for sequential work.
2. A standard algorithm with an execution policy when appropriate.
3. A task pool or executor abstraction for independent work.
4. Explicit threads when a long-lived thread has a clear ownership model.

Avoid creating one operating-system thread per small task.

## Core tools

- `std::jthread` provides automatic joining and stop-token support (C++20).
- `std::mutex` and `std::lock_guard` protect short critical sections.
- `std::unique_lock` is useful with condition variables and deferred locking.
- `std::condition_variable` coordinates waiting threads.
- `std::atomic` is appropriate for simple independently updated state.
- `std::future` and `std::promise` communicate task completion and results.

## RAII thread ownership

```cpp
#include <thread>
#include <vector>

void process_all(const std::vector<int>& values) {
    std::jthread worker([&values](std::stop_token stop) {
        for (int value : values) {
            if (stop.stop_requested()) {
                return;
            }
            process(value);
        }
    });
}
```

The example assumes `process` is defined and thread-safe. A reference capture is safe only while the referenced object outlives the worker. Scope and ownership should make that guarantee obvious.

## Mutex discipline

- Protect every access to shared mutable state with the same synchronization rule.
- Keep lock-held regions small.
- Never call unknown callbacks while holding a lock unless re-entrancy is designed.
- Avoid acquiring multiple locks in inconsistent orders.
- Use `std::scoped_lock` for locking multiple mutexes without manual ordering.

```cpp
std::mutex first_mutex;
std::mutex second_mutex;

void update_both() {
    std::scoped_lock lock(first_mutex, second_mutex);
    update_first();
    update_second();
}
```

## Atomics and memory ordering

Use the default sequentially consistent ordering until profiling or a formal correctness argument justifies a weaker order. Relaxed ordering only guarantees atomicity of the atomic object, not publication of unrelated data. Release/acquire pairs can publish prior writes when used correctly.

Do not replace a mutex with atomics merely because atomics sound faster. Correctness and contention measurements matter more.

## Cancellation and shutdown

- Define who requests cancellation and who owns shutdown.
- Make blocking operations interruptible when practical.
- Do not detach threads whose lifetime is not independently managed.
- Avoid destroying objects while worker threads may still access them.
- Propagate worker exceptions through futures or an explicit error channel.

## Testing

Run ThreadSanitizer where supported. Add tests for shutdown, cancellation, repeated start/stop, queue saturation, and exceptional paths. Stress tests are useful but do not prove the absence of data races.
