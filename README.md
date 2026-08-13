
# Perfect Programming Principles

These programming principles are written very strict and should be considered the absolute strictest standard when it comes to writing clean and safe code.
These principles should be considered, but not necessarily followed 100% of the time.

While these principles are written for specifically critical and safe C++ code, they can still be considered and applied to other languages and uses as well.

---

## Discouraged Practices


### Naming
> "There are only two hard things in Computer Science: cache invalidation and naming things" - Phil Karlton

&emsp;Naming is one of the most difficult things in programming, but following some simple rules makes it easier.

#### 1. Do Not Abbreviate
&emsp;Early programmers tended to name variables as single letters or would heavily abbreviate their variable names.

&emsp;```p, n, creat, strncpy, ...``` 

&emsp;Really the only variables you can get away with single letter variable names are very simple values (`a, b, c, x, y, z`) that are obvious or not important or historically single letter variables like `i` in loops.

&emsp;Early programmers heavily shortened their variable names because a lot of them were mathematicians and math loves single letter variables; additionally, screens in the early days of computing were only 80 characters wide and punch cards were also only 80 characters wide, but in today's day of age with 4K resolution wide screens and with very large amounts of memory, we do not need to shorten names.

#### 2. Be Descriptive in Your Name
&emsp;A variable name should be very descriptive as to what it is; there should be no ambiguity as to what a variable represents. 

&emsp;```int counter = 0``` <-- What does `counter` keep track of? \
&emsp;```int number_of_vowels_in_string = 0``` <-- This is a lot more clear.

---

### Types

#### 1. Type Names Should Be Descriptive
&emsp;I see a lot of variables where the type and the name of the variable are seen as two separate "ideas". \
Like in [&sect;Naming-2](#2-be-descriptive-in-your-name), your data type should also be as descriptive as possible. i.e: 

&emsp;```int number_of_vowels_in_string = 0``` <-- `int` is an ok data type here, but could be better. \
&emsp;```std::size_t number_of_vowels_in_string = 0``` <-- This is a slightly better data type.

&emsp;```using counter_t = std::size_t;``` \
&emsp;```counter_t number_of_vowels_in_string = 0``` <-- Clearly states that `number_of_vowels_in_string` is a counter (`counter_t`).

#### 2. Declare Const by Default
&emsp;Every variable that is initialized should be defined as a `const`. If a variable is not defined as `const`, then it is assumed that the variable value will be edited during runtime. 

&emsp;```const int variable_name = 1;``` <-- It is clear that this variable will not be changed during program execution.

#### 3. Avoid Unsafe Data Types
&emsp;In C++, raw pointers cause many issues such as memory leaks. Use smart pointers like `std::unique_ptr` and `std::shared_ptr` for every use of a pointer. Do not use raw pointers. 

&emsp;Smart pointers should also be used for any object that needs to be 'cleaned up' when it goes out of scope:

&emsp;```std::unique_ptr<FILE, decltype(&std::fclose)> file(std::fopen(file_path, "r"), &std::fclose);``` <-- C `FILE` example but still applicable.

---

### Functions

#### 1. Descriptive Function Names
&emsp;It should be really obvious what a function does solely from the name, its return type, and the arguments passed to it.

&emsp;```bool foobar(const std::string& a);``` <-- No clue what this function does. \
&emsp;```bool doesFileExist(const std::string& file_path);``` <-- More obvious

#### 2. Limit Function Scope of Operation
&emsp;A function should be a single operation, it should not do more than one "thing" at a time. If you are not able to name the function and describe what the function does, then your function is too long and should be split into multiple independent functions.

&emsp;```void createFileAndWriteToIt(const std::string& file_path, const std::string& contents);``` <-- Should be two separate functions

&emsp;```void createFile(const std::string& file_path);``` <-- Separate functions\
&emsp;```void writeToFile(const std::string& contents);``` <-- ^