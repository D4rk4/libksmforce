# libksmforce

`libksmforce` is a shared library designed to force all memory allocated by programs to be KSM-mergeable. It leverages the `prctl` system call to enable Kernel Same-page Merging (KSM) for the entire process.

## Table of Contents

- [Introduction](#introduction)
- [Requirements](#requirements)
- [Installation](#installation)
- [Usage](#usage)
- [Functions](#functions)
- [License](#license)

## Introduction

Kernel Same-page Merging (KSM) is a memory-saving de-duplication feature in the Linux kernel. It allows the kernel to share identical memory pages between different processes. This library intercepts memory allocation functions (`malloc`, `calloc`, `realloc`, `mmap`, `mremap`) and marks the allocated memory as mergeable using the `prctl` system call.

## Requirements

- Linux operating system with KSM support
- GCC (GNU Compiler Collection)
- Development libraries: `libdl`

## Installation

1. **Compile the library**:
    ```sh
    gcc -ggdb3 libksmforce.c -ldl -shared -fno-builtin-malloc -fPIC -o libksmforce.so
    ```

2. **Set the environment variable `LD_PRELOAD` to load the library before running a program**:
    ```sh
    LD_PRELOAD=./libksmforce.so ./program
    ```

## Usage

To use `libksmforce`, compile it as a shared library and preload it before running your target program. This ensures that all memory allocations done by the target program are marked as KSM-mergeable.

### Example

1. **Compile the library**:
    ```sh
    gcc -ggdb3 libksmforce.c -ldl -shared -fno-builtin-malloc -fPIC -o libksmforce.so
    ```

2. **Run your program with `LD_PRELOAD`**:
    ```sh
    LD_PRELOAD=./libksmforce.so ./your_program
    ```

## Functions

`libksmforce` intercepts and overrides the following standard library functions:

- `malloc(size_t size)`
- `free(void *ptr)`
- `calloc(size_t nmemb, size_t size)`
- `realloc(void *ptr, size_t size)`
- `mmap(void *addr, size_t length, int prot, int flags, int fd, off_t offset)`
- `munmap(void *addr, size_t length)`
- `mremap(void *old_address, size_t old_size, size_t new_size, int flags, ...)`

### Internal Functions

- **libksmforce_init**: Initializes the library by resolving the original memory allocation functions using `dlsym`.
- **ksmforce**: Enables KSM merging for the allocated memory using `prctl`.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.
