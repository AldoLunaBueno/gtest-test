# Probando **gtest**

This tutorial shows you how to set up GoogleTest (gtest) and GNU Make from scratch to test your C++ code. We will build a simple math application, write a test suite, and automate everything using a Makefile.

---

## 1. Project Structure

Organize your workspace to separate the core application logic from the testing framework:

```text
my_project/
│
├── src/
│   ├── math_utils.cpp
│   └── math_utils.h
│
├── tests/
│   └── test_main.cpp
│
└── Makefile
```

---

## 2. Write the C++ Code to Test

Create a header file and an implementation file containing the functions you want to test.

### `src/math_utils.h`

```cpp
#ifndef MATH_UTILS_H
#define MATH_UTILS_H

int add(int a, int b);

#endif
```

### `src/math_utils.cpp`

```cpp
#include "math_utils.h"

int add(int a, int b) {
    return a + b;
}
```

---

## 3. Write the GoogleTest Suite

GoogleTest requires an entry point that initializes the framework and executes the assertions. Write a file containing your test cases and a main function.

### `tests/test_main.cpp`

```cpp
#include < gtest/gtest.h >
#include "../src/math_utils.h"

// Define a test case using the TEST macro
TEST(MathUtilsTest, HandlesPositiveSum) {
    EXPECT_EQ(add(2, 3), 5); // Non-fatal assertion
}

TEST(MathUtilsTest, HandlesNegativeSum) {
    ASSERT_EQ(add(-1, -1), -2); // Fatal assertion
}

// Entry point for running the test runner
int main(int argc, char **argv) {
    ::testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
}
```

---

## 4. Create the Makefile

To compile your tests, you must link against the gtest library and its system dependencies (like pthread).

> **Note:** Make sure you have GoogleTest installed on your machine (`sudo apt install libgtest-dev` on Debian/Ubuntu, or built from source).

### `Makefile`

```makefile
# Compiler and flags
CXX = g++
CXXFLAGS = -std=c++17 -Wall -Isrc

# Linker flags for GoogleTest
LDFLAGS = -lgtest -lpthread

# Target binary name
TARGET = run_tests

# Source files
SRCS = src/math_utils.cpp tests/test_main.cpp
OBJS = $(SRCS:.cpp=.o)

# Default rule to build and run tests
all: $(TARGET)
	@echo "Running tests..."
	./$(TARGET)

# Link the executable
$(TARGET): $(OBJS)
	$(CXX) $(CXXFLAGS) -o $(TARGET) $(OBJS) $(LDFLAGS)

# Compile source files into object files
%.o: %.cpp
	$(CXX) $(CXXFLAGS) -c $< -o $@

# Clean build artifacts
clean:
	rm -f src/*.o tests/*.o $(TARGET)

.PHONY: all clean
```

---

## 5. Compile and Execute

Open your terminal in the `my_project/` root directory and perform the following commands:

1. **Build and automatically run the tests:**
   ```bash
   make
   ```

2. **Review output:** GoogleTest will display text output confirming the results of your individual assertions:
   ```text
   [==========] Running 2 tests from 1 test suite.
   [----------] Global test environment set-up.
   [----------] 2 tests from MathUtilsTest
   [ RUN      ] MathUtilsTest.HandlesPositiveSum
   [       OK ] MathUtilsTest.HandlesPositiveSum (0 ms)
   [ RUN      ] MathUtilsTest.HandlesNegativeSum
   [       OK ] MathUtilsTest.HandlesNegativeSum (0 ms)
   [----------] 2 tests from MathUtilsTest (0 ms total)

   [==========] 2 tests from 1 test suite ran. (0 ms total)
   [  PASSED  ] 2 tests.
   ```

3. **Clean build files:** Remove intermediate binary artifacts to reset your project tree:
   ```bash
   make clean
   ```
