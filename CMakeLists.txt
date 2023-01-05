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
