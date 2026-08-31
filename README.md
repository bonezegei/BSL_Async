# BSL_Async

**BSL_Async** is an asynchronous execution and multithreading library designed for the **Bonezegei Scripting Language (BSL)**. It provides a simple, object-oriented approach to spawn background worker tasks, execute non-blocking delayed timer callbacks, and synchronize concurrent threads using cross-platform mutexes within your `.bzg` scripts.

## Table of Contents
- [Installation](#installation)
- [Getting Started](#getting-started)
- [Code Examples](#code-examples)
  - [1. Asynchronous Tasks and Non-blocking Timers](#1-asynchronous-tasks-and-non-blocking-timers)
  - [2. Thread Synchronization with Mutexes](#2-thread-synchronization-with-mutexes)
  - [3. Parallel Batch Processing](#3-parallel-batch-processing)
- [API Reference](#api-reference)
- [License & Author](#license--author)
- [Citation](#citation)

## Installation

Install `BSL_Async` using the BSL Package Manager (`bzg`):

```bash
bzg install async
```

## Getting Started

To use the library in your script, include the async module after installation and instantiate the object:

```javascript
include("lib/async.bzg");

var a = async();
```

## Code Examples

### 1. Asynchronous Tasks and Non-blocking Timers

This example demonstrates how to spawn concurrent background worker threads and set up non-blocking sleep callbacks.

```javascript
/*
    Async Tasks and Non-blocking Timers Example
    Author: Jofel Batutay (Bonezegei)
    Date: August 31, 2026
*/

// include the library after Installation (" bzg install async ")
include("lib/async.bzg");

var a = async();

// 1. Run a task in the background concurrently
a.run(function(data) {
    print("Background worker received: " + data);
}, "Task Payload 1");

// 2. Non-blocking delay (sleep for 2000 milliseconds before calling callback)
a.sleep(2000, function(msg) {
    print("Timer finished: " + msg);
}, "Delayed 2 Second");

print("Main script continues executing without blocking!");

// Keep the main thread alive to allow background tasks to complete
delay(5000);
```

### 2. Thread Synchronization with Mutexes

This example demonstrates how to create a thread-safe critical section using mutex locks to prevent race conditions when updating shared variables.

```javascript
/*
    Thread Synchronization with Mutexes Example
    Author: Jofel Batutay (Bonezegei)
    Date: August 31, 2026
*/

include("lib/async.bzg");

var a = async();

// Create a mutex lock handle
var lock_id = a.mutex();
var counter = 0;

// Worker task 1
a.run(function(id) {
    a.lock(lock_id);
    print("Worker " + id + " entering critical section.");
    counter = counter + 1;
    print("Counter updated to: " + counter);
    a.unlock(lock_id);
}, 1);

// Worker task 2 running simultaneously
a.run(function(id) {
    a.lock(lock_id);
    print("Worker " + id + " entering critical section.");
    counter = counter + 1;
    print("Counter updated to: " + counter);
    a.unlock(lock_id);
}, 2);

// Keep the main thread alive to allow tasks to finish
delay(2000);
```

### 3. Parallel Batch Processing

This example demonstrates how to dispatch multiple parallel background jobs with varying delays and safely track completion progress using a mutex lock.

```javascript
/*
    Parallel Batch Processing Example
    Author: Jofel Batutay (Bonezegei)
    Date: August 31, 2026
*/

include("lib/async.bzg");

var a = async();

var lock_id = a.mutex();
var completed_count = 0;
var total_jobs = 3;

function dispatchJob(job_id, delay_ms) {
    a.sleep(delay_ms, function(id) {
        // Critical section to update shared stats
        a.lock(lock_id);
        completed_count = completed_count + 1;
        print("Job #" + id + " completed! Progress: " + completed_count + "/" + total_jobs);
        
        if (completed_count == total_jobs) {
            print("All parallel jobs have finished executing.");
        }
        a.unlock(lock_id);
    }, job_id);
}

// Dispatch 3 concurrent background jobs with different delay durations
print("Dispatching parallel jobs...");
dispatchJob(101, 800);  // Finishes 1st
dispatchJob(102, 1200); // Finishes 2nd
dispatchJob(103, 1500); // Finishes 3rd

// Keep the main thread alive to watch all jobs finish
delay(5000);
```

---

## API Reference

| Function / Method Signature | Return Value | Description |
| :--- | :--- | :--- |
| `async()` | `Object` | Factory constructor that initializes and returns a new `async` object with attached native methods. |
| `async.run(callback_fn, [optional_arg])` | `number` / `null` | Spawns a background thread that executes `callback_fn(optional_arg)` concurrently. Returns `1` on success, or `null` on failure. |
| `async.sleep(ms, callback_fn, [optional_arg])` | `number` / `null` | Spawns a background thread that waits for `ms` milliseconds before executing `callback_fn(optional_arg)`. Returns `1` on success, or `null` on failure. |
| `async.mutex()` | `number` | Allocates and returns a numerical handle ID for a new thread-safe cross-platform mutex. Returns `-1` if maximum limit is reached. |
| `async.lock(handle)` | `number` / `null` | Blocks the calling thread until the specified mutex handle is acquired. Returns `1` on success, or `null` on invalid handle. |
| `async.unlock(handle)` | `number` / `null` | Releases the specified mutex handle lock. Returns `1` on success, or `null` on invalid handle. |

---

## License & Author

* **Author:** Jofel Batutay ([Bonezegei](https://github.com/bonezegei))
* **Date:** August 31, 2026
* **Website:** [bonezegei.com](https://bonezegei.com)

## Citation 
[![DOI](https://zenodo.org/badge/1352175674.svg)](https://doi.org/10.5281/zenodo.22204326)

If you use this library, please cite it as below:

**APA Format:**

Batutay, J. (2026). *bonezegei/BSL_Async* [Computer software]. https://doi.org/10.5281/zenodo.22204326
