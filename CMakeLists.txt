cmake_minimum_required(VERSION 3.27.7)

project(cmakeEx3)

add_executable(${PROJECT_NAME} main.cpp)

add_subdirectory(Adder)

target_include_directories(${PROJECT_NAME} PUBLIC Adder)
target_link_directories(${PROJECT_NAME} PUBLIC Adder)
target_link_libraries(${PROJECT_NAME} mearlymath)