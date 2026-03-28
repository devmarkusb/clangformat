# clangformat

This repo is a single source of truth for **`.clang-format`** configs. Use it by syncing the file into your project (e.g. via a small script) and running **clang-format** via [pre-commit](https://pre-commit.com) (e.g. [mirrors-clang-format](https://github.com/pre-commit/mirrors-clang-format)); no CMake or custom shell hooks in your tree.

## Layout

```
clangformat/
  .clang-format              # optional: default/latest (e.g. copy of configs/v22/.clang-format)
  configs/
    v14/
      .clang-format          # for clang-format 14.x
    v22/
      .clang-format          # for clang-format 22.x
```

- **Versioned configs:** `configs/v<major>/` holds a `.clang-format` valid for that clang-format major. Consumers fetch the one that matches their pre-commit `rev` (or pick explicitly).
- **Root `.clang-format`:** Optional default for repos that don’t use the sync script or want “latest”.

## How consumers use it

Projects that use this repo typically add a small script (e.g. `devenv/sync-clang-format.sh`) that:

1. Picks a version (script arg, env `CLANGFORMAT_VERSION`, or from `.pre-commit-config.yaml` mirrors-clang-format `rev`).
2. Fetches `configs/v<major>/.clang-format` from this repo (raw GitHub URL), or the root `.clang-format` if that path doesn’t exist.
3. Writes it to the project root `.clang-format`.

Example (run from project repo root):

```bash
./devenv/sync-clang-format.sh [VERSION]
```

- **VERSION** (optional): clang-format major (e.g. `14`, `22`). Default: from pre-commit config or `22`.
- Override source: env `CLANGFORMAT_REPO`, `CLANGFORMAT_BRANCH`, or `CLANGFORMAT_VERSION`.

Formatting is then enforced by pre-commit (e.g. `pre-commit run --all-files`); no need for CMake format targets or custom hooks from this repo.

## Maintaining versioned configs

- **v22** (and newer): Full option set (e.g. `BreakBeforeConceptDeclarations`, `IndentRequiresClause`, `StatementAttributeLikeMacros`, `SeparateDefinitionBlocks`, `BitFieldColonSpacing`, `Standard: Latest`).
- **v14**: Omit options that don’t exist in 14; keep a compatible subset to avoid unknown-key noise.

When adding a new clang-format major (e.g. v23), add `configs/v23/.clang-format` (copy from v22 and adjust if needed). Keep the root `.clang-format` as a default for repos that don’t use the sync script or want “latest”.

---

# (deprecated, without pre-commit) clangformat

The following way of using this repo is **deprecated**. Prefer the flow above: sync `.clang-format` into your repo and use pre-commit for formatting.

**Previously**, this repo was used via **CMake FetchContent** and a **shell script** (`clangformat.sh`) to copy the config and add format targets (e.g. “format-all”). That avoided pre-commit and relied on a fixed `MB_CLANGFORMAT_VER` and custom hooks in the consumer.

- **Why deprecated:** Pre-commit gives a single, standard way to run clang-format (and other linters) with pinned versions; no need for per-repo CMake format targets or custom scripts. Syncing only the `.clang-format` file keeps this repo as “config only” and lets each project choose how to run clang-format (almost always via pre-commit).
- **If you still rely on the old integration:** The root `.clang-format` and any files you already fetch (e.g. `clangformat.sh`, CMake snippets) may remain available for a while, but will not be improved. Prefer migrating to the sync script + pre-commit flow described above.

Continuing with the deprecated readme part... 

---

Intended to be the single point of truth of the/a .clang-format file (C++ code auto formatting configuration).
Mainly handling storage and versioning here. But there is also useful
tool support for creating format-all CMake targets or applying formatting
during pre-commit git hooks. See Usage section for that.

## Requirements

Preferred clang-format version: see MB_CLANGFORMAT_VER in CMakeLists.txt,
which you can configure to be a higher version also of course.
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
#!/usr/bin/env bash

script_dir="$( cd "$( dirname "${BASH_SOURCE[0]}" )" >/dev/null 2>&1 && pwd )"

$script_dir/../clangformat.sh
```
to your repo's local .githooks/pre-commit git hook file, to apply formatting on each commit
automatically.

Additionally, you can include the following lines in your project's
`CMakeLists.txt` files, which will add a clang-format target per project.
When building the target, every file from the list of subdirs (recursively) will
be formatted.
```
set(cxx_dirs "apps;include;libs;sdks;source;src;test")

add_clang_format_project_target("${cxx_dirs}")
```
Be careful to really pass a semicolon-separated list of subdir strings.
In the example `add_clang_format_project_target(${cxx_dirs})` without
quotes wouldn't work.

## templates?

A directory for storing examples, other versions, or certain default styles,
just for comparison.
