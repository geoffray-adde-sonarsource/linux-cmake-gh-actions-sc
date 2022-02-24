cmake_minimum_required(VERSION 3.9)
project(sonar_scanner_example)
set(CMAKE_BUILD_TYPE Debug)
set(CMAKE_CXX_OUTPUT_EXTENSION_REPLACE ON)


# add flags to get coverage
set(CMAKE_CXX_FLAGS " ${CMAKE_CXX_FLAGS} -fprofile-arcs -ftest-coverage")
set(CMAKE_CXX_STANDARD 17)

# build the executable
add_executable(sonar_scanner_example src/main.cpp src/mylib.cpp)

# build the test
add_executable(test src/test.cpp src/mylib.cpp)

# create directory for gcov coverage reports
add_custom_target(coverage_dir ALL
    COMMAND ${CMAKE_COMMAND} -E make_directory ${CMAKE_BINARY_DIR}/coverage)

# run test executable
add_custom_target(run_test ALL
                  COMMAND $<TARGET_FILE:test>
                  DEPENDS sonar_scanner_example coverage_dir
                  )

# run gcov
add_custom_target(compute_coverage ALL
                  COMMAND gcov -o ${CMAKE_BINARY_DIR}/CMakeFiles/test.dir/src ${PROJECT_SOURCE_DIR}/src/*.cpp
                  DEPENDS run_test
                  WORKING_DIRECTORY ${CMAKE_BINARY_DIR}/coverage
                  )
