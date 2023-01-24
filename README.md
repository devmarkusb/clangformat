# clangformat

Intended to be the single point of truth of the/a .clang-format file (C++ code auto formatting configuration).
Mainly handling storage and versioning here. But there is also useful
tool support for creating format-all CMake targets or applying formatting
during pre-commit git hooks. See Usage section for that.

## Requirements

Preferred clang-format version: see MB_CLANGFORMAT_VER in CMakeLists.txt.
Although plain clang-format is used if the preferred version is not
available.

This needs to be updated when using features in `.clang-format`
not supported by lower versions.

## Usage

### Example cmake inclusion in your repo

```
cmake_minimum_required(VERSION 3.14)

include(FetchContent)

FetchContent_Declare(mb-clangformat
        GIT_REPOSITORY "https://github.com/devmarkusb/clangformat"
        GIT_TAG origin/HEAD
        GIT_SHALLOW ON
        )

FetchContent_MakeAvailable(mb-clangformat)

# of course the copied files are not intended to be edited
file(COPY ${mb-clangformat_SOURCE_DIR}/.clang-format DESTINATION ${PROJECT_SOURCE_DIR}/)
file(COPY ${mb-clangformat_SOURCE_DIR}/clangformat.sh DESTINATION ${PROJECT_SOURCE_DIR}/
        FILE_PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE)
```

Then you could add
```
script_dir="$( cd "$( dirname "${BASH_SOURCE[0]}" )" >/dev/null 2>&1 && pwd )"

$script_dir/../clangformat.sh
```
to your pre-commit git hook file, in order to apply formatting on each commit
automatically.

Additionally, you can include the following lines in your project's
`CMakeLists.txt` files, which will add a clang-format target per project.
When building the target every file from list of subdirs (recursively) will
be formatted.
```
set(cxx_dirs "apps;include;libs;sdks;source;src;test")

make_clang_format_project_target("${cxx_dirs}")
```
Be careful to really pass a semicolon separated list of subdir strings.
In the example `make_clang_format_project_target(${cxx_dirs})` without
quotes wouldn't work.

## templates?

A directory for storing examples, other versions, or certain default styles,
just for comparison.
