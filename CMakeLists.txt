cmake_minimum_required(VERSION 3.27.7)

# 设置一些配置参数(如版本号)
set(PROJECT_VERSION_MAJOR 1)
set(PROJECT_VERSION_MINOR 0)
set(PROJECT_VERSION_PATCH 0)
set(CONFIG_STRING "PROJECT VERSION CONFIG FILE")

# 使用 configure_file 生成头文件
configure_file(cmakeEx3_config.h.in cmakeEx3_config.h)

project(cmakeEx3)
add_executable(${PROJECT_NAME} main.cpp)

option(USE_ADDER, "a simple add two float number" ON)
if(USE_ADDER)
    add_subdirectory(Adder)
    # list(APPEND EXTRA_LIB_DwIRS "${CMAKE_BINARY_DIR}/Adder/")
    list(APPEND EXTRA_LIB_DIRS "${PROJECT_SOURCE_DIR}/Adder")
    list(APPEND EXTRA_INCLUDES_DIRS "${PROJECT_SOURCE_DIR}/Adder")
    list(APPEND EXTRA_LINKS mearlymath)
endif()

# Download ALL the SUBMODULES
find_package(Git QUIET)
if(GIT_FOUND AND EXISTS "${PROJECT_SOURCE_DIR/.git}")
#update submodules as needed
    option(GIT_SUBMODULES "Check submodules during build" ON)
    if(GIT_SUBMODULE)
        message(STATUS "Submodule update")
        execute_process(COMMAND ${GIT_EXECUTABLE} submodule update --init --recursive
            WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
            RESULT_VARIABLE GIT_SUBMOD_RESULT)
        if(NOT GIT_SUBMOD_RESULT EQUAL "0")
            message(FATAL_ERROR "git submodule update --init failed with ${GIT_SUBMOD_RESULT}, please checkout submodules")    
        endif(NOT GIT_SUBMOD_RESULT EQUAL "0")      
    endif()  
endif()

# check all the submodules
if(NOT EXISTS "${PROJECT_SOURCE_DIR}/external/glfw/CMakeLists.txt")
    message(FATAL_ERROR "The glfw submodules was not downloaded! GIT_SUBMODULE was turned off or failed. please check!")    
endif()

add_subdirectory(external/glfw)

target_include_directories(${PROJECT_NAME} 
    # PUBLIC Adder 
    PUBLIC external/glfw/include
    PUBLIC external/glfw/deps
    ${EXTRA_INCLUDES_DIRS}
)

target_link_directories(${PROJECT_NAME}
    # PRIVATE Adder
    PRIVATE external/glfw/src
    PRIVATE external/glfw/deps
    ${EXTRA_LIB_DIRS}
) 

# 包含生成的头文件目录
include_directories(${CMAKE_BINARY_DIR})

target_link_libraries(${PROJECT_NAME} ${EXTRA_LINKS} glfw)