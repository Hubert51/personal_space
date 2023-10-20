### 1. 何时返回错误码，何时raise error
* raise error:
  * interupts execution
  * stack unwinding, go to the call stack
  * rich error information
    * carry rich information about what wrong, including stack trace.
  * explicit handling required
    * need something like try-catch, otherwise program will crash
  * common in modern language

* return error code
  * does not interrupt execution, the caller function receives the error code and decidde how to handle
  * manual checking,
  * limited error information
  * simpler, but can be error-prone
    * if not handle carefully, it can lead to "slient" errors that are difficult to trace
  * Common in C,C++
    * since in these language, the exception handling is not robust or unavailable

### 2. `noexcept` in C++

1. `noexcept` is a specifier in C++ that is used to indicate whether a function could potentially throw exception or not. It helps in optimizing the performance of the program by allowing the compiler to perform optimizations on functions that do not throw exceptions. 

```C++
void myFunction() noexcept {
    // Function implementation
}
/*
If function does attempt to throw an exception, the program will terminate, typically calling `std::terminate()`
*/
```

2. `noexcept` Operator:
to check function at compile time  
```C++
bool is_nothrow = noexcept(myFunction());
```

### 3. `likely attribute` & `unlikely attribute`

### 4. `volatile`
type qualifier used to notify the compiler that a particular object can be changed at any time, externally to the current code flow. Often used for 
* memory-mapped hardware registers, 
* global variables modified by an interrupt service routine
* global variable shared by multiple threads
#### Characteristics of `volatile`
1. prevents compiler optimizations: ensuring each read and write operation is executed explicitly as it appears in the code
2. ensures consistency: ensures value is read from memory every time it is accessed, maintaining consistency in multi-threading or asynchronous environments
3. used in embedded systems, multi-thread application, which variables can be changed unexpectedly
```C++
#include <iostream>
#include <thread>

volatile bool flag = false; // global flag can be changed asynchronously

void waitForFlag() {
    while (!flag) {
        // Wait until flag becomes true
    }
    std::cout << "Flag is now true." << std::endl;
}

int main() {
    std::thread t(waitForFlag); // launch a thread to wait for the flag

    std::this_thread::sleep_for(std::chrono::seconds(2)); // main thread sleeps for 2 seconds
    flag = true; // main thread modifies the flag

    t.join(); // wait for the waiting thread to complete
    return 0;
}
``` 
#### points to note:
1. not a substitute for proper synchronization
2. limited use cases:
   * proper synchronization and atomic operations are other better choices
3. specialized scenarios:
  * in embedded system or low-level programming where program direct interaction with hardware or specific memory addresses


```C++
#include <iostream>
#include <thread>
#include <atomic>

std::atomic<bool> flag(false); // global atomic flag

void waitForFlag() {
    while (!flag.load()) { // Use atomic load to read the value of the flag
        // Wait until flag becomes true
        std::this_thread::yield(); // Add this line to avoid busy waiting, make the waiting more efficient
    }
    std::cout << "Flag is now true." << std::endl;
}

int main() {
    std::thread t(waitForFlag); // launch a thread to wait for the flag

    std::this_thread::sleep_for(std::chrono::seconds(2)); // main thread sleeps for 2 seconds
    flag.store(true); // Use atomic store to modify the value of the flag

    t.join(); // wait for the waiting thread to complete
    return 0;
}
```
### 5. RAII
resource acquisition is initialization
#### Key concepts of RAII:
1. Resource acquisition: resource allocation is done during object's creation
2. automatic resource release
3. scope-based management: RAII ensures resources are held for a well-defined duration and are released when no longer needed
```C++
#include <iostream>

class IntArray {
public:
    IntArray(size_t size) : size_(size), data_(new int[size]) {
        std::cout << "Resource acquired" << std::endl;
    }

    ~IntArray() {
        delete[] data_;
        std::cout << "Resource released" << std::endl;
    }

    int& operator[](size_t i) { return data_[i]; }

private:
    size_t size_;
    int* data_;
};

int main() {
    {
        IntArray arr(10); // Resource is acquired here
        arr[0] = 1;       // Use the resource
    } // The arr object goes out of scope here, and its destructor is called, releasing the resource

    std::cout << "End of program" << std::endl;
    return 0;
}
```

#### Benefits of RAII:
1. automatric resource management: do not need to manully release the resource, reducing risk of leak.
2. exception safety: when error occurs, will still do cleanup
3. cleaner code: no need to explicit resource management and error handling code
4. scoped resource management: will be released when they go out of scope. 

In modern C++, smart pointer like `std::unique_ptr` and `std::shared_ptr` are RAII