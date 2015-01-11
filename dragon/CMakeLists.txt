cmake_minimum_required(VERSION 3.1)
project(dragon C)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=c99")

set(dragon_sources ast.c main.c anal.c symbol.c util.c token.c)

include_directories(${CMAKE_CURRENT_BINARY_DIR})
include_directories(${CMAKE_CURRENT_SOURCE_DIR})

file(GLOB dragon_headers *.h)

find_package(BISON)
bison_target(parser parser.y ${CMAKE_CURRENT_BINARY_DIR}/parser.tab.c
    COMPILE_FLAGS "--report-file=${CMAKE_CURRENT_BINARY_DIR}/parser.output")

find_package(FLEX)
flex_target(lexer lexer.l ${CMAKE_CURRENT_BINARY_DIR}/lexer.c
    COMPILE_FLAGS "-o lexer.c --header-file=${CMAKE_CURRENT_BINARY_DIR}/lexer.h")

add_flex_bison_dependency(lexer parser)


add_executable(dragon
    ${dragon_sources}
    ${dragon_headers}
    ${BISON_parser_OUTPUTS}
    ${FLEX_lexer_OUTPUTS}
)

add_custom_target(check
    COMMAND ${CMAKE_CURRENT_SOURCE_DIR}/check.sh
    ${CMAKE_CURRENT_SOURCE_DIR}
    ${CMAKE_CURRENT_BINARY_DIR}/dragon all
    DEPENDS ${CMAKE_CURRENT_BINARY_DIR}/dragon
    COMMENT "Running tests"
)