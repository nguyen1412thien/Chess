# 04 — Conventions & Definition of Done

## 1. Naming Conventions (C#)

- `PascalCase` for public classes, structs, enums, methods, and properties: `ChessGame`, `TryMakeMove()`.
- `camelCase` for local variables and parameters: `targetSquare`, `legalMoves`.
- `_camelCase` for private fields: `_gameState`.
- Constants: `PascalCase` or `UPPER_SNAKE_CASE` based on team convention — `PascalCase` is recommended to stay aligned with standard C# conventions.
- File Name = Name of the primary class defined in the file (Unity standard).

## 2. Mandatory Architectural Rules

- **No** `using UnityEngine` imports are permitted in any files belonging to `ChessCore` or `ChessAI`. If coordinate or vector representations are needed, define custom types (`Square`, `Coord`) within `ChessCore`. Conversions to Unity's `Vector3` must only occur within the `UnityIntegration` layer.
- All modifications to the `GameState` must pass through `ChessGame.TryMakeMove()` — the `UnityIntegration` layer is never allowed to modify the `Board` representation directly.
- Every new rule implementation (or bug fix) must be backed by a unit test reproducing the scenario prior to code modifications (test-first approach for core rules).

## 3. Git Workflow

- Main branch: `main` — must always compile and remain in a stable, buildable state.
- Each Task in `PHASE-PLAN.md` → a dedicated feature branch: `phase1/task-1.4-move-generator`.
- Concise commit messages with prefixes denoting the type of change:
  - `feat: ...` — new feature
  - `fix: ...` — bug fix
  - `test: ...` — adding/modifying tests
  - `refactor: ...` — code restructuring without behavioral changes
  - `docs: ...` — documentation updates (including files in `Docs/`)
- Upon completing a task, check off the item `[x]` in `PHASE-PLAN.md` within the same commit or PR.

## 4. Testing

- **Edit Mode Tests** are mandatory for all logic within `ChessCore` and `ChessAI`.
- Focus testing on edge cases — these are where chess implementations commonly contain bugs:
  - Castling when the King's path is intercepted by an attacked square.
  - En Passant validation (only legal immediately after the opponent's double-step pawn push).
  - Pawn promotion combined with captures.
  - Pinned pieces: ensuring they cannot move if the move exposes their King to check.
  - Checkmate patterns utilizing various pieces (Queen, Rook, Knight, etc.).
- Use FEN strings from famous game positions as test cases to cross-validate results against standard chess platforms (like lichess.org or chess.com).

## 5. Definition of Done (for each Task)

A task is considered **Done** when:
1. Code compiles without errors or new warnings.
2. Corresponding unit tests are implemented (if in `ChessCore`/`ChessAI`) and all tests pass.
3. Manual verification has been completed in the Unity Editor (if UI-related).
4. The checkbox `[x]` in `PHASE-PLAN.md` has been checked.
5. Code is committed/pushed with a clear message adhering to section 3 guidelines.

## 6. Suggested Coding Session Workflow

1. Open `PHASE-PLAN.md` and select the next uncompleted task in the current phase.
2. Review the relevant sections in `ARCHITECTURE.md` to keep module boundaries in mind.
3. Write the unit tests first (if implementing rules logic) → write code until the tests pass.
4. Run the entire test suite to ensure no regressions occur.
5. Update documentation if any architectural assumptions change.
