# 02 and 06 build their problems with hqp_tasks; all four compare HQP with
# DAQP and LexLS.
set(TARGETS
    02_compare_solvers
    04_random_problem
    05_lex_slacks
    06_timing_stats
)

foreach(TARGET ${TARGETS})
    add_executable(${TARGET} ${TARGET}.cpp)
    target_link_libraries(${TARGET} PRIVATE Eigen3::Eigen HQP::HQP hqp_tasks::hqp_tasks daqp_eigen lexls)
    target_include_directories(${TARGET} PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})
    add_test(NAME test_${TARGET} COMMAND ${TARGET})
endforeach()
