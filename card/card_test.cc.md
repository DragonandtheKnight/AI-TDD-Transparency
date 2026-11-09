# AI-TDD Transparency Report

**File:** `test/card/card_test.cc`  
**Contributor:** Jingtong Hu ([TNY233](https://github.com/TNY233))  
**AI Tools:** ChatGPT (OpenAI GPT-6); OpenAI Codex  
**Initial activity:** November 2, 2026  
**Updated activity:** November 8–9, 2026  
**Development branches:** `feature/8-card-data` (initial work); `TNY233/card-system` (updated Card system)

## Input

The contributor used ChatGPT and Codex to discuss and develop GoogleTest coverage for the Card system in *Dragon and the Knight*. The project uses C++17 and GoogleTest. The contributor supplied or discussed the existing Card interfaces, project requirements, and the decision to use test-first development where appropriate.

### Initial ChatGPT activity (November 2)

The original report documented these selected contributor prompts, in chronological order:

1. “我理解的tdd都是先写测试然后在写代码 我们不应该是把全部的测试先写完然后继续写实现”
2. “Ok, execute”
3. The contributor supplied the resulting C++ test code and asked ChatGPT to “check” it.

ChatGPT explained the Red–Green–Refactor cycle and distinguished regression tests for existing Card functionality from tests intended to precede new effect implementations. The initial draft included a Card constructor test and three proposed BlockEffect tests.

### Later Card-system work (November 8–9)

The contributor established the architecture and requested a broader, test-first Card interface, including:

- A Card with one primary type and potentially multiple effects.
- A polymorphic `CardEffect` interface, with `Card` owning effects through `std::unique_ptr`.
- A shared, non-owning `EffectContext` passed to effects.
- Deep-copy behavior through a virtual `clone()` operation so Cards can be safely stored and copied by value in containers such as `std::vector<Card>`.
- Explicit tests for copy and move semantics, effect ordering, ownership, invalid inputs, failure cases, and destruction.

The contributor used Codex to help generate the new test suite and Card infrastructure. Subsequent instructions asked Codex to fix cpplint-only issues without changing game behavior, assertions, or test coverage, and to preserve the original TDD commits and timestamps. This section summarizes those later instructions rather than presenting them as verbatim prompts.

## Output

### 1. Initial test draft: historical record

The November 2 draft contained these four tests:

| Initial test | Intended check |
|---|---|
| `CardTest.ConstructorStoresCardData` | Verify stored Card constructor data. |
| `BlockEffectTest.AddsShield` | Verify a BlockEffect adds shield. |
| `BlockEffectTest.StacksShield` | Verify repeated BlockEffect applications stack shield. |
| `BlockEffectTest.ZeroBlockChangesNothing` | Verify a zero-value BlockEffect does not change shield. |

At that time, the Card constructor and getters had already been implemented. The constructor test was therefore a **regression test**, not evidence of test-first implementation. The proposed BlockEffect interfaces were not final; the initial tests had not been confirmed to compile or pass. These four draft tests must not be confused with the later 16-test suite.

### 2. Updated GoogleTest suite: 16 tests

Codex assisted in developing the revised `test/card/card_test.cc`, which uses test doubles named `RecordingEffect`, `FailingCloneEffect`, and `DestructionEffect` to observe polymorphic behavior and ownership without requiring finished combat effects.

The following test names were checked against the Card test file uploaded to the team project repository:

| # | Test (`CardTest.*`) | Behavior tested |
|---|---|---|
| 1 | `ConstructorPreservesMetadata` | The constructor stores Card metadata. |
| 2 | `PreservesPrimaryTypeAndApCost` | Card types and action-point costs are preserved, including zero cost. |
| 3 | `EmptyCardHasNoImplicitEffects` | A Card with no effects can be applied without executing an effect. |
| 4 | `AddingOneEffectTransfersOwnershipAndDispatchesIt` | Adding a `unique_ptr` transfers ownership and calls the effect. |
| 5 | `MultipleEffectsExecuteInInsertionOrderOnEveryApplication` | Multiple effects run in insertion order on repeated applications. |
| 6 | `NullEffectIsRejectedWithoutChangingExistingEffects` | A null effect is rejected without losing existing effects. |
| 7 | `CopyConstructorClonesStateIntoIndependentEffects` | Copy construction deep-copies owned effects. |
| 8 | `CopyRemainsUsableAfterOriginalIsDestroyed` | A copied Card remains usable after the original is destroyed. |
| 9 | `CopyAssignmentReplacesMetadataAndDeepCopiesEffects` | Copy assignment replaces metadata and clones effects. |
| 10 | `SelfCopyAssignmentPreservesEffectsAndState` | Self-copy assignment preserves state. |
| 11 | `CopyingAnEmptyCardClearsDestinationEffects` | Assigning a Card without effects removes previous destination effects. |
| 12 | `FailedCopyAssignmentLeavesDestinationUnchanged` | Failed cloning does not corrupt the assignment destination. |
| 13 | `MoveConstructorTransfersEffectsWithoutCloning` | Move construction transfers effects without cloning. |
| 14 | `MoveAssignmentReplacesEffectsWithoutCloning` | Move assignment transfers effects without cloning. |
| 15 | `VectorOfCardsCanCopyCardsByValue` | Cards can be copied independently into a vector. |
| 16 | `OwnedEffectsAreDestroyedWithTheirCards` | Owned effects are destroyed with their Cards and copies. |

These tests establish the Card infrastructure contract. They **do not** establish that concrete Damage, Heal, Block, Poison, or Bleeding combat behaviors have all been implemented or independently tested.

### 3. AI-assisted implementation and follow-up

Following the test commit, Codex assisted in writing the updated Card infrastructure, including `Card.h`, `Card.cc`, `CardType.h`, `card_effect.h`, and `effect_context.h`. The contributor reviewed the approach and requested preservation of the selected composition/polymorphism and cloning design.

Later, Codex made limited cpplint repairs: adding the explicit `<algorithm>` include in `Card.cc`, wrapping long comments in `Card.h`, and correcting include ordering plus an explicit `<algorithm>` include in `card_test.cc`. No test assertions or Card logic were changed in that formatting task.

### 4. Verification and TDD chronology

| Check | Reported result |
|---|---|
| Card GoogleTests | **16/16 passed** after implementation |
| Combined Card + Deck GoogleTests | **31/31 passed** in the final locally integrated version |
| cpplint 2.0.2 (`src/` and `test/`) | **0 errors** in the validated integrated version |
| `git diff --check` | Passed |
| Card test commit | `1299196` — November 8, 2026, 22:30:40 −08:00 |
| Card implementation commit | `8c54fe6` — November 8, 2026, 22:32:09 −08:00 |
| Card formatting commit | `ba8bca5` — November 9, 2026 |

Git verification showed that the Card test commit is an ancestor of the implementation commit and that both recorded Author Date and Commit Date precede implementation by **89 seconds**. These timestamps establish the **commit order**, not the original time each line of code was written or the existence of an executed failing (RED) test run. The available records do not independently establish a completed RED run before implementation.

Verification results above are based on local Codex execution reports reviewed by the contributor; they should not be confused with a successful GitHub Actions run on a test-only remote branch.

## Human Contribution

The contributor was responsible for selecting the requirements, deciding how AI assistance would be used, reviewing results, and choosing the integration workflow.

- Independently worked on the initial Card constructor/getters before the November 2 regression test was drafted.
- Selected the Card system for AI-assisted test development and requested that tests precede new implementations where feasible.
- Discussed and approved the multiple-effect Card architecture, single primary Card type, polymorphic effects, non-owning context, and the need for independent deep copies.
- Reviewed the proposed interfaces, tests, commit structure, and Codex verification reports.
- Required separate test and implementation commits and explicitly checked their Git chronology.
- Requested cpplint-only changes, preserved unrelated changes, and checked that the original TDD history remained intact.

**AI contribution disclosure:** ChatGPT provided TDD explanations, test design assistance, and interface discussion. Codex assisted with the revised 16-test suite, Card production infrastructure, compilation/verification, and formatting-only repairs. It would be inaccurate to describe all of the revised production code as independently handwritten by the contributor.

The earlier four-test draft and the later 16-test suite represent **different development stages**. No unverified claim is made that every Card feature followed a fully executed Red–Green–Refactor cycle.

## References

- [Main game repository](https://github.com/DragonandtheKnight/Dragon-and-the-Knight)
- [AI-TDD Transparency repository](https://github.com/DragonandtheKnight/AI-TDD-Transparency)
- [Card test source at test commit `1299196`](https://github.com/DragonandtheKnight/Dragon-and-the-Knight/blob/1299196/test/card/card_test.cc)
- Test commit: `1299196` — `test card system`
- Implementation commit: `8c54fe6` — `build card system`
- Card formatting commit: `ba8bca5` — `fix card cpplint`
- Deck/Card integration merge: `ebd2cb8` — `Merge Card cpplint fixes into Deck`
- Development branch: `TNY233/card-system`

**Scope note:** This report covers `test/card/card_test.cc` and directly related Card infrastructure. Deck tests and Deck implementation are documented separately in `test/game/deck_test.cc.md`.
