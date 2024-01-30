cmake_minimum_required(VERSION 3.24)

project(clangformat)

set(MB_CLANGFORMAT_VER 15 CACHE STRING
    "clang-format-? version to be used; default is the preferred choice, compatible with .clang-format file")

find_program(CLANGFORMAT_EXE NAMES clang-format-${MB_CLANGFORMAT_VER} clang-format)
message("CLANGFORMAT_EXE: ${CLANGFORMAT_EXE}")

configure_file(
    ${CMAKE_CURRENT_LIST_DIR}/clangformat.sh.in
    ${CMAKE_CURRENT_LIST_DIR}/clangformat.sh
    @ONLY
)

macro(glob_recurse_append_cxx_sources cxx_sources dir)
    file(GLOB_RECURSE
            s
            ${dir}/*.[chi]pp ${dir}/*.[chi] ${dir}/*.cu
            )
    list(APPEND ${cxx_sources} ${s})
endmacro()

# See README.md on usage.
macro(add_clang_format_project_target cxx_dirs)
    if (CLANGFORMAT_EXE)
        unset(all_cxx_sources)
        foreach (cxx_dir ${cxx_dirs})
            glob_recurse_append_cxx_sources(all_cxx_sources ${cxx_dir})
        endforeach ()

        add_custom_target(
                clang-format-${PROJECT_NAME}
                COMMAND ${CLANGFORMAT_EXE}
                -i
                -style=file
                ${all_cxx_sources}
        )
        unset(all_cxx_sources)
    endif ()
endmacro()
