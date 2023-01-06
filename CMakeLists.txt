cmake_minimum_required(VERSION 3.24)

project(clangformat)

set(CLANGFORMAT_VER 15 CACHE INTERNAL
        "clang-format-? version to be used at least, compatible with .clang-format file"
        FORCE)

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
