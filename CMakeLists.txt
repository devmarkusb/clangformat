cmake_minimum_required(VERSION 3.24)

project(clangformat)

set(CLANGFORMAT_VER 15 CACHE INTERNAL
        "clang-format-? version to be used at least, compatible with .clang-format file"
        FORCE)
message("CLANGFORMAT_VER: ${CLANGFORMAT_VER}")

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

find_program(CLANG_FORMAT "clang-format-${CLANGFORMAT_VER}")

macro(make_clang_format_project_target cxx_dirs)
    if (CLANG_FORMAT)
        unset(all_cxx_sources)
        foreach (cxx_dir ${cxx_dirs})
            glob_recurse_append_cxx_sources(all_cxx_sources ${cxx_dir})
        endforeach ()
        add_custom_target(
                clang-format-${PROJECT_NAME}
                COMMAND ${CLANG_FORMAT}
                -i
                -style=file
                ${all_cxx_sources}
        )
    endif ()
endmacro()
