
### Miscellaneous

#### `&&` *rvalue* Reference
Using `&` in the function declaration passes the arguments to it by ***lvalue*** reference. Hence this would throw and error:-
```c++
void modify(int& var) {
    var += 10;
    cout << var;
}

int main() {
    int var = 8;
    modify(var); 
    
    modify(10); // Throws error

    return 0;
}
	
```
However, in order to give an rvalue reference we can use `&&`:-
```c++
void modify(int&& var) {
    var += 10;
    cout << var;
}

int main() {
    int var = 8;
    modify(var); // Throws error 
    
    modify(10);

    return 0;
}
```
But now `modify(var)` throws an error as it is an *lvalue* reference. In order to accept both *lvalue* and *rvalue* reference, we can use template function along with rvalue reference:-
```c++
template <typename T>
void modify(T&& var) {
    var += 10;
    cout << var;
}

int main() {
    int var = 8;
    modify(var); // No error
    
    modify(10); // No error

    return 0;
}
```


#### Sleep V/S Busy Wait Loop
**Observation:** While experimenting with how DP and Recursion affects the system resources, I observed that that running three instances of the program, throttles the CPU if I use a busy-wait loop but would not throttle if use sleep(). 
**Reason:**
##### **Why `sleep()` keeps CPU usage low:**

When you use `sleep()`, the operating system suspends the execution of the thread or process for the specified duration. During this time:

- The CPU is **not actively executing instructions** for your program.
- The OS scheduler marks your program as "waiting" and allocates CPU cycles to other processes or threads.
- The CPU may even enter a low-power state (using instructions like `HLT`), conserving energy and reducing usage.

This is why the CPU usage remains low (10–20%)—your program is effectively "paused," and the CPU is free to handle other tasks.

##### **Why `clock()` causes high CPU usage:**

When you use a busy-wait loop with `clock()`, the program continuously checks the elapsed time in a loop:

```cpp
while (clock() < start_time + 500 * CLOCKS_PER_SEC / 1000);
```

Here:

- The CPU is **actively executing instructions** in the loop, repeatedly calling `clock()` and comparing values.
- This keeps the CPU "busy" and prevents it from switching to other tasks or entering a low-power state.
- Since the loop runs continuously without yielding, the CPU usage spikes to 100%.

##### **Why both resolve in the same time:**

Both methods measure time accurately, so the program finishes after the same duration. However, the difference lies in how efficiently the CPU is utilized during the wait:

- `sleep()` lets the CPU rest or work on other tasks.
- `clock()` keeps the CPU occupied, wasting cycles on unnecessary checks.

##### **Implications:**

- **Efficiency:** `sleep()` is more efficient because it frees up CPU resources for other processes.
- **Performance:** Busy-waiting (`clock()`) can degrade system performance, especially if multiple instances of the program are running, as it monopolizes CPU cycles.

#### ios::sync_with_stdio(false)
This disconnects the C-style (printf, scanf) and C++ - style (cout, cin) buffers. Usually they are synchronized. This sometimes leads to unexpected behavior when you mix C-style input and output and C++ -style input and output. Try this program : - 
```C++
#include <iostream>
#include <cstdio> // For scanf and printf
using namespace std;

int main() {
    // Disable synchronization
    ios::sync_with_stdio(false);
    cin.tie(0);

    int a, b;

    // Use scanf for input
    printf("Enter two integers: ");
    scanf("%d %d", &a, &b);

    // Use cout for output
    cout << "You entered: " << a << " and " << b << endl;

    // Use printf for output again
    printf("Using printf: You entered %d and %d\n", a, b);

    return 0;
}
```
- In some environments, you might see the `cout` output appear **after** the `printf` output, even though logically it should appear first.
- This happens because `cout` has its own buffer, which may not flush immediately, while `printf` works with a separate buffer.
- Since synchronization is disabled, the C++ I/O (`cout`) and C I/O (`printf`) buffers operate independently. As a result, the order of outputs may be inconsistent.
