# clangformat

Intended to be the single point of truth of the .clang-format file (C++ code auto formatting configuration).
Just handling storage and versioning here.

## Requirements

clang-format version: 10

## Usage

### Example cmake inclusion in your repo

```
cmake_minimum_required(VERSION 3.14)

include(FetchContent)

FetchContent_Declare(clangformat
GIT_REPOSITORY "https://github.com/MarkusBorris/clangformat"
GIT_TAG HEAD
GIT_SHALLOW ON
)

FetchContent_MakeAvailable(clangformat)

# of course the copied file is not intended to be edited
file(COPY ${clangformat_SOURCE_DIR}/.clang-format DESTINATION ${CMAKE_SOURCE_DIR}/)
```

How to actually apply the formatting is beyond the scope of this document.

## templates?

A directory for storing examples, other versions, or certain default styles,
just for comparison.