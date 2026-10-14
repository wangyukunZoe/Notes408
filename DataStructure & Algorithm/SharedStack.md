from claude:
Since you're learning DS&A, here's the context: **"SharedStack" isn't a standard textbook term** — you won't find it in classic DS&A material (CLRS, etc.) or the C++ STL. It's a concept that shows up in real-world/systems programming, usually meaning one of these:

1. **A thread-safe stack** — a stack that multiple threads can safely push/pop from at the same time, protected by a mutex. (Most common meaning, especially in concurrency contexts.)
2. **A stack shared via smart pointers** — multiple parts of a program hold a `shared_ptr` to one stack instance instead of each having their own copy.

Since you're early in DS&A, it's worth being clear on the fundamentals first:

- **Stack** = a LIFO (Last-In-First-Out) data structure. Core operations: `push` (add), `pop` (remove top), `top`/`peek` (look at top), `empty`, `size`.
- **"Shared"** just means: instead of one thread/owner having exclusive access, it's built so multiple threads can use it safely without corrupting the data.

Here's a working C++ example — a basic **thread-safe SharedStack** using a mutex, with a demo of multiple threads pushing and popping from it:It compiles and runs correctly — one producer thread pushes 1–10, and two consumer threads safely pop values without any race conditions or crashes.**How it works, piece by piece:**

- `std::stack<T> data_` — the underlying LIFO container (this is your plain, non-shared stack under the hood).
- `std::mutex mtx_` — locked before every operation so only one thread touches `data_` at a time.
- `push()` — locks, inserts, then notifies waiting threads via the condition variable.
- `try_pop()` — non-blocking; returns `false` immediately if empty.
- `wait_and_pop()` — blocks the calling thread until something is available (good for producer/consumer patterns).
- `main()` — wraps the stack in a `shared_ptr` and hands that pointer to a producer thread and two consumer threads, demonstrating the "shared" part: all three threads point at the _same_ stack instance.

If you want, I can also show you the **non-thread-safe basic stack** (the actual DS&A fundamental you'll likely be tested on) side-by-side, or walk through how to implement a stack from scratch using an array/linked list instead of `std::stack`.

```cpp
#include <iostream>
#include <stack>
#include <mutex>
#include <memory>
#include <thread>
#include <vector>
#include <condition_variable>

// A thread-safe stack that can be safely shared across multiple threads.
// Multiple threads can hold a shared_ptr<SharedStack<T>> to the same instance.
template <typename T>
class SharedStack {
public:
    SharedStack() = default;

    // Non-copyable (the point is to share via pointer/reference, not copy)
    SharedStack(const SharedStack&) = delete;
    SharedStack& operator=(const SharedStack&) = delete;

    // Push a value onto the stack (thread-safe)
    void push(T value) {
        std::lock_guard<std::mutex> lock(mtx_);
        data_.push(std::move(value));
        cv_.notify_one(); // wake up any thread waiting to pop
    }

    // Try to pop; returns false if empty (non-blocking)
    bool try_pop(T& out) {
        std::lock_guard<std::mutex> lock(mtx_);
        if (data_.empty()) {
            return false;
        }
        out = std::move(data_.top());
        data_.pop();
        return true;
    }

    // Blocking pop; waits until an item is available
    T wait_and_pop() {
        std::unique_lock<std::mutex> lock(mtx_);
        cv_.wait(lock, [this] { return !data_.empty(); });
        T value = std::move(data_.top());
        data_.pop();
        return value;
    }

    bool empty() const {
        std::lock_guard<std::mutex> lock(mtx_);
        return data_.empty();
    }

    size_t size() const {
        std::lock_guard<std::mutex> lock(mtx_);
        return data_.size();
    }

private:
    mutable std::mutex mtx_;
    std::condition_variable cv_;
    std::stack<T> data_;
};

// ---------------- Demo ----------------

int main() {
    // Create the shared stack once; all threads get a shared_ptr to it
    auto stack = std::make_shared<SharedStack<int>>();

    // Producer thread: pushes values 1..10
    std::thread producer([stack]() {
        for (int i = 1; i <= 10; ++i) {
            stack->push(i);
            std::cout << "Produced: " << i << "\n";
            std::this_thread::sleep_for(std::chrono::milliseconds(10));
        }
    });

    // Consumer threads: pop values as they become available
    auto consumer_task = [stack](int id) {
        for (int i = 0; i < 5; ++i) {
            int value = stack->wait_and_pop();
            std::cout << "Consumer " << id << " popped: " << value << "\n";
        }
    };

    std::thread consumer1(consumer_task, 1);
    std::thread consumer2(consumer_task, 2);

    producer.join();
    consumer1.join();
    consumer2.join();

    std::cout << "Final stack empty? " << std::boolalpha << stack->empty() << "\n";
    return 0;
}
```

