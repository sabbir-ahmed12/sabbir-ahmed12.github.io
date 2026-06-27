---
layout: post
title: Pointer in C/C++
date: 2026-06-027 00:57:00-0400
description: Learn about pointer and it's application in C/C++
tags: C++
categories: tutorial
giscus_comments: true
related_posts: false
---

# What is a Pointer?
A pointer is a variable that stores the memory address of another variable. For example - in the program below, `a` is an integer variable which represents a memory address. Let's say `10` is stored at address `200`(here memory address is represented in integer value for explaining the concept but it is usually represented in hexadecimal form) inside computer memory. Remember that an integer usually takes 4 bytes of memory. However, now the variable `a` represents address `200`. A pointer variable is a variable which can store that address instead of storing the value `10`. However, the type of the pointer variable must be same as the data type of the variable.

```c++
#include<iostream>

int main() {
    int a = 10;
    return 0;
}
```

## Declaring Pointers in C++

```
data_type* pointer_name;

// Pointer to an integer
int* intPtr;
```
Or,
```
data_type *pointer_name;

// Pointer to a character
char *cPtr;
```

## Initializing Pointers in C++
There are two ways of initializing a pointer safely.

1. **Assigning an existing variable's address:** The most common way of initializing a pointer is by storing the address of an already declared variable. It stores the address of the variable and can access or modify the underlying value as shown below:

    ```c++
    #include<iostream>

    int main() {
        int num = 12;
        int* ptr = &num;

        // Display the stored address
        std::cout << ptr << std::endl;

        return 0;
    }
    ```
    > To get the address of variable `num`, you need to use the `&` (also known as reference operator) operator.

2. **Using `nullptr` (Modern C++):** Sometimes we need to declare a pointer in advance, even if we don’t yet have a variable for it to point to—for example, when a pointer will later be assigned dynamically allocated memory or a value returned by a function. In such cases, we should initialize the pointer with nullptr. This prevents it from pointing to random memory and makes it safer to check before using it:

    ```c++
    // Does not point anywhere
    int* ptr = nullptr;
    ```

## Dereferencing a Pointer
Dereferencing a pointer means to access or modify the data stored at the memory address held by a pointer. `*` operator is used to dereference a pointer. 

```c++
#include<iostream>

int main() {
    int num = 10;
    int* ptr = &num;

    // Display the address
    std::cout << ptr std::endl;

    // Dereferencing pointer
    std::cout << *ptr << std::endl;

    // Modifying underlying value
    *ptr = 12;

    // Print updated value
    std::cout << num << *ptr << std::endl;

    return 0;
}
```
We can also change the underlying value in that address using the pointer and when we do that, both the `num` variable and the pointer variable will show the modified value.

> **NOTE:** If we attempt to dereference a null pointer, the program will typically crash or throw a runtime error instead of silently reading or writing garbage memory. This makes bugs easier to detect. By initializing a pointer with `nullptr`, we ensure it doesn’t accidentally access random memory, and we can safely check whether it points to a valid location before using it:

```c++
if (ptr != nullptr) { 
    *ptr = 10;  // Change the stored value
} 
```

## Types of Pointer in C++:

### Null Pointer
A null pointer doesn’t point to any memory location. In modern C++, it is represented using the keyword `nullptr`. Using `nullptr` helps ensure that a pointer does not accidentally reference garbage memory. 

For example, the code block below shows how `nullptr` can prevent undefined behavior by allowing us to check whether a pointer is pointing to a valid memory location before using it:

```C++
#include<iostream>

int main() {
    int* ptr = nullptr; 

    if (ptr != nullptr) { 
        *ptr = 10;  // Safe: This block runs only if ptr points to valid memory 
    } else { 
        std::cout << "Pointer is null, cannot assign value.\n"; 
    } 

    return 0;
}
```

### Void Pointer
A void pointer (also called a generic pointer) can hold the memory address of any data type, but it cannot be dereferenced directly without typecasting.

When the type is known at compile time and no runtime checks are needed, static casting is used:

```c++
#include<iostream>

int main() {
    void* ptr;
    int num = 12;
    ptr = &num;

    // Casting to access the value
    std::cout << *((int*)ptr) << std:endl;

    return 0;
}
```

> Casting was done by `(int*)` and to dereference the integer pointer we used `*((int*)ptr)`.

### Dangling Pointer
A dangling pointer is one that points to memory that has already been freed or deleted. Accessing such memory is unsafe and may crash the program.

```c++
#include<iostream>

int main() {
    // Using heap memory to store 5
    int* ptr = new int(5);

    // Accessing underlying value
    std::cout << *ptr << std::endl;

    // Delete the pointer
    delete ptr;
    // ptr is now dangling (Invalid memory reference) 
    
    return 0;
}
```

### Wild Pointer
A wild pointer is an uninitialized pointer that points to a random memory location. Dereferencing such a pointer is dangerous because it can cause undefined behavior, crashes, or data corruption:

```c++
int *ptr;  // Not initialized
*ptr = 10;  // Crash the program with error
```

### Constant Pointer
A constant pointer is a pointer that, once initialized, cannot point to a different address, but the value stored at that address can still be modified:

```c++
#include<iostream>

int main() {
    int x = 5, y = 10;
    int* const ptr = &x;

    // Stored value can be changed
    *ptr = 20;

    // Stored address can't be changed
    ptr = &y;    // Not allowed

    return 0;
}
```
> We can also make the value constant by using `const` before the data type e.g., `const int* const ptr = &x;`.

## Pointers and Arrays
In C++, when we assign an array to a pointer, the pointer refers to the first element of the array. From there, we can use pointer arithmetic (see the next section) to access different elements, just as we would with indexing.

When we write:

```c++
int arr[3] = {10, 20, 30}; 
int* ptr = arr; 
```
Here:
- `arr` gives the address of the first element (`&arr[0]`).
- `ptr` now points to the address of the first element.

Now `ptr` is equivalent to `arr[0]`, and (`ptr + i`) is equivalent to `arr[i]`.

Using pointers with arrays is useful because it allows more efficient traversal and manipulation of array elements, especially in functions, without copying the entire array. Pointer arithmetic can also simplify operations like iterating through dynamically allocated arrays or working with segments of an array.

The example below shows how we can access array elements using pointers:

```c++
#include <iostream> 
using namespace std; 

int main() { 
    double numbers[] = {2.5, 4.5, 6.5, 8.5}; 
    double* ptr = numbers; 

    for (int i = 0; i < 4; i++) { 
        // Using pointer arithmetic instead of array indexing
        cout << "numbers[" << i << "] = " << *(ptr + i) << endl; 
    } 

    return 0; 
}  
```

The output will be:
```c++
numbers[0] = 2.5 
numbers[1] = 4.5 
numbers[2] = 6.5 
numbers[3] = 8.5 
```

Since pointers give direct access to memory, we can also change array values using them. This can be useful when we want to efficiently modify elements in an array without using the array index notation, especially in scenarios like passing arrays to functions, iterating through large datasets, or implementing dynamic data structures where direct memory manipulation improves performance:

```c++
#include <iostream> 
using namespace std; 

int main() { 
    int arr[3] = {1, 2, 3};  
    int* ptr = arr;  

    *ptr = 10;             // Changes arr[0] to 10  
    *(ptr + 1) = 20;  // Changes arr[1] to 20  

    for (int i = 0; i < 3; i++) { 
        cout << arr[i] << " "; 
    } 

    return 0; 
}  
```

The output will be:
```
10 20 3 
```

## Pointer Arithmetic
Pointers in C++ allows us to perform arithmetic operations using them. It allows us to move through memory locations efficiently, especially when working with arrays. However, it supports only a limited number of well-defined operations.

### Increment (++)
The increment operator (++) moves the pointer to the next memory location based on its data type:

```c++
#include <iostream> 
using namespace std; 

int main() { 
    int arr[] = {10, 20, 30};  
    int* ptr = arr;   // Points to arr[0]  
    ptr++;                // Now points to arr[1]  
    cout << *ptr; 
    return 0; 
} 
```
The output will be:
```
20
```
Here, `ptr++` increases the pointer by the size of the data type (`int`) (i.e., `4 bytes`), not just by one byte. Consider the example below to understand it better:

```c++
#include<iostream>

int main() {
    int a = 10;
    int* ptrVar = &a;

    std::cout << ptrVar << std::endl;
    std::cout << (ptrVar+1) << std::endl;

    return 0;
}
```
Here, in the first statement, it'll print a memory address and in the second statement, it'll add `4` bytes to the initial memory address. This is because the type of the pointer variable is integer and integer takes `4` bytes of memory. Therefore, the next memory address will be at `(previous memory address + 4 bytes)`. ***We'll get a random value if we try to dereference `*(p+1)` because we don't know what happened with that memory address. Maybe some other programs stored that value at some points in that memory address.***

> You can check the size of a data type using `sizeof` operator. For example - `sizeof(int)`.

### Decrement (--)
The decrement operator (`--`) moves the pointer to the previous memory location:

```c++
#include <iostream> 
using namespace std; 

int main() { 
    int arr[] = {10, 20, 30};  
    int* ptr = &arr[2]; // Points to arr[2]  
    ptr--;                         // Now points to arr[1]  
    cout << *ptr; 
    return 0; 
}
```
The output will be:
```
20
```

### Addition
The addition operator (`+`) can be used with pointers to move them forward in memory. When we add an integer `n` to a pointer, the pointer advances by `n` elements, not raw bytes. The actual number of bytes skipped depends on the size of the type the pointer refers to:

```c++
#include <iostream> 
using namespace std; 

int main() { 
    int arr[] = {10, 20, 30, 40};  
    int* ptr = arr;  
    cout << *(ptr + 2); 
    return 0; 
} 
```
The output will be:
```
30
```
> **NOTE:** If we increment a pointer beyond the valid range of the array (or object) it points to, we invoke undefined behavior. This can lead to memory corruption, program crashes, or unpredictable results. Always ensure your pointer arithmetic stays within the allocated bounds.

### Subtraction
The subtraction operator (`-`) can be used with pointers to move them backward in memory. When we subtract an integer `n` from a pointer, the pointer moves back by `n` elements:

```c++
#include <iostream> 
using namespace std; 

int main() { 
    int arr[] = {10, 20, 30, 40};  
    int* ptr = &arr[3];        // Points to arr[3] 
    cout << *(ptr - 2);       // Moves 2 elements back → arr[1] 
    return 0; 
} 
```

The output will be:
```
20
```
Just like the addition operator, subtracting too far can move the pointer outside the valid range of the array (or object) it belongs to, invoking undefined behavior.

### Pointer Difference
We can subtract one pointer from another (as long as they both point to the same array). The result is the number of elements between them, not the number of bytes:

```c++
#include <iostream> 
using namespace std; 

int main() { 
    int arr[] = {10, 20, 30, 40};  
    int* p1 = &arr[0];  
    int* p2 = &arr[3];  
    cout << (p2 - p1); 
    return 0; 
} 
```

The output will be:
```
3
```
The output indicates that there are `3` elements between the pointers `p1` and `p2`.

### Pointers and Functions
One of the most powerful applications of pointers in C++ is their use with functions. Normally, when we pass variables to a function, they are passed by value, meaning the function receives a copy of the variable. Changes inside the function don’t affect the original variable.

Pointers solve this limitation by enabling pass-by-reference. Unlike C++’s built-in pass-by-reference (`&`), pointers allow more flexibility—for example, passing `nullptr`, handling arrays dynamically, or modifying multiple variables through a single pointer. They also make it clear that a function may change the original data, improving both clarity and efficiency.

### Passing Pointer to Function

```c++
#include <iostream> 
using namespace std; 
 
void updateValue(int* p) { 
    *p = *p + 10; // Modifies the original variable 
} 
 
int main() { 
    int num = 5; 
    updateValue(&num); // Pass address of num 
    cout << "Updated value: " << num << endl; 
    return 0; 
} 
```

The output will be:
```
Updated value: 15 
```
Here, `updateValue()` receives the address of `num`, and by dereferencing the pointer (`*p`), it changes the original variable.

### Pointers to Functions
C++ also allows pointers to point to functions themselves. This lets us store the address of a function in a pointer and call it indirectly, which is useful for callback mechanisms or passing functions as arguments:

```c++
#include <iostream> 
using namespace std; 
 
void greet() { 
    cout << "Hello from a function!" << endl; 
} 
 
int main() { 
    void (*funcPtr)() = &greet; // Pointer to function 
    funcPtr(); // Call function via pointer 
    return 0; 
} 
```
In this example, (`*funcPtr`) declares `funcPtr` as a pointer to a function. The parentheses are necessary as without them, C++ would interpret it as a function returning `void` instead of a pointer. The `()` after `(*funcPtr)` indicates that the function takes no parameters.

As a whole, the `void (*funcPtr)() = &greet;` line assigns the address of the `greet` function to the `(*funcPtr)` pointer. The `&` is optional, as the function name alone also represents its address.

The output will be:
```
Hello from a function! 
```

Pointer can be used to write `callback` function. You can learn more about function pointer [here]().

## Advantages and Disadvantages of Pointers
### Advantages
- **Efficient memory management:** Pointers allow us to allocate and deallocate memory dynamically, which is essential for managing resources effectively.

- **Faster execution:** By giving direct access to memory addresses, pointers reduce overhead and make programs more efficient.

- **Support for complex data structures:** Pointers are the foundation for implementing linked lists, trees, graphs, and other advanced data structures.

- **Pass by reference:** Pointers make it possible to modify variables directly inside functions without returning values.

### Disadvantages:
- **Risk of memory leaks:** If dynamically allocated memory is not properly freed, it can lead to wasted memory.

- **Dangling and wild pointers:** Mismanagement of pointers (e.g., using freed or uninitialized pointers) can cause crashes or undefined behavior.

- **Increased complexity:** Pointer syntax and logic can be difficult for beginners to understand and debug.

- **Security issues:** Improper use of pointers can expose vulnerabilities like buffer overflows, leading to potential exploits.

## Best Practices for Using C++ Pointers
Applying these best practices will ensure effective usage of C++ pointers:

- **Always initialize pointers:** Set pointers to a valid address or nullptr to avoid wild pointers.

- **Limit pointer arithmetic:** Keep arithmetic minimal and always stay within array bounds to avoid undefined behavior.

- **Validate pointers before use:** Check for nullptr before dereferencing to ensure safe access.

- **Use references when possible:** For function parameters, prefer references over raw pointers if ownership transfer is not required.

## References:
- CodeAcademy and GeeksForGeeks