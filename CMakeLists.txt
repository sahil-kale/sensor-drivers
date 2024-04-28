# Project settings
cmake_minimum_required(VERSION 3.14)
set(This tests)
set(BINARY ${CMAKE_PROJECT_NAME})
project(${This} C CXX)

set(CMAKE_C_STANDARD 99)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)
set(CPPUTEST_HOME /usr/)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Og -g -Werror -Wextra -Wall")

# Include directories
include_directories(${PROJECT_SOURCE_DIR}/inc)
include_directories(${PROJECT_SOURCE_DIR}/test/inc)
include_directories(${PROJECT_SOURCE_DIR}/test/mocks)
include_directories(${PROJECT_SOURCE_DIR}/utils/host)

# Source directories
file(GLOB_RECURSE C_SRCS "${PROJECT_SOURCE_DIR}/src/*.c")
file(GLOB_RECURSE CPP_SRCS "${PROJECT_SOURCE_DIR}/src/*.cpp")
file(GLOB_RECURSE C_TEST_SRCS "${PROJECT_SOURCE_DIR}/test/*.c")
file(GLOB_RECURSE CPP_TEST_SRCS "${PROJECT_SOURCE_DIR}/test/*.cpp")
file(GLOB_RECURSE MOCK_SRCS "${PROJECT_SOURCE_DIR}/test/mocks/*.c" "${PROJECT_SOURCE_DIR}/test/mocks/*.cpp")

# Set compiler flags
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -g -pedantic -Werror")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -g -pedantic -Werror")
# Add a compiler definition -DUNITTEST to the compiler
add_definitions(-DUNIT_TEST)

add_library(MAIN_SOURCES ${C_SRCS} ${CPP_SRCS})
# Set pedantic and pedantic-errors flags for MAIN_SOURCES
target_compile_options(MAIN_SOURCES PRIVATE -pedantic -pedantic-errors -Wfloat-equal -Wredundant-decls  -Wswitch-default  -pedantic -Wconversion)

# Build executable
add_executable(${This} ${C_TEST_SRCS} ${CPP_TEST_SRCS} ${MOCK_SRCS})

find_library(CPPUTEST_LIBRARY NAMES CppUTest PATHS ${CPPUTEST_HOME} NO_DEFAULT_PATH)
find_library(CPPUTESTEXT_LIBRARY NAMES CppUTestExt PATHS ${CPPUTEST_HOME} NO_DEFAULT_PATH)

if(NOT CPPUTEST_LIBRARY OR NOT CPPUTESTEXT_LIBRARY)
  message(FATAL_ERROR "CppUTest library not found")
endif()

target_link_libraries(${This} ${CPPUTEST_LIBRARY} ${CPPUTESTEXT_LIBRARY} MAIN_SOURCES)
