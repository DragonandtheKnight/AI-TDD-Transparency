# AI-TDD Transparency Report

**File:** `test/card/card_test.cc`  
**Contributor:** Jingtong Hu ([TNY233](https://github.com/TNY233))  
**AI Tool:** ChatGPT (OpenAI GPT-6)  
**Date:** November 2, 2026  
**Development Branch:** `feature/8-card-data`

## Input

The contributor used ChatGPT to discuss Test-Driven Development (TDD) and generate GoogleTest unit tests for the Card System of *Dragon and the Knight*.

The following are selected prompts provided to ChatGPT, in chronological order:

1. "我理解的tdd都是先写测试然后在写代码 我们不应该是把全部的测试先写完然后继续写实现"

2. "Ok, execute"

3. The contributor provided the resulting C++ test code and requested "check" to review the tests.

Additional context provided to the AI included the existing `Card.h`, `Card.cpp`, `CardType.h`, the GitHub repository, and previously discussed card system requirements.

The contributor specifically requested assistance with writing tests before implementing new features, consistent with the course's TDD requirements.

## Output

### 1. TDD Discussion and Planning

ChatGPT explained the Red-Green-Refactor development cycle and recommended developing one feature at a time.

It identified two categories of tests:

- **Regression testing:** Testing existing Card constructor and getter implementations.
- **Test-first development:** Writing tests for new CardEffect functionality before implementing it.

The AI recommended starting with basic Card data validation, followed by BlockEffect tests for shield management.

### 2. AI-Assisted Test Code

ChatGPT generated and reviewed GoogleTest code for `test/card/card_test.cc`.

Four test cases were included:

| Test | Expected Behavior |
|---|---|
| `CardTest.ConstructorStoresCardData` | Verify that the Card constructor correctly stores the card name, cost, type, and effect value. |
| `BlockEffectTest.AddsShield` | Applying a BlockEffect with value 5 to a player with 0 shield should result in 5 shield. |
| `BlockEffectTest.StacksShield` | Applying a BlockEffect with value 5 twice should result in 10 shield. |
| `BlockEffectTest.ZeroBlockChangesNothing` | Applying a BlockEffect with value 0 should leave the player's shield unchanged. |

The AI used GoogleTest assertions such as `EXPECT_EQ()` to verify the expected behavior.

### 3. Proposed Interfaces

For the BlockEffect tests, ChatGPT proposed the following interfaces:

- `BlockEffect(int amount)` to construct a shield effect.
- `effect.apply(player)` to apply the effect to a player.
- `player.getBlock()` to retrieve the player's current shield.

These interfaces are proposed test specifications and have not yet been confirmed as final project interfaces.

The AI also noted that the project's intended CardEffect system should eventually support both Player and Enemy objects.

### 4. Test Verification Status

| Verification | Status |
|---|---|
| Test code reviewed for GoogleTest syntax | Completed through AI review |
| Card constructor test execution | Not yet run |
| BlockEffect test execution | Not yet run |
| Test build configuration | Not yet completed |
| Production BlockEffect implementation | Not yet completed |
| All four tests passing | Not yet verified |

The current BlockEffect tests depend on interfaces that have not yet been implemented, including `BlockEffect.h` and `Player::getBlock()`.

Therefore, the tests are not currently confirmed to compile or pass.

The Card constructor and getters were implemented before these tests were written. As a result, the Card constructor test is considered a regression test rather than a complete test-first TDD cycle.

The BlockEffect tests were designed before the BlockEffect implementation, as intended for TDD. The initial RED test execution and subsequent GREEN verification are still pending.

## Human Contribution

The contributor was responsible for the design decisions, review, and project integration.

- [x] Independently implemented the existing Card constructor and getter functions.
- [x] Provided the current Card interfaces and implementation details to ChatGPT.
- [x] Selected the Card System as the component to test.
- [x] Requested AI assistance specifically for TDD testing.
- [x] Created the `test/card` directory and worked on `card_test.cc`.
- [x] Reviewed the generated test code and requested corrections.

ChatGPT assisted with test design, GoogleTest code generation, explanations, and code review.

**No AI-generated production implementation code was added as part of this testing activity.**

The contributor remains responsible for writing the production implementation, validating the test results, and integrating the code into the team repository.

## References

- Main project: https://github.com/DragonandtheKnight/Dragon-and-the-Knight
- AI-TDD Transparency repository: https://github.com/DragonandtheKnight/AI-TDD-Transparency
- Existing Card header: https://github.com/DragonandtheKnight/Dragon-and-the-Knight/blob/main/src/card/Card.h
- Test file: `test/card/card_test.cc` (local branch `feature/8-card-data`; commit pending)
- Related commit / Pull Request: Pending

**Note:** This report should be updated after the tests are executed, the BlockEffect interfaces are finalized, and the corresponding implementation is completed.
