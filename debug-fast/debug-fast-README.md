# debug-fast

Multi-language root-cause debugging skill. Identifies the exact cause of any bug and
gives a working fix — across 12+ programming languages.

## What It Does

Drop this skill into a Claude Project and Claude will stop giving vague "there could be
many reasons" answers. Instead it classifies the bug, applies the right language-specific
diagnostic lens, and gives you a precise fix with a one-sentence explanation of why it happened.

## Triggers On

- Any error message or stack trace
- "This isn't working" / "Why is X happening"
- Performance issues, memory leaks, race conditions
- Build errors, dependency conflicts, deployment failures
- "Why does my code do Y instead of Z"

## Output Format

Every response follows:
```
Root cause: [one sentence]
Fix: [minimal code change]
Why: [one sentence on the underlying concept]
```

## Language Coverage

| Language | What's Covered |
|---|---|
| JavaScript / TypeScript | Null errors, async/closures, stale state, React patterns |
| Python | AttributeError, async, mutable defaults, profiling |
| Go | Nil pointers, goroutine leaks, data races, pprof |
| Rust | Borrow checker, lifetimes, unwrap panics, ASAN |
| Java / Kotlin | NPE, deadlocks, thread dumps, heap analysis |
| C# / .NET | Async deadlocks, disposed objects, nullable refs |
| C++ | Segfaults, memory leaks, Valgrind, AddressSanitizer |
| HTML / CSS | Layout bugs, z-index, flexbox, overflow, Tailwind |
| SQL | N+1 queries, missing indexes, NULL bugs, deadlocks |
| Ruby / Rails | Safe navigation, N+1, strong params, pry debugging |
| PHP / Laravel | Null coalescing, Eloquent N+1, CSRF, auth middleware |

Plus a universal methodology: binary search, bracket the lie, recent changes check.
