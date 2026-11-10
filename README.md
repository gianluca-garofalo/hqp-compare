# HQP Compare

Comparisons and benchmarks of the [HQP solver](https://github.com/gianluca-garofalo/Weighted-Hierarchical-QP) against [DAQP](https://github.com/darnstrom/daqp) and [LexLS](https://github.com/jrl-umi3218/lexls).

| Test | What it checks |
|------|----------------|
| `02_compare_solvers` | One hierarchy built with [hqp-tasks](https://github.com/gianluca-garofalo/hqp-tasks), solved by all three |
| `04_random_problem` | HQP matches LexLS on a random hierarchy (new seed every run) |
| `05_lex_slacks` | HQP matches LexLS; prints the per-level slacks of DAQP and LexLS |
| `06_timing_stats` | Timing statistics of the three solvers over repeated solves |

## Building and running

```bash
cmake -S . -B build -G Ninja -DCMAKE_BUILD_TYPE=Release
cmake --build build
ctest --test-dir build --output-on-failure
./build/tests/06_timing_stats
```

HQP and hqp_tasks come from installed packages when CMake finds them (point `CMAKE_PREFIX_PATH` at their prefix); otherwise they are fetched at `HQP_GIT_TAG` and `HQP_TASKS_GIT_TAG` (default `main`). To compare a local change to the solver, pass `-DFETCHCONTENT_SOURCE_DIR_HQP=/path/to/Weighted-Hierarchical-QP`, and likewise `FETCHCONTENT_SOURCE_DIR_HQP_TASKS` for hqp-tasks.

DAQP and LexLS are pinned to specific commits in `CMakeLists.txt`, so results only change when those pins are bumped.

## License

BSD 3-Clause License.
