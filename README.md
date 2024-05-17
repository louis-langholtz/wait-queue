# Wait Queue, a Header-Only C++ 20 MPMC Thread-Safe Queue

#### Unit Test and Documentation Generation Workflow Status

![GH Actions Workflow Status](https://img.shields.io/github/actions/workflow/status/connectivecpp/wait-queue/build_run_unit_test_cmake.yml?branch=main&label=GH%20Actions%20build,%20unit%20tests%20on%20main)

![GH Actions Workflow Status](https://img.shields.io/github/actions/workflow/status/connectivecpp/wait-queue/build_run_unit_test_cmake.yml?branch=develop&label=GH%20Actions%20build,%20unit%20tests%20on%20develop)

![GH Actions Workflow Status](https://img.shields.io/github/actions/workflow/status/connectivecpp/wait-queue/gen_docs.yml?branch=main&label=GH%20Actions%20generate%20docs)

## Overview

`wait_queue` is a multi-reader, multi-writer FIFO queue (often called MPMC for multiple producer / multiple consumer) for transferring data between threads. It is templatized on the type of data passed through the queue as well as the queue container type. Data is passed with value semantics, either by copying or by moving (as opposed to a queue that transfers data by pointer or reference). The wait queue has both wait and no-wait pop semantics. A fixed size container (e.g. a `ring_span`) can be used, eliminating any and all dynamic memory management (useful in embedded or deterministic environments). Similarly, a circular buffer that only allocates on construction can be used, which eliminates dynamic memory management when pushing or popping values on or off the queue.

A graceful shutdown can be performed using the `request_stop` method (modeled on the C++ 20 `request_stop` from `std::stop_source`). This allows waiting reader threads to be notified for shutdown. Alternatively a `std::stop_token` can be passed in to the `wait_queue` constructor, allowing shutdown from outside of the `wait_queue` object.

`wait_queue` is inspired by code from Anthony Williams' Concurrency in Action book, although heavily modified.

Additional documentation can be found dkfjlkjdlkjklj

## Generated Documentation

The generated Doxygen documentation for `wait_queue` is [here](https://connectivecpp.github.io/wait-queue/).

## Dependencies

The `wait-queue` header file does not have any third-party dependencies. It uses C++ standard library headers only. The unit test code does have dependencies as noted below.

## C++ Standard

`wait-queue`  uses C++ 20 features, including `std::stop_token`, `std::stop_source`, `std::condition_variable_any`, `std::scoped_lock`, and `concepts` / `requires`.

## Supported Compilers

Continuous integration workflows build and unit test on g++ (through Ubuntu) and MSVC (through Windows). Note that clang support for C++ 20 `std::jthread` and `std::stop_token` is still incomplete as of May 2024.

## Unit Test Dependencies

The unit test code uses [Catch2](https://github.com/catchorg/Catch2). If the `WAIT_QUEUE_BUILD_TESTS` flag is provided to Cmake (see commands below) the Cmake configure / generate will download the Catch2 library as appropriate using the [CPM.cmake](https://github.com/cpm-cmake/CPM.cmake) dependency manager. If Catch2 (v3 or greater) is already installed using a different package manager (such as Conan or vcpkg), the `CPM_USE_LOCAL_PACKAGES` variable can be set which results in `find_package` being attempted. Note that v3 (or later) of Catch2 is required.

The unit test uses two third-party libraries (each is a single header-only file):

- Martin Moene's [ring-span-lite](https://github.com/martinmoene/ring-span-lite)
- Justas Masiulis' [circular_buffer](https://github.com/JustasMasiulis/circular_buffer)

Specific versions and / or branches for the dependencies are in `test/CMakeLists.txt`.

## Build and Run Unit Tests

To build and run the unit test program:

First clone the `wait-queue` repository, then create a build directory in parallel to the `wait-queue` directory (this is called "out of source" builds, which is recommended), then `cd` (change directory) into the build directory. The CMake commands:

```
cmake -D WAIT_QUEUE_BUILD_TESTS:BOOL=ON -D JM_CIRCULAR_BUFFER_BUILD_TESTS:BOOL=OFF ../wait-queue

cmake --build .

ctest
```

For additional test output, run the unit test individually, for example:

```
test/wait_queue_test -s
```

The example can be built by adding `-D WAIT_QUEUE_BUILD_EXAMPLES:BOOL=ON` to the CMake configure / generate step.

