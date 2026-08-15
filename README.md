
# Perfect Programming Principles

These programming principles are written very strict and should be considered the absolute strictest standard when it comes to writing clean and safe code.
These principles should be considered, but not necessarily followed 100% of the time.

While these principles are written specifically for critical and safe C++ code, they can still be considered and applied to other languages and uses as well.

## Table of Contents
- [Variables](#variables)
- [Data Types](#data-types)
- [Functions](#functions)
- [Conditionals](#conditionals)
- [Control Flow](#control-flow)

---

## Discouraged Practices

### Variables

#### 1. Do Not Abbreviate

&emsp;Early programmers tended to name variables as single letters or would heavily abbreviate their variable names.

&emsp;`p, n, creat, strncpy, ...`

&emsp;Really the only variables you can get away with single letter variable names are very simple values (`a, b, c, x, y, z`) that are obvious, not important, or historically single letter variables like `i` in loops.

&emsp;`struct vector3 {int x, y, z;};` <-- These variable names are fine because it is obvious what they represent. \
&emsp;`int exampleFunction(int a);` <-- Parameter `a` is not ok here because it is not obvious what `a` represents.

&emsp;Early programmers heavily shortened their variable names because a lot of them were mathematicians and math loves single letter variables; additionally, screens in the early days of computing were only 80 characters wide and punch cards were also only 80 characters wide, but in today's day of age of wide screens and what seems like infinite memory, we do not need to shorten names.

#### 2. Use Descriptive Variable Names

&emsp;A variable name should be very descriptive as to what it is; there should be no ambiguity as to what a variable represents. 

&emsp;`int value;` <-- What does `value` keep track of? \
&emsp;`int number_of_vowels_in_string;` <-- This is a lot more clear.

---

### Data Types

#### 1. Data Types Should Be Descriptive

&emsp;There are countless examples where a variable name stores all information relating to the variable, including the name, unit, etc., while the data type is a primitive type:

&emsp;`int led_blink_time_period_seconds;`

Like in [&sect;Variables-2](#2-use-descriptive-variable-names), your data type should also be as descriptive as possible. i.e: 

&emsp;`int number_of_vowels_in_string;` <-- `int` works here, but could be better. \
&emsp;`std::size_t number_of_vowels_in_string;` <-- This is a slightly better data type, but could be improved.

&emsp;`using counter_t = std::size_t;` \
&emsp;`counter_t number_of_vowels_in_string;` <-- Clearly states that `number_of_vowels_in_string` is a counter (`counter_t`) and implies that it is meant to be iteratively incremented or some similar operation.

&emsp; Note: aliased types are not strongly typed in C++; for example, a function accepting an aliased `int` called `counter_t` will accept both a `counter_t` and an `int` as an argument (See [&sect;Types-5](#5-create-strong-typed-data-types-when-possible)).

#### 2. Declare Const by Default

&emsp;Every variable that is initialized should be defined as a `const`. If a variable is not defined as `const`, then it is assumed that the variable value will be edited during runtime. 

&emsp;`int variable_name = 1;` <-- Even if its "obvious" that this variable is not changed in the code, it can be assume that it is updated somewhere in the code that hasn't been seen yet. \
&emsp;`const int variable_name = 1;` <-- It is clear that this variable will not be changed during program execution.

#### 3. Avoid Floating-Point Data Types

&emsp;Floating-point values have issues with their precision and can lead to unexpected results.

&emsp;See the classic floating-point example of `0.1 + 0.2 != 0.3`, and see other examples [here](https://jvns.ca/blog/2023/01/13/examples-of-floating-point-problems/).

&emsp;If you find yourself using a floating-point value, think of ways that it can be converted to an integer. 99.99% of the time, if a floating-point value is used, it can be replaced with an integer. For example, look at real-world sensors; most real-world sensors have a minimum precision value expressed as an integer type. A processor temperature sensor can only read values of, lets say, 1/1000th of a degree Celsius of precision; therefore, the variable used to store the value should be an integer type of `millidegrees_celsius_t` or something similar. 

&emsp;`using millidegrees_celsius_t = uint32_t;`

#### 4. Avoid Unsafe Data Types

&emsp;One example of an unsafe data type in C++ are raw pointers. To avoid this issue, use smart pointers like `std::unique_ptr` and `std::shared_ptr` for every use of a pointer. Do not use raw pointers. 

#### 5. Create Strong Typed Data Types when Possible

&emsp;As stated in [&sect;Types-1](#1-types-should-be-descriptive) aliased data type declarations are not strongly typed, so:

&emsp;`using aliased_type = int;` \
&emsp;`aliased_type foo(aliased_type a, aliased_type b);`

&emsp;Here, `foo` will accept `int`'s for parameters and will allow the return value to be assigned to an `int` variable with no issue at all.

&emsp;`int x = foo(1, 2);` <-- Does not raise a compilation or runtime error.

&emsp;This issue with weak-aliased type aliasing allows two non-compatible types like `meters_t` and `seconds_t` to be used interchangeably if their underlying data types are the same (i.e. `uint32_t`).

&emsp;`using meters_t = uint32_t;` \
&emsp;`using seconds_t = uint32_t;`

&emsp;`meters_t fallDistance(seconds_t time);` <-- Returns the distance an object falls in free fall after how many seconds.

&emsp;`meters_t distance = 100;` \
&emsp;`seconds_t time = fallDistance(distance);` <-- Incorrect usage, but since `meters_t` and `seconds_t` types are aliased to to the same underlying type (`uint32_t`), there is no compilation error or warning.

&emsp;Here we should define a new strong typed data type. It is recommended to either creating your own strong typing framework or use an external library like [strong_type](https://github.com/rollbear/strong_type) or [type_safe](https://github.com/foonathan/type_safe).

---

### Functions

#### 1. Use Descriptive Function Names

&emsp;Similar to [&sect;Variables-2](#2-use-descriptive-variable-names) and [&sect;Data Types-1](#1-data-types-should-be-descriptive), it should be really obvious what a function does solely from the name, its return type, and the arguments passed to it.

&emsp;`bool foo(const std::string& a);` <-- No clue what this function does. \
&emsp;`bool doesFileExist(const std::string& file_path);` <-- More obvious

#### 2. Limit Function Scope of Operation
&emsp;A function should be a single operation, it should not do more than one "thing" at a time. If you are not able to name the function and describe what the function does, then your function is too long and should be split into multiple independent functions.

&emsp;`void createFileAndWriteToIt(const std::string& file_path, const std::string& contents);` <-- Should be two separate functions

&emsp;`void createFile(const std::string& file_path);` <-- Separate functions\
&emsp;`void writeToFile(const std::string& contents);` <-- ^

&emsp;Another suggestion I've heard is if you cannot print your entire function on a standard piece of printer paper, in a reasonable font size, then your function is too long.

#### 3. Check Every Argument

&emsp;Every single argument to a function should be checked at the start of a function.

&emsp;For example, if a pointer is passed to a function it should be checked if it is a `nullptr` before any operations are done on it. If a value for a function takes an index of a list object to return the value at that index, it should be checked to make sure the passed index is withing the list's bounds.

`if (index < 0 || list.size() <= index) throw std::out_of_range("Index out of range.");`

#### 4. Check Every Return Value

&emsp;If a function returns a value, that means that value is important, and should be checked.

&emsp;A function either returns nothing (`void`), some status, or a useful object/value. Any non-`void` function's return value should be checked, or if really not important (like return value of a `print` or `logging` function who's return value doesn't really matter) it should be casted to void using `(void)` to show that the programmer has at least checked the functions return type and explicitly ignores it.

&emsp;To guarantee a function's return value is not ignored, the `[[nodiscard]]` tag should be applied to the function; this ensures that a return value is either captured or casted to `void`.

&emsp;`[[nodiscard]] int importantReturnValue();` <-- Ensures return value of this function is captured or casted to void.

&emsp;`int x = importantReturnValue();` <-- Ok \
&emsp;`(void) importantReturnValue();` <-- Ok, but not recommended if return value is important. \
&emsp;`importantReturnValue();` <-- Compiler warning.

&emsp;If a data type should not be ignored every time it is used, like a status value or error code, then a new `struct` data type can be defined with `[[nodiscard]]` in the declaration and the `[[nodiscard]]` clause will be enforced everywhere the struct is returned.

---

### Conditionals

#### 1. Do Not Nest Conditions

&emsp;Nesting many conditional statements is a quick was to make your code unreadable. You should instead try to use early return statements with a flipped conditional evaluation.

```cpp
if (condition1) {
    if (condition2) {
        if (condition3) {
            doSomething();
        }
    }
}
```
&emsp;Try instead:

```cpp
if (!condition1) {
    return;
}

if (!condition2) {
    return;
}

if (!condition3) {
    return;
}

doSomething();
```

&emsp;Unnesting the conditional statements make the code more readable and allows you to perform clean up operations for each failed condition and return different values.

#### 2. Do Not Evaluate Conditions Inside of Conditional Statement

&emsp;A complex conditional evaluating in the conditional statement make the code very unreadable. 

```cpp
if ((condition1 || condition2) && condition3 && condition4) {
    doSomething();
}
```

&emsp;Make every condition evaluation be assigned to a variable and have that variable be evaluated in the conditional statement.

```cpp
const bool condition12 = condition1 || condition2;
const bool condition1234 = condition12 && condition3 && condition4;

if (condition1234) {
    doSomething();
}
```

&emsp;This makes complicated conditions more readable and allows you to easily debug and/or log the evaluated condition.

---

### Control Flow

#### 1. Do Not Use Complex Control Flow Statements Like `goto`

&emsp;Jump statements like `goto` can cause complex control flow issues, and it is recommended to not use `goto` as there are other, better solutions out there. 

#### 2. Avoid Recursion

&emsp;Recursion is a powerful tool, but can be dangerous and unpredictable. Most recursive operations can be rewritten to use a loop instead. 

&emsp;Be careful with both direct and indirect recursion, they are both to be avoided.

&emsp;Direct recursion example (`foo()` -> `foo()` -> ...):

```cpp
void foo() {
    foo();
}
```

&emsp;Indirect recursion example (`foo()` -> `bar()` -> `foo()` -> ...):

```cpp
void foo() {
    bar();
}

void bar() {
    foo();
}
```

#### 3. Apply Upper Bounds to Loops

&emsp;Unless a loop is intentionally continuously looped through, like an update loop, there should be a strict upper bounds to the loop to prevent a runaway inescapable loop.

```cpp
template <typename T>
struct linked_list<T> {
    T value;
    linked_list<T>* next;
}

linked_list<int> list1 = {1, nullptr}
linked_list<int> list2 = {2, &list1}
list1.next = &list2;

```

&emsp;Runaway loop example:

```cpp
template <typename T>
linked_list<T>* findEnd(linked_list<T>* list) {
    while (list.next != nullptr) {
        list = list->next;
    }
    return list;
}
```

&emsp;Bounded loop example:

```cpp
template <typename T>
linked_list<T>* findEnd(linked_list<T>* list) {
    const static int MAX_ITERATIONS = 65536;

    int iterations = 0;
    while (list.next != nullptr) {
        if (iterations >= MAX_ITERATIONS) {
            return nullptr;
        }

        list = list->next;
        iterations++;
    }
    return list;
}
```

&emsp;The first example code will run forever and will never return, crashing the program; however, the second example code will stop after 65,536 loop iterations (a large but relatively small number), and will return a `nullptr`. Crucially, the second example does not run for an infinite amount of time.

---
