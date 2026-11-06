# AI-TDD Transparency Report

**File:** `test/game/rng_test.cc`  
**Contributor:** Lizi Luo ([ChestnutLUO](https://github.com/ChestnutLUO))  
**AI Tool:** Claude Code (Claude Opus 5.5, `claude-opus-5-5`)

## Input

Prompts given to the AI, verbatim and in order (2026-11-02):

1. > draft tests for the Rng class
2. > use mt 19937

The second prompt arrived while the AI was writing `src/game/rng.h` (see [`../../src/game/rng.h.md`](../../src/game/rng.h.md)). The AI then changed the engine from SplitMix64 to `std::mt19937` in the tests, the header and the board.

Earlier in the same session the contributor and the AI designed `Rng` on the Class UML board ("RNG · one seeded stream (proposal)"). The contributor decided on one random stream per run, with each enemy constructed from its own seed drawn from that stream.

## Output

The AI wrote all of `test/game/rng_test.cc`: 25 unit tests for `Rng` in `src/game/rng.h`. The interface comes from the board, with one change. `Shuffle` became `template <typename T> void Shuffle(std::vector<T>&)` instead of taking `std::vector<Card>&`, because `card/Card.h` doesn't compile yet (`card/CardType.h` is missing) and because more than cards may need shuffling. The AI updated the board to match.

| Test | Behaviour required |
|---|---|
| `RngSeedTest.SameSeedGivesSameSequence` | Two `Rng(42)` give the same 100 values |
| `RngSeedTest.DifferentSeedsGiveDifferentSequences` | Seeds 42 and 43 give different values |
| `RngSeedTest.NextU32MatchesMt19937ReferenceValues` | With seed 5489 (mt19937's default), the 1st value is 3499211612 and the 10 000th is 4123659995, the value the C++ standard requires. So a seed gives the same run on every platform |
| `RngStateTest.RestoredStateContinuesTheSameSequence` | `SetState(other.GetState())` continues the other Rng's sequence (for Load Game) |
| `RngStateTest.RestoringAnEarlierStateReplaysIt` | Restoring a saved state replays the same values |
| `RngStateTest.RestoringTextThatIsNotAStateThrows` | `SetState("not a saved state")` and `SetState("")` throw `std::invalid_argument` |
| `RngUniformIntTest.StaysWithinInclusiveRange` | `UniformInt(-3, 3)` stays in −3…3 |
| `RngUniformIntTest.ReachesBothEnds` | `UniformInt(1, 6)` returns both 1 and 6 |
| `RngUniformIntTest.EqualMinAndMaxReturnsThatValue` | `UniformInt(5, 5)` is 5 |
| `RngUniformIntTest.IsRoughlyUniform` | 60 000 draws of 1…6: each value 10 000 ± 600 |
| `RngUniformIntTest.FullIntRangeDoesNotOverflow` | `UniformInt(INT_MIN, INT_MAX)` works and gives negative and non-negative values |
| `RngUniformIntTest.MinAboveMaxThrows` | `UniformInt(6, 1)` throws `std::invalid_argument` |
| `RngChanceTest.ZeroPercentNeverHappens` | `Chance(0)` is always false |
| `RngChanceTest.HundredPercentAlwaysHappens` | `Chance(100)` is always true |
| `RngChanceTest.ThirtyPercentHappensAboutThirtyPercentOfTheTime` | 10 000 calls of `Chance(30)`: 3 000 ± 300 hits |
| `RngChanceTest.PercentOutsideZeroToHundredThrows` | `Chance(-1)` and `Chance(101)` throw `std::invalid_argument` |
| `RngPickIndexTest.ReachesEveryIndexAndNoOther` | `PickIndex(4)` returns exactly 0, 1, 2 and 3 |
| `RngPickIndexTest.SizeOfOneAlwaysPicksZero` | `PickIndex(1)` is 0 |
| `RngPickIndexTest.EmptyOrNegativeSizeThrows` | `PickIndex(0)` and `PickIndex(-1)` throw `std::invalid_argument` |
| `RngShuffleTest.KeepsTheSameElements` | A shuffle is a permutation |
| `RngShuffleTest.ChangesTheOrder` | Shuffling 0…19 changes the order |
| `RngShuffleTest.SameSeedGivesSameOrder` | The same seed gives the same order |
| `RngShuffleTest.EmptyAndSingleElementVectorsAreFine` | Empty and one-element vectors are unchanged |
| `RngShuffleTest.EveryOrderIsEquallyLikely` | 60 000 shuffles of {0, 1, 2}: each of the 6 orders 10 000 ± 600 |
| `RngShuffleTest.WorksForAnyElementType` | Shuffles a `std::vector<std::string>` |

The counting tests use a fixed seed, so they give the same result on every run. Their bounds are about six standard deviations wide, so any correct generator passes them.

### Test verification performed by the AI

The tests were built outside the main repository, against a throwaway stand-in `Rng` (`std::mt19937`, rejection sampling, Fisher–Yates) and eight broken versions of it. The first draft, which used SplitMix64, was checked the same way before the engine changed. The stand-ins were **never added to the main repository** and were deleted after the check. No AI-written implementation code is in the project.

| Stand-in | Result |
|---|---|
| Correct | All 25 pass, also when built with the committed `CMakeLists.txt` on an exported copy of branch `rng` |
| Seed ignored | `DifferentSeedsGiveDifferentSequences` and `NextU32MatchesMt19937ReferenceValues` fail |
| A different engine (`std::mt19937_64`, ignoring the seed) | `NextU32MatchesMt19937ReferenceValues`, `DifferentSeedsGiveDifferentSequences` and the two state round-trip tests fail |
| No argument checks | The three `…Throws` tests fail |
| `max - min + 1` computed as `int` | Only `FullIntRangeDoesNotOverflow` fails |
| `UniformInt` never returns `max` | `ReachesBothEnds`, `IsRoughlyUniform`, `ReachesEveryIndexAndNoOther` and `EveryOrderIsEquallyLikely` fail |
| Shuffle swaps with any position (biased) | Only `EveryOrderIsEquallyLikely` fails |
| `SetState` ignored | `RestoredStateContinuesTheSameSequence` and `RestoringAnEarlierStateReplaysIt` fail |
| `SetState` accepts any text | Only `RestoringTextThatIsNotAStateThrows` fails |

| Check | Result |
|---|---|
| Main repo build | **Red, as expected:** the tests compile against the declarations in `src/game/rng.h` and stop at the link step, because no `Rng` function is defined yet |
| `cpplint`, `clang-format --dry-run --Werror` | Clean |

## Human Contribution

- Decided the design on the Class UML board: one seeded stream per run, owned by `Game` and passed as `Rng&`, with each enemy built from its own seed.
- Asked for these tests (prompt above).
- **To complete before merging (contributor):**
  - [ ] Read and understood every test above.
  - [ ] Agreed with the team on the choices these tests pin: `std::mt19937` as the engine, the engine state saved as text, `std::invalid_argument` for bad arguments, and a template `Shuffle`.
  - [ ] Agreed with the owners of `Game`, `FloorMap`, `Level`, `Enemy`, `Deck` and `Battle` on the proposed changes to their classes.
  - [ ] Implemented `Rng` by hand and saw all 25 tests pass.
  - [ ] Recorded any edits made to the AI-generated tests.

## References

- Commit: `d0658aa` (`test(game): add Rng tests`), branch `rng`, not pushed yet
- Based on: commit `f2225eb` (`feat(game): add Rng class declaration`), branch `rng`
- Related reports: [`../../src/game/rng.h.md`](../../src/game/rng.h.md), [`../../CMakeLists.txt.md`](../../CMakeLists.txt.md) (revision 4: the game library and test target)
