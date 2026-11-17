# AI-TDD Transparency Report

**File:** `test/map/floor_map_test.cc`  
**Contributor:** `1nkWang`  
**AI Tool:** OpenAI Codex

## Input

- Asked for tests covering the three extended routes in the fixed 17-node map.
- Asked for tests for seeded random floor generation and for a test run against the saved random-map implementation.
- Asked to test the merged map and non-combat levels and to verify the GitHub checks.

## Output

- Updated the fixed-map route tests to traverse each route, verify the only final node, and reject invalid movement. The initial checks exposed the difference between the eight-node and 17-node maps. All nine Level/FloorMap tests passed after the route extension was saved.
- Drafted separate tests for seed reproducibility, variation across seeds, reachability, and preservation of the current map after a factory failure. That draft targeted an earlier three-route proposal. The final random-grid run used the repository's current test suite.
- Compiled and ran the current Level/FloorMap suite. All 20 tests passed, including coverage for same-column connections, no re-entry into visited nodes, and map properties over 300 seeds.
- Built an isolated verification copy combining the branch's map changes with the incoming non-combat levels and CMake targets. The game executable built and all 206 CTest cases passed. GitHub's GoogleTest and cpplint jobs also passed after the merge commit was pushed.

## Human Contribution

- Implemented and saved the current random-grid map, including the two-way same-column connections and visited-node tracking.
- Resolved the conflicts with `main`, retaining the room modules and their tests alongside the map changes, then committed and pushed the merge.

## References

- [Random map generation issue #32](https://github.com/DragonandtheKnight/Dragon-and-the-Knight/issues/32)
- [Pull request #43](https://github.com/DragonandtheKnight/Dragon-and-the-Knight/pull/43)
- [Seeded map commit `fe0ec27`](https://github.com/DragonandtheKnight/Dragon-and-the-Knight/commit/fe0ec277354e2bad7e5182ebde31439a80690137)
- [Merge commit `56c1f27`](https://github.com/DragonandtheKnight/Dragon-and-the-Knight/commit/56c1f275f19aebe76d35e44a24ccffc16609da60)
- [GoogleTest run](https://github.com/DragonandtheKnight/Dragon-and-the-Knight/actions/runs/44023240856) and [cpplint run](https://github.com/DragonandtheKnight/Dragon-and-the-Knight/actions/runs/44023240970)
