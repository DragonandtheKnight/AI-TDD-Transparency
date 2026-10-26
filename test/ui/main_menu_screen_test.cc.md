# AI-TDD Transparency Report

**File:** `test/ui/main_menu_screen_test.cc`  
**Contributor:** Lizi Luo ([ChestnutLUO](https://github.com/ChestnutLUO))  
**AI Tool:** Claude Code (Claude Opus 5.5, `claude-opus-5-5`)

## Input

Prompts given to the AI, verbatim and in order (2026-10-26):

1. > Analyze codebase and based on the tldr file's and README.md let's disscuss TDD for last commit about UI headfile
2. > yes, wire up gtest and draft the main menu test

The AI could read the whole main repository, including the commit under test (`bcf94b5 feat(ui): add UI header design`), `README.md`, `CONTRIBUTING.md`, the `UI Design(1).tldr` and `Class UML.tldr` boards, and the FTXUI and googletest submodules.

## Output

### Discussion (prompt 1)

The AI reviewed commit `bcf94b5` for testability. It made no code changes in this step. It reported:

- The screen factories take data plus a struct of callbacks, which lets tests draw a screen and send key presses without a terminal.
- The repository had no test setup, and several domain headers did not compile (`card/CardType.h` and `card/Deck.h` are missing).
- The UI Design board and the Class UML board disagree in places, including card rarity names, a single enemy versus several targets in battle, and missing getters.
- It suggested a test order: start with screens that have no domain dependencies, beginning with the main menu.

### Test file (prompt 2)

The AI wrote all of `test/ui/main_menu_screen_test.cc`. It has 12 tests for `MakeMainMenuScreen` in `src/ui/main_menu_screen.h`. Expected behaviour comes from the main menu on the UI Design board.

| Test | Behaviour required |
|---|---|
| `MainMenuScreenTest.ShowsGameTitle` | "Dragon And The Knight" is drawn |
| `MainMenuScreenTest.ListsEntriesTopToBottomInBoardOrder` | New Game, Load Game, Setting, Exit Game appear top to bottom |
| `MainMenuScreenTest.MovingTheSelectionChoosesNothing` | Arrow keys never trigger an action |
| `BoardEntries/MainMenuEntryTest.EnterChoosesSelectedEntry/*` (4) | Pressing ArrowDown N times and then Enter calls exactly that entry's callback; New Game is selected on open |
| `BoardEntries/MainMenuEntryTest.ClickChoosesEntry/*` (4) | One left click on an entry's label calls exactly that entry's callback |
| `MainMenuScreenWithoutActionsTest.ChoosingAnEntryDoesNotThrow` | Missing callbacks are harmless (`src/preview/ui_preview.cc` passes `{}`) |

### Test verification performed by the AI

| Check | Result |
|---|---|
| Test file compiles | Yes |
| Test run in the main repo (`cmake --build build --target dragon_knight_tests`, then `ctest --test-dir build`) | **Red, as expected for TDD:** link error `"MakeMainMenuScreen(MainMenuActions)" not found`, because the screen has no implementation yet. `ctest` reports `dragon_knight_tests_NOT_BUILT`. |
| `cpplint test/ui/main_menu_screen_test.cc` | No errors |
| `clang-format --dry-run --Werror` | Clean |

### Throwaway implementation used only to check the tests (not committed)

To confirm the tests can pass and that they catch real mistakes, the AI built them, outside the main repository, against a minimal stand-in for `MakeMainMenuScreen`: four FTXUI buttons in a vertical container under a title. The stand-in was **never added to the main repository** and was deleted right after the check. No AI-written implementation code is in the project.

- With the stand-in, all 12 tests passed.
- When the stand-in's empty-callback guard was removed, `ChoosingAnEntryDoesNotThrow` failed with `std::bad_function_call`, as intended.

## Human Contribution

- Authored the interface under test (`src/ui/main_menu_screen.h`, commit `bcf94b5`) and the main menu design on the UI Design board, which the tests follow.
- Directed the AI (prompts above): asked for the TDD analysis, approved wiring up googletest and drafting the main menu test, and asked for this report and a test run.
- **To complete before merging (contributor):**
  - [x] Read and understood every test above.
  - [x] Confirmed the menu label: the board says "Setting"; the test also accepts "Settings".
  - [ ] Confirmed with the team that screens must tolerate missing callbacks (`ChoosingAnEntryDoesNotThrow`).
  - [ ] Implemented `MakeMainMenuScreen` by hand and saw all 12 tests pass.
  - [ ] Recorded any edits made to the AI-generated tests.

## References

- Commit: https://github.com/DragonandtheKnight/Dragon-and-the-Knight/commit/c91f8425c90d670100ed19564089c01a7746cfd7
- Based on: commit `bcf94b5` (`feat(ui): add UI header design`), branch `UI`
- Related report: [`CMakeLists.txt.md`](../../CMakeLists.txt.md) (googletest wiring that builds this test).
