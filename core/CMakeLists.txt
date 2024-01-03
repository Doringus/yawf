project(yafw-core CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED True)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fsanitize=thread")

file(GLOB_RECURSE SOURCES
    ${CMAKE_CURRENT_SOURCE_DIR}/include/*pp
    ${CMAKE_CURRENT_SOURCE_DIR}/src/*pp
)

file(GLOB_RECURSE UNIT_TEST_SOURCES
        ${CMAKE_CURRENT_SOURCE_DIR}/test/*_test.cpp
)

add_library(${PROJECT_NAME} STATIC ${SOURCES})

target_include_directories(${PROJECT_NAME} PUBLIC
        ${CMAKE_CURRENT_SOURCE_DIR}/include
)

include(FetchContent)
FetchContent_Declare(
        googletest
        GIT_REPOSITORY https://github.com/google/googletest
        GIT_TAG v1.14.0
)
set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)
FetchContent_MakeAvailable(googletest)

enable_testing()
add_executable(${PROJECT_NAME}-unittest ${UNIT_TEST_SOURCES})
target_include_directories (${PROJECT_NAME}-unittest PRIVATE
        $<TARGET_PROPERTY:${PROJECT_NAME},INCLUDE_DIRECTORIES>
)

target_link_libraries(${PROJECT_NAME}-unittest PRIVATE
        gtest_main
        yafw-core
)

include(GoogleTest)
gtest_discover_tests(${PROJECT_NAME}-unittest)