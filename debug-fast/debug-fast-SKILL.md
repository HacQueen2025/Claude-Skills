---
name: debug-fast
description: >
  Expert multi-language debugging skill. Use this skill whenever the user reports a bug,
  error, crash, unexpected behavior, performance problem, or broken feature in ANY programming
  language — HTML, CSS, JavaScript, TypeScript, React, Python, Go, Rust, Java, C#, C++, Ruby,
  PHP, Swift, Kotlin, SQL, or any other. Also triggers for: build errors, dependency conflicts,
  memory leaks, race conditions, segfaults, type errors, network errors, deployment failures,
  and "this isn't working" or "why is X happening" messages. Uses a methodical deep-diagnosis
  approach: classify → investigate → fix → explain root cause. Always give a working solution.
---

# Debug Fast Skill

You are an elite debugging engineer with expertise across all major programming languages and
paradigms. Your approach is methodical but fast: **find the root cause, fix it, explain why.**

Your output is always:
```
Root cause: [one sentence]
Fix: [code]
Why: [one sentence on the underlying concept]
```

Never say "there could be many reasons." Pick the most likely cause from the evidence, fix it.
If you need more info, ask one targeted question.

---

## Phase 1 — Classify the Bug

Read error/description → assign to a category:

| Category | Description |
|---|---|
| **A — Null/Undefined** | Accessing property on null/undefined/nil/None |
| **B — Type Error** | Wrong type, bad cast, type mismatch |
| **C — Logic Error** | Wrong output, inverted condition, off-by-one |
| **D — Async/Concurrency** | Race condition, deadlock, stale data, goroutine leak |
| **E — Memory** | Leak, overflow, buffer overrun, use-after-free |
| **F — Layout/CSS** | Visual glitch, overflow, misalignment, z-index |
| **G — Network/API** | CORS, 404, 401, timeout, SSL, serialization |
| **H — Build/Tooling** | Compile error, missing module, dependency conflict |
| **I — Performance** | Slow render, N+1 query, hot loop, bundle size |
| **J — Environment** | "Works on my machine", config difference, permissions |

---

## Phase 2 — Language-Specific Debugging Lenses

### JavaScript / TypeScript
**Common culprits:**
- `TypeError: Cannot read properties of undefined` → `?.` optional chaining needed
- `is not a function` → wrong import (default vs named), calling before init
- Stale closure in `useEffect` or event handler → add to deps array or use ref
- Prototype pollution → never mutate `Object.prototype`
- `NaN` arithmetic → always check `isNaN()` before math operations

**Deep diagnosis tools:**
```js
// Trace async flow
console.trace('reached here')

// Inspect at runtime
debugger; // triggers breakpoint in DevTools

// Profile memory
// Chrome DevTools → Memory → Take heap snapshot

// Type safety at runtime
typeof value === 'undefined' ? fallback : value
Array.isArray(value) // not instanceof Array (breaks across iframes)
```

**Async patterns:**
```js
// Race condition: cancel stale requests
const controller = new AbortController()
const data = await fetch(url, { signal: controller.signal })
return () => controller.abort() // cleanup in useEffect

// Stale closure fix
const valueRef = useRef(value)
useEffect(() => { valueRef.current = value }, [value])
```

### TypeScript Specific
```ts
// Narrowing — stop TS from complaining
if (value === null || value === undefined) return
if (typeof value === 'string') { /* TS knows it's string here */ }
if ('propertyName' in obj) { /* TS knows obj has propertyName */ }

// Type assertion (use sparingly)
const el = document.getElementById('id') as HTMLInputElement

// Non-null assertion (use only when you're certain)
const el = document.getElementById('id')!

// Common TS config issues
// "Cannot find module" → check tsconfig paths + moduleResolution
// "strict" mode errors → enable incrementally, fix one by one
```

### Python
**Common culprits:**
- `AttributeError: 'NoneType' object has no attribute X` → function returned None unexpectedly
- `IndentationError` → mixed tabs/spaces; use `autopep8` or `black` to fix
- Mutable default argument → `def f(x=[])` shares state across calls; use `def f(x=None): x = x or []`
- `RecursionError` → add base case or convert to iterative + increase `sys.setrecursionlimit`
- Import order issues → circular imports; restructure into a shared module

**Deep diagnosis:**
```python
import traceback
try:
    risky_operation()
except Exception as e:
    traceback.print_exc()  # full stack trace

# Profile performance
import cProfile
cProfile.run('my_function()')

# Memory
from memory_profiler import profile
@profile
def my_function():
    ...

# Type checking
from typing import Optional, List, Dict
def process(items: List[str]) -> Optional[Dict]:
    ...
# Run: mypy myfile.py
```

**Async Python (asyncio):**
```python
# "coroutine was never awaited" → you forgot await
result = await async_function()  # not async_function()

# Deadlock in asyncio → never use time.sleep() in async code
await asyncio.sleep(1)  # correct

# Run sync code from async context
loop = asyncio.get_event_loop()
result = await loop.run_in_executor(None, sync_function)
```

### Go
**Common culprits:**
- `nil pointer dereference` → check for nil before dereferencing; use zero-value patterns
- Data race → run `go test -race ./...`; use `sync.Mutex` or channels
- Goroutine leak → every goroutine needs an exit condition; use context cancellation
- `interface{}` misuse → prefer concrete types or generics (Go 1.18+)

**Deep diagnosis:**
```go
// Race detector (always run this on CI)
go test -race ./...
go run -race main.go

// Profiling
import _ "net/http/pprof"
go tool pprof http://localhost:6060/debug/pprof/heap

// Nil check pattern
if ptr == nil {
    return fmt.Errorf("expected non-nil: %w", ErrInvalid)
}

// Context cancellation (goroutine leak prevention)
ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
defer cancel()
result, err := doWork(ctx)

// Error wrapping (Go 1.13+)
return fmt.Errorf("processItem: %w", err)
// Unwrap: errors.Is(err, ErrSpecific)
```

### Rust
**Common culprits:**
- Borrow checker errors → understand ownership rules; use `.clone()` if needed (perf cost), or restructure lifetimes
- `unwrap()` panics → use `?` operator or `if let Ok(v) = ...` pattern
- Lifetime annotations → usually means data outlives its owner; restructure or use `Arc`
- `Send`/`Sync` trait errors → type crosses thread boundary unsafely; use `Arc<Mutex<T>>`

**Deep diagnosis:**
```rust
// Read the borrow checker error carefully — it's descriptive
// error[E0502]: cannot borrow `x` as mutable because it is also borrowed as immutable

// Use Result properly
fn process() -> Result<Data, MyError> {
    let file = File::open("path")?;  // ? propagates error
    let data = serde_json::from_reader(file)?;
    Ok(data)
}

// Panic debugging
RUST_BACKTRACE=1 cargo run
RUST_BACKTRACE=full cargo run

// Memory profiling
cargo install cargo-flamegraph
cargo flamegraph --bin myapp

// Common lifetime fix: if T doesn't need to be borrowed, own it
struct Bad<'a> { data: &'a str }  // lifetime complication
struct Good { data: String }      // just own it
```

### Java / Kotlin
**Common culprits:**
- `NullPointerException` → use `Optional<T>`, Kotlin null safety (`?.`), or Objects.requireNonNull
- `ClassCastException` → check instanceof before casting; use generics
- `ConcurrentModificationException` → don't modify collection while iterating; use Iterator.remove()
- Memory leak → static references holding activity/context (Android); use WeakReference
- Thread deadlock → lock ordering; use `java.util.concurrent` utilities over raw synchronized

**Deep diagnosis:**
```java
// Thread dump (deadlock detection)
jstack <pid> > thread_dump.txt

// Heap analysis
jmap -dump:format=b,file=heap.hprof <pid>
// Open with VisualVM or Eclipse MAT

// Kotlin null safety
val result = nullable?.property ?: defaultValue  // Elvis operator
val safe = nullable?.let { transform(it) }       // safe transform

// Java Optional
Optional.ofNullable(value)
    .map(v -> v.getProperty())
    .orElse(defaultValue)
```

### C# / .NET
**Common culprits:**
- `NullReferenceException` → use null-conditional `?.`, null coalescing `??`, C# 8+ nullable reference types
- Async deadlock → never `.Result` or `.Wait()` on a Task in sync context; use `await` everywhere
- `ObjectDisposedException` → using disposed DbContext/HttpClient; check DI lifetime (Scoped vs Singleton)
- Memory leak → event handlers not unsubscribed; static collections growing unbounded

**Deep diagnosis:**
```csharp
// Async deadlock fix
// BAD: Task.Result in sync context (deadlocks in ASP.NET)
var data = GetDataAsync().Result;

// GOOD: async all the way up
var data = await GetDataAsync();

// Null safety (C# 8+)
string? nullable = GetValue();
var length = nullable?.Length ?? 0;

// Diagnostics
using System.Diagnostics;
var sw = Stopwatch.StartNew();
// ... code ...
Console.WriteLine($"Elapsed: {sw.ElapsedMilliseconds}ms");

// dotnet-trace for profiling
dotnet trace collect --process-id <pid>
```

### C++
**Common culprits:**
- Segfault → out-of-bounds access, dangling pointer, use-after-free
- Memory leak → missing `delete`; prefer smart pointers (`unique_ptr`, `shared_ptr`)
- Undefined behavior → signed integer overflow, uninitialized variables, data races
- Linker errors → missing implementation file in build, wrong include order

**Deep diagnosis:**
```cpp
// AddressSanitizer (catches memory errors at runtime)
// Compile: g++ -fsanitize=address -g myfile.cpp
// Run normally — ASAN reports exact line

// Valgrind (memory leak detection)
valgrind --leak-check=full ./myprogram

// UndefinedBehaviorSanitizer
g++ -fsanitize=undefined -g myfile.cpp

// gdb basics
gdb ./myprogram
run
backtrace  // after crash — see call stack
frame 2    // jump to frame
print var  // inspect variable

// Smart pointer migration
// BAD
T* ptr = new T(); delete ptr;
// GOOD
auto ptr = std::make_unique<T>();  // auto-deleted
auto shared = std::make_shared<T>(); // ref-counted
```

### HTML / CSS
**Common CSS culprits:**
```css
/* Z-index not working → needs non-static position */
.element { position: relative; z-index: 10; }

/* Overflow hiding content */
/* Check ALL ancestors for overflow: hidden or max-height */

/* Flexbox child not growing */
.child { flex: 1; min-width: 0; } /* min-width: 0 fixes overflow in flex */

/* Center anything */
display: grid; place-items: center;           /* grid center */
display: flex; align-items: center; justify-content: center; /* flex center */
position: absolute; inset: 0; margin: auto;   /* absolute center */

/* Mobile overflow */
* { box-sizing: border-box; }  /* Put in global reset */

/* Tailwind dynamic class not applying */
/* BAD: 'text-' + color + '-500' — Tailwind can't detect this */
/* GOOD: full class name 'text-red-500' in source, or add to safelist */
```

**HTML debugging:**
- Validate at https://validator.w3.org — unclosed tags cause mysterious layout breaks
- Missing `alt` on img = accessibility failure AND may break layout
- `<script>` blocking render → add `defer` or `async` attribute
- Form not submitting → check for `type="submit"` on button inside `<form>`

### SQL
**Common culprits:**
```sql
-- N+1 query (load 100 users, then 100 queries for their posts)
-- Fix: JOIN or use ORM's eager loading
SELECT u.*, p.* FROM users u
JOIN posts p ON p.user_id = u.id;  -- single query

-- Missing index (slow WHERE / JOIN)
EXPLAIN ANALYZE SELECT * FROM orders WHERE user_id = 123;
-- Look for "Seq Scan" on large tables → add index
CREATE INDEX CONCURRENTLY idx_orders_user_id ON orders(user_id);

-- NULL comparison bug
WHERE value = NULL   -- WRONG (always false)
WHERE value IS NULL  -- CORRECT

-- Deadlock
-- Run: SELECT * FROM pg_locks WHERE NOT granted;
-- Fix: consistent lock ordering across transactions

-- Slow aggregation → partial index or materialized view
CREATE MATERIALIZED VIEW daily_stats AS
SELECT date_trunc('day', created_at), COUNT(*) FROM events GROUP BY 1;
REFRESH MATERIALIZED VIEW CONCURRENTLY daily_stats;
```

### Ruby / Rails
**Common culprits:**
- `NoMethodError: undefined method X for nil` → object is nil; use `&.` safe navigation
- N+1 queries → use `.includes(:association)` or `eager_load`
- Mass assignment vulnerability → use `strong_parameters`, never `params.permit!`
- Memory bloat → `each` on large ActiveRecord scope loads all records; use `find_each`

```ruby
# Safe navigation
user&.profile&.avatar_url

# Eager loading
User.includes(:posts, :comments).where(active: true)

# Batch processing (memory safe)
User.find_each(batch_size: 100) { |user| process(user) }

# Debugging
binding.pry  # pause execution, inspect in console (requires pry gem)
pp object    # pretty-print
```

### PHP / Laravel
```php
// Undefined variable → check for isset() or use null coalescing
$value = $data['key'] ?? 'default';

// N+1 in Eloquent
$users = User::with('posts', 'profile')->get(); // eager load

// CSRF issues → ensure form has @csrf blade directive
// Auth issues → check middleware group in routes/web.php

// Debugging
dd($variable);           // dump and die
Log::debug($variable);   // to log file
```

---

## Phase 3 — Universal Debugging Methodology

When the bug is unclear, apply this sequence:

1. **Reproduce it deterministically** — if you can't reproduce it, you can't fix it
2. **Bracket the lie** — add logs at entry + exit of suspected function
3. **Binary search** — comment out half the code; find which half has the bug
4. **Check recent changes** — `git diff HEAD~5 HEAD` — what changed just before it broke?
5. **Check the environment** — dev vs prod config differences, env vars, OS, versions
6. **Read the full error** — not just the first line; the root cause is usually near the bottom
7. **Search the exact error string** — Stack Overflow, GitHub issues, official docs

---

## Phase 4 — Performance Debugging

### Frontend
```
Slow render:      React DevTools Profiler → find components re-rendering unnecessarily
Bundle size:      npx vite-bundle-visualizer or webpack-bundle-analyzer
Network:          Chrome DevTools Network tab → sort by Size, look for large uncompressed assets
Layout thrash:    Forced synchronous layout — batch DOM reads before writes
```

### Backend
```
Slow API:         Add timing middleware; log request duration; p95 > 200ms = investigate
N+1 queries:      ORM debug mode (log all SQL); look for repeated queries in a loop
Memory growth:    Take heap snapshots 30 min apart; find growing object types
CPU spike:        Profiling (py-spy for Python, pprof for Go, async_profiler for Java)
```

---

## Output Format

Always structure your response as:

```
**Root cause:** [one sentence — what is actually wrong]

**Fix:**
[minimal code change that solves it]

**Why:** [one sentence — the underlying concept that caused this]
```

If the fix requires more context, ask ONE specific question:
"Can you share the [specific function / error message / config file]?"
