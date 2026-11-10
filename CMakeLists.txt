cmake_minimum_required(VERSION 3.22)

project(hqp_compare VERSION 0.1.0 DESCRIPTION "Comparisons of the HQP solver with DAQP and LexLS" LANGUAGES CXX)

include(CTest)
include(FetchContent)

find_package(Eigen3 3.4 REQUIRED NO_MODULE)

# HQP and hqp_tasks: use installed packages when CMake finds them (set
# CMAKE_PREFIX_PATH), otherwise fetch them. To build against local checkouts,
# pass -DFETCHCONTENT_SOURCE_DIR_HQP=... and -DFETCHCONTENT_SOURCE_DIR_HQP_TASKS=...
set(HQP_GIT_TAG "main" CACHE STRING "Weighted-Hierarchical-QP revision to fetch when HQP is not installed")
set(HQP_TASKS_GIT_TAG "main" CACHE STRING "hqp-tasks revision to fetch when hqp_tasks is not installed")

find_package(HQP CONFIG QUIET)
if(HQP_FOUND)
    message(STATUS "hqp_compare: using HQP from ${HQP_DIR}")
else()
    FetchContent_Declare(HQP
        GIT_REPOSITORY "https://github.com/gianluca-garofalo/Weighted-Hierarchical-QP.git"
        GIT_TAG ${HQP_GIT_TAG}
    )
    FetchContent_MakeAvailable(HQP)
    message(STATUS "hqp_compare: fetched HQP at ${HQP_GIT_TAG}")
endif()

find_package(hqp_tasks CONFIG QUIET)
if(hqp_tasks_FOUND)
    message(STATUS "hqp_compare: using hqp_tasks from ${hqp_tasks_DIR}")
else()
    FetchContent_Declare(hqp_tasks
        GIT_REPOSITORY "https://github.com/gianluca-garofalo/hqp-tasks.git"
        GIT_TAG ${HQP_TASKS_GIT_TAG}
    )
    FetchContent_MakeAvailable(hqp_tasks)
    message(STATUS "hqp_compare: fetched hqp_tasks at ${HQP_TASKS_GIT_TAG}")
endif()

# Reference solvers, pinned so that results do not move with their default
# branches. Bump these deliberately.
set(EIGEN ON CACHE BOOL "Build DAQP's Eigen interface (daqp_eigen)")
FetchContent_Declare(daqp
    GIT_REPOSITORY "https://github.com/darnstrom/daqp"
    GIT_TAG ac404cbc1cced268b4742ec40a01169ec41d0c08
)
FetchContent_Declare(lexls
    GIT_REPOSITORY "https://github.com/jrl-umi3218/lexls"
    GIT_TAG d5562f9c707060710ef8c9a4d83deed4e14c8b66
)
# Keep LexLS's own tests out of this project's ctest run.
set(BUILD_TESTING OFF)
FetchContent_MakeAvailable(daqp lexls)
unset(BUILD_TESTING)

if(BUILD_TESTING)
    add_subdirectory(tests)
endif()
