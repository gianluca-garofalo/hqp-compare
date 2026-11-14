# Notes for agents working on hqp-compare

hqp-compare compares the HQP solver
([Weighted-Hierarchical-QP](https://github.com/gianluca-garofalo/Weighted-Hierarchical-QP)) with
[DAQP](https://github.com/darnstrom/daqp) and [LexLS](https://github.com/jrl-umi3218/lexls), as tests and timing
statistics. This file is the handoff from the sessions that set it up (November 2026).

## Map

| Test | What it checks |
|---|---|
| `tests/02_compare_solvers.cpp` | One hierarchy built with [hqp-tasks](https://github.com/gianluca-garofalo/hqp-tasks), solved by all three |
| `tests/04_random_problem.cpp` | HQP matches LexLS on a random hierarchy, with a new seed every run |
| `tests/05_lex_slacks.cpp` | HQP matches LexLS; prints the per-level slacks of DAQP and LexLS |
| `tests/06_timing_stats.cpp` | Timing statistics of the three solvers over repeated solves |
| `tests/lexls_interface.hpp` | Builds a LexLS problem from `(A, lower, upper, breaks)` |

## Build and test

```bash
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release && cmake --build build && ctest --test-dir build   # 4 tests
cmake -S . -B build -DFETCHCONTENT_SOURCE_DIR_HQP=/path/to/Weighted-Hierarchical-QP \
                    -DFETCHCONTENT_SOURCE_DIR_HQP_TASKS=/path/to/hqp-tasks                     # local checkouts
./build/tests/06_timing_stats
```

- **HQP and hqp_tasks:** installed packages when CMake finds them, otherwise fetched at `HQP_GIT_TAG` and
  `HQP_TASKS_GIT_TAG` (`main` by default). Weighted-Hierarchical-QP's `downstream` CI job builds this repository
  against its own changes.
- **DAQP and LexLS** are pinned in `CMakeLists.txt` (DAQP `ac404cb`, LexLS `d5562f9`, the same pins as hlq's), so
  results change only when the pins are bumped.
- **CI** (`.github/workflows/ci.yml`): gcc and clang, Debug and Release, and weekly (Monday 04:17 UTC), to catch
  changes in the fetched `main` branches.

## Things to know when comparing

- **DAQP weighs every row alike:** it minimizes the violations of rows scaled to unit norm. Compare it on problems
  whose rows have unit norm, or the "differences" are a different objective.
- **DAQP's accuracy is about 1e-6.** Compare its level costs with a tolerance, not exactly.
- **Compare lexicographically:** level by level, where the first level whose cost differs by more than a relative
  tolerance decides. Comparing solutions directly is wrong when a level has several optima.
- **`04_random_problem` draws a new problem every run,** so a failure may not reproduce. Print the seed when it
  fails. It failed now and then before Weighted-Hierarchical-QP#10 fixed the solver.

## History and scope

- The comparisons lived in Weighted-Hierarchical-QP until its split (Weighted-Hierarchical-QP#9). Their history was
  grafted here.
- #1 made this repository build on its own:
  - HQP and hqp_tasks are found or fetched (before, `HQP::HQP` was linked without being found);
  - DAQP and LexLS are pinned instead of following their `master`, and LexLS's own tests no longer run in ctest;
  - CI was added.
- **For broad comparisons, use hlq's instead.** [hlq](https://github.com/gianluca-garofalo/hlq), HQP's successor,
  has a larger comparison of its own, `benchmarks/compare.cpp` (`hlq_compare`): hlq, HierarchicalQP, LexLS and DAQP
  level by level on 2,000 problems per family, with ill-conditioned and degenerate families. Its timing harness is
  in hlq's `tools/bench`. This repository remains the place for HQP-centered checks and for problems built with
  hqp-tasks.

## Open

- **Pin HQP and hqp_tasks:** once Weighted-Hierarchical-QP tags `v2.0.0` (its issue #8), pin `HQP_GIT_TAG` and
  `HQP_TASKS_GIT_TAG` to releases.
- **Seed `04_random_problem`:** make it seeded, or at least print its seed on failure.

## Conventions

- **Commits and PR titles:** `type> description.`, lowercase, imperative, with a final period (`feat`, `fix`,
  `chore`, `docs`, `test`, `ci`).
- **No AI attribution:** no `Co-Authored-By`/session trailers, and no model names, in commits, code or PRs.
- **Branches and merging:** work on a branch and open a PR; never merge without the owner's explicit go-ahead.
- **PR descriptions:** keep them short: what changed, why, the numbers, how it was checked.
