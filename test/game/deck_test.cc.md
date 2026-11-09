# AI-TDD Transparency Report

**File:** `test/card/deck_test.cc`  
**Contributor:** Jingtong Hu ([TNY233](https://github.com/TNY233))  
**AI Tools:** ChatGPT (OpenAI GPT-6); OpenAI Codex  
**Date:** November 8–9, 2026  
**Development branch:** `TNY233/deck-system`

## Input

The contributor used ChatGPT to design the Deck system for *Dragon and the Knight* and used Codex for AI-assisted GoogleTest development. The tests were intended to define the required Deck behavior before the Deck implementation was committed.

Requirements and decisions discussed with the AI included:

- A persistent Card collection stored separately from temporary battle piles.
- A battle draw pile, hand, and discard pile.
- `AddCard`, `RemoveCard`, and `GetCards` for managing the persistent collection.
- `StartBattle`, `Shuffle`, `Draw`, `ReshuffleDiscardPile`, `DiscardPlayedCard`, `DiscardHand`, and `GetHand` for battle operations.
- A seedable RNG so shuffle behavior can be tested reproducibly.
- `StartBattle` rebuilding the battle piles from the persistent collection and starting with an empty hand, without automatically drawing cards.
- `Draw` appending cards to the existing hand, and recycling the discard pile only when necessary.
- Invalid indexes being rejected without modifying the collection or hand.
- Unplayed cards remaining in the hand between ordinary turns; `DiscardHand` is a separate explicit operation, **not** an automatic end-of-turn rule.

The contributor instructed Codex to develop tests and keep separate test-first and implementation commits. Later prompts instructed Codex to fix only cpplint issues and to preserve unrelated changes and existing commit timestamps. The requirements above are a summary of the development discussions, **not a fabricated verbatim transcript of Codex prompts**.

## Output

### 1. AI-assisted GoogleTest suite: 15 tests

Codex assisted in generating `test/card/deck_test.cc`. Its test fixtures use simple Cards identified by metadata, not finished gameplay abilities. The tests assert Deck behavior through public methods and compare Card metadata rather than relying on a particular shuffled permutation.

The following test names were checked against the Deck test file uploaded to the team project repository:

| # | Test | Behavior tested |
|---|---|---|
| 1 | `DeckConstructionTest.SeededDeckStartsWithEmptyCollectionAndHand` | A newly constructed seeded Deck has an empty collection and hand. |
| 2 | `DeckConstructionTest.AddCardCopiesMetadataAndAllowsDuplicateCards` | Adding Cards copies metadata and permits duplicates. |
| 3 | `DeckTest.GetCardsExposesCollectionAsAConstReference` | `GetCards` exposes the persistent collection through a const reference. |
| 4 | `DeckTest.RemoveCardAcceptsFirstMiddleAndLastIndexes` | Valid indexes can remove Cards from different positions. |
| 5 | `DeckTest.RemoveCardRejectsInvalidIndexesWithoutChangingCollection` | Invalid indexes do not alter the collection. |
| 6 | `DeckTest.StartBattleMakesCollectionDrawableAndLeavesHandEmpty` | Battle setup populates the draw pile but does not draw an opening hand. |
| 7 | `DeckTest.StartBattleResetsPreviousDrawHandAndDiscardPiles` | Starting a new battle resets temporary battle state. |
| 8 | `DeckTest.DrawAppendsRequestedNumberWithoutReplacingExistingHand` | Drawing adds Cards without replacing Cards already in hand. |
| 9 | `DeckConstructionTest.DrawingFromEmptyBattleLeavesHandEmpty` | Drawing from an empty battle Deck leaves the hand empty. |
| 10 | `DeckTest.DrawStopsWhenDrawAndDiscardPilesAreEmpty` | An exhausted Deck does not generate extra Cards. |
| 11 | `DeckTest.ShuffleIsReproducibleWithIdenticalSeedAndInitialDeck` | Identical seeds and collections yield reproducible shuffle results. |
| 12 | `DeckTest.DiscardPlayedCardRemovesSelectedHandCardAndMakesItDrawable` | A played Card moves to discard and can later be drawn again. |
| 13 | `DeckTest.DiscardPlayedCardRejectsInvalidIndexesWithoutChangingHand` | Invalid hand indexes are rejected without changing the hand. |
| 14 | `DeckTest.DiscardHandMovesAllCardsToDiscardForLaterDrawing` | An explicit discard-hand operation moves all hand Cards into the recyclable pile. |
| 15 | `DeckTest.DrawUsesRemainingDrawPileThenReshufflesDiscardsInSameRequest` | A single Draw can consume the remaining draw pile and then recycle discards without duplicating Cards. |

The initial test source included comments documenting the assumed Deck contract and requesting team confirmation before implementing Deck gameplay. Those assumptions were subsequently discussed and used in implementation.

### 2. Implementation and AI-assisted follow-up

The contributor worked through the Deck design and implementation with ChatGPT guidance, with Codex assisting in tests, checking, and repository operations. The resulting implementation covers:

- `src/card/deck.h` and `src/card/deck.cc`
- `src/game/rng.h` and `src/game/rng.cc`
- Test build configuration in `CMakeLists.txt`

The implementation separates `cards_` from temporary draw, hand, and discard piles; supports repeatable RNG behavior; and uses Card copy support when initializing battle state. No assertion is made that complete Player/Enemy/Battle integration or an automatic per-turn drawing policy has been finalized.

Codex later fixed Deck-specific cpplint findings in `src/card/deck.cc`, adding an explicit `<vector>` include and removing redundant blank lines/trailing whitespace. Separately, Card formatting fixes were integrated into the Deck branch by a normal merge; no gameplay code or tests were rewritten during that integration.

### 3. Verification and TDD chronology

| Check | Reported result |
|---|---|
| Deck GoogleTests | **15/15 passed** after implementation |
| Combined Card + Deck GoogleTests | **31/31 passed** in the integrated version |
| cpplint 2.0.2 (`src/` and `test/`) | **0 errors** in the integrated version |
| `git diff --check` | Passed |
| Deck test commit | `553e642` — November 8, 2026, 22:33:42 −08:00 |
| Deck implementation commit | `b331b43` — November 8, 2026, 22:34:12 −08:00 |
| Deck formatting commit | `09239e1` — November 9, 2026 |
| Card-format integration merge | `ebd2cb8` — November 9, 2026 |

Git verification showed that the Deck test commit is an ancestor of the implementation commit, with its recorded Author Date and Commit Date **30 seconds earlier**. The original commit timestamps were left unchanged. These facts establish commit chronology but **do not independently prove** when the source was originally written or that a failing (RED) suite was executed before implementation.

The Deck test commit also follows the earlier Card implementation in the shared Git history, because Deck depends on Card. This does not contradict the test-before-implementation sequence **within the Deck feature**.

The results above come from the contributor's locally reviewed Codex build and verification reports; a test-only commit on GitHub is not expected to have a fully implemented Deck target yet.

## Human Contribution

The contributor was responsible for project requirements, gameplay choices, review, and validation of the proposed Deck behavior.

- Chose to separate the permanent Deck collection from the hand and battle draw/discard piles.
- Decided that `StartBattle` does not automatically draw an opening hand.
- Chose to retain unplayed hand Cards between turns, leaving `DiscardHand` as an explicit operation.
- Worked through `AddCard`, `RemoveCard`, `StartBattle`, `Draw`, discard/recycle behavior, and RNG considerations with ChatGPT assistance.
- Reviewed the GoogleTest contract, Codex-produced test suite, and implementation behavior.
- Required separate test and implementation commits, verified their actual timestamps, and retained the original history.
- Requested narrowly scoped cpplint fixes and checked the final 31-test and zero-lint reports before planning the team push.

**AI contribution disclosure:** ChatGPT assisted with conceptual explanations, interface design, implementation guidance, and review. Codex assisted with generating the 15-test suite, building/testing it, lint repairs, and Git integration/verification. This was an **AI-assisted development workflow**, not a claim of entirely unaided code production.

The available record confirms test-before-implementation **commit ordering**. It does not establish that a complete Red–Green–Refactor cycle was executed for every Deck method, and no such claim is made.

## References

- [Main game repository](https://github.com/DragonandtheKnight/Dragon-and-the-Knight)
- [AI-TDD Transparency repository](https://github.com/DragonandtheKnight/AI-TDD-Transparency)
- [Deck test source at test commit `553e642`](https://github.com/DragonandtheKnight/Dragon-and-the-Knight/blob/553e642/test/card/deck_test.cc)
- Deck test commit: `553e642` — `test deck system`
- Deck implementation commit: `b331b43` — `build deck system`
- Deck formatting commit: `09239e1` — `fix deck cpplint`
- Card formatting commit: `ba8bca5` — `fix card cpplint`
- Integrated Deck merge: `ebd2cb8` — `Merge Card cpplint fixes into Deck`
- Development branch: `TNY233/deck-system`

**Documentation location:** This AI-TDD report is stored in `test/game/deck_test.cc.md`; the C++ test source remains at `test/card/deck_test.cc` in the main game repository.

**Scope note:** This report documents the Deck-specific test suite. Card-effect ownership, copy/move behavior, and their tests are covered in `card/card_test.cc.md`.
