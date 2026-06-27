---
layout: post
title: Function Pointer in C++
date: 2026-06-027 00:57:00-0400
description: Learn about function pointer and it's application
tags: C++
categories: tutorial
giscus_comments: true
related_posts: false
---

# What is Function Pointer?
In C++, pointers store memory address. While they are commonly used to store memory address of variables or objects, they can also store the address of a function. A function pointer allows a program to call a function indirectly or pass a function as an argument, enabling flexible and dynamic behavior. 

Consider the example below to find the address of a function:

```c++
#include<iostream>

ing getNumber(int num) {
    return num;
}

int main() {
    std::cout << getNumber << std::endl;
    return 0;
}
```
Note that the function is not called here, we just put the function name without `()`.

So, a function pointer is a pointer variable that stores the address of a function with specific return type and parameter list.

## How to Create a Function Pointer and Invoke a Function Using Function Pointer?
Consider the example below where we create the `add` function. It takes two integers and return the sum of the integers. To create a function pointer of that function we need to follow the synax below:

```
return_type(*functionPtr)(parameter_type);
```
```c++
#include<iostream>

int add(int num1, int num2) {
    return num1 + num2;
}

int main() {
    // Creating a pointer to the add function
    int(*addFuncPtr)(int, int) = add;

    // Invoke using the function pointer
    std::cout << addFuncPtr(2, 3) << std::endl;

    // Invoke the function directly
    std::cout << add(2, 3) << std::endl;

    return 0;
}
```

## Applications of Function Pointer:
### Passing functions as argument (Callback Function)
If you're familiar with Python or Javascript, you may know that you can pass a function as an argument of another function. That's one of reasons why functions are called first class citizen or object in Python. We can implement that using function pointer in C++. Consider the example below where we want to write a sort function. Depending on the function we pass as argument to the sort function, it sorts a list of number either in ascending or descending order.

```c++
#include<iostream>
#include<vector>

/*
* Sort into ascending oder
*/
bool ascendingCompare(int a, int b) {
    return a < b;
}

/*
* Sort into descending order
*/
bool descendingCompare(int a, int b) {
    return a > b;
}

void sort(vector<int>& intVector, bool(*compareFuncPtr)(int, int)) {
    for(int startIdx = 0; startIdx < intVector.size(); startIdx++) {

        int bestIdx = startIdx;

        for(int currentIdx=startIdx+1, currentIdx < intVector.size(); currentIdx++) {
            if(compareFuncPtr(intVector[currentIdx], intVector[bestIdx])) {
                bestIdx = currentIdx;
            }
        }
        swap(intVector[startIdx], intVector[bestIdx]);
    }
}

/*
* Prints the numbers in a vector
*/
void printVector(vector<int>& intVector) {
    for(int i=0; i < intVector.size(); i++) {
        cout << intVector[i] << " ";
    }
}

int main() {
    vector<int> numbers = {12, 1, 5, 6, 10, 2};
    printVector(numbers);
    bool(*functionPtr)(int, int) = ascendingCompare;
    sort(numbers, ascendingCompare);
    printVector(numbers);

    return 0;
}
```
