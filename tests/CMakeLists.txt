add_executable(compare_02_compare_solvers 02_compare_solvers.cpp)
add_executable(compare_04_random_problem 04_random_problem.cpp)
add_executable(compare_05_lex_slacks 05_lex_slacks.cpp)
add_executable(compare_06_timing_stats 06_timing_stats.cpp)

set(COMPARE_TARGETS
    compare_02_compare_solvers
    compare_04_random_problem
    compare_05_lex_slacks
    compare_06_timing_stats
)

foreach(TARGET ${COMPARE_TARGETS})
    target_link_libraries(${TARGET} PRIVATE Eigen3::Eigen HQP::HQP daqp_eigen lexls)
    if(TARGET hqp-tasks)
        target_link_libraries(${TARGET} PRIVATE hqp-tasks)
    endif()
    target_include_directories(${TARGET} PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})
    add_test(NAME test_${TARGET} COMMAND ${TARGET})
endforeach()
