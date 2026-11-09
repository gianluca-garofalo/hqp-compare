cmake_minimum_required(VERSION 3.22)
project(hqp-compare VERSION 0.1.0 LANGUAGES CXX)

find_package(Eigen3 REQUIRED NO_MODULE)

include(FetchContent)
set(FETCHCONTENT_UPDATES_DISCONNECTED ON)

FetchContent_Declare(daqp
    GIT_REPOSITORY "https://github.com/darnstrom/daqp"
    GIT_TAG "origin/master"
)
FetchContent_MakeAvailable(daqp)

FetchContent_Declare(lexls
    GIT_REPOSITORY "https://github.com/jrl-umi3218/lexls"
    GIT_TAG "origin/master"
)
FetchContent_MakeAvailable(lexls)

if(BUILD_TESTING)
    add_subdirectory(tests)
endif()
