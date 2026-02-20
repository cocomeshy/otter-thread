# thread

Cross-platform threading primitives for Otter. Provides OS-level thread creation, joining, sleeping, and mutex synchronization.

## API Reference

### `thread.spawn(func:rawptr, arg:rawptr) -> rawptr`

Create a new OS thread that executes the given function.

- **Parameters:**
  - `func` — Function pointer (`&function_name`)
  - `arg` — Argument pointer passed to the function (or `null`)
- **Returns:** Thread handle
- **Platform:** `CreateThread` (Windows), `clone` syscall (Linux), `bsdthread_create` (macOS)

```otter
~> worker() {
  io.out("running in thread");
}

rock handle:rawptr = thread.spawn(&worker, null);
thread.join(handle);
```

---

### `thread.join(handle:rawptr) -> void`

Block the current thread until the target thread completes.

- **Parameters:**
  - `handle` — Thread handle from `thread.spawn`
- **Platform:** `WaitForSingleObject` (Windows), `waitid` (Linux), `bsdthread_terminate` polling (macOS)

---

### `thread.sleep(ms:int) -> void`

Suspend the current thread for the specified duration.

- **Parameters:**
  - `ms` — Milliseconds to sleep
- **Platform:** `Sleep` (Windows), `nanosleep` (Linux/macOS)

```otter
thread.sleep(1000);  // sleep 1 second
```

---

### `thread.current_id() -> int`

Get the current thread's OS-level identifier.

- **Returns:** Thread ID as integer
- **Platform:** `GetCurrentThreadId` (Windows), `gettid` (Linux), `thread_selfid` (macOS)

---

### `thread.mutex_create() -> rawptr`

Allocate a new mutual exclusion lock.

- **Returns:** Mutex handle (allocated memory)

```otter
rock mtx:rawptr = thread.mutex_create();
defer thread.mutex_destroy(mtx);
```

---

### `thread.mutex_lock(mtx:rawptr) -> void`

Acquire the mutex. Blocks if already held by another thread. Uses a compare-and-swap spinlock.

- **Parameters:**
  - `mtx` — Mutex handle from `mutex_create`

---

### `thread.mutex_unlock(mtx:rawptr) -> void`

Release the mutex, allowing other threads to acquire it.

- **Parameters:**
  - `mtx` — Mutex handle

---

### `thread.mutex_destroy(mtx:rawptr) -> void`

Free the memory used by the mutex.

- **Parameters:**
  - `mtx` — Mutex handle

```otter
rock mtx:rawptr = thread.mutex_create();
thread.mutex_lock(mtx);
// critical section
thread.mutex_unlock(mtx);
thread.mutex_destroy(mtx);
```

## Dependencies

- `memory` — for `alloc`/`free` (mutex allocation)

## Install

```
otter pkg add thread
otter pkg pull
```
