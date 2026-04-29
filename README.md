# polyguns-engine

A lightweight game engine built in C++ with first person shooters in mind.

## Prerequisites

- A C++20 compiler (MSVC 2022, Clang 14+, or GCC 11+)
- [CMake](https://cmake.org/) 3.20 or newer
- [Python 3](https://www.python.org/) with the `jinja2` package (used by GLAD 2
  to generate the OpenGL loader at configure time)
  ```sh
  pip install jinja2
  ```
- Git (used by CMake `FetchContent` to pull dependencies)
- OpenGL 3.3 capabilities

## Important commands

### How to build
The tasks should be present should you choose to work with VSCode (recommended for this repo). Here's the way to do it in CLI:


### How to test


## Third-party dependencies

These are pulled automatically by CMake `FetchContent` into `third_party/` the
first time you configure the project; the downloaded trees are gitignored.

| Library | Purpose                           | Version |
|---------|-----------------------------------|---------|
| GLFW    | Windowing, input, GL context      | 3.4     |
| GLAD 2  | OpenGL function loader (3.3 core) | 2.0.6   |

## Layout

```
polyguns-engine/
  include/engine/   public engine headers
  src/              engine source code
  tests/            unit tests
  third_party/      dependencies go here
```
