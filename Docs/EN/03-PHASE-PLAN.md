# 03 — Phase & Task Plan

> Status Checklist: `[ ]` Not started, `[x]` Completed. Each Task should ideally be implemented as a separate commit or Pull Request.

---

## Phase 0 — Project Initialization

**Goal:** Get a working Unity project running with the correct directory structure, ready for coding.

- [ ] **Task 0.1** — Create a Unity project (choose a 3D or 2D core template depending on how you wish to display the chessboard).
- [ ] **Task 0.2** — Create the directory structure `Assets/Scripts/{ChessCore, ChessAI, UnityIntegration, Tests}` as described in `02-ARCHITECTURE.md`.
- [ ] **Task 0.3** — Create three `.asmdef` files: `ChessCore.asmdef`, `ChessAI.asmdef` (references `ChessCore`), and the default assembly for `UnityIntegration` (references both of the above).
- [ ] **Task 0.4** — Install the Unity Test Framework and create a dummy test to ensure Edit Mode Tests are operational.
- [ ] **Task 0.5** — Initialize a Git repository (if not already done) and add a standard `.gitignore` for Unity (ignoring `Library/`, `Temp/`, `Logs/`, `*.csproj`, etc.).

**Phase 0 Criteria for Completion:** Unity opens without errors, running the Test Runner shows the dummy test passing, and code is successfully pushed to GitHub.

---

## Phase 1 — Chess Game Core (`ChessCore`)

**Goal:** Implement all chess rules correctly, verified via unit tests — **no UI required yet**.

- [ ] **Task 1.1** — Define `PieceType`, `Color`, `Piece`, and `Square` (immutable structs/enums).
- [ ] **Task 1.2** — Define `Board` — represented by a `Piece?[8,8]` array, with a constructor for the standard starting position.
- [ ] **Task 1.3** — Define `GameState` (tracks turn, castling rights, en passant target, half-move clock, full-move number, history).
- [ ] **Task 1.4** — `MoveGenerator`: Generate **pseudo-legal** moves for each piece type (Pawn, Knight, Bishop, Rook, Queen, King) — check validations that prevent the King from being checked are not required yet.
- [ ] **Task 1.5** — `Rules.IsSquareAttacked()` — Check if a square is under attack by the opponent (fundamental for check detection).
- [ ] **Task 1.6** — Filter out invalid moves to find true **legal** moves: eliminate any moves that leave one's own King in check.
- [ ] **Task 1.7** — Detect **Check** (King is under attack).
- [ ] **Task 1.8** — Detect **Checkmate** (King is in check + no legal moves remain).
- [ ] **Task 1.9** — Detect **Stalemate** (active player has no legal moves but is not in check).
- [ ] **Task 1.10** — Implement **Castling** rules: validation conditions (King/Rook have not moved, path is empty, King does not pass through or land on a square under attack).
- [ ] **Task 1.11** — Implement **En Passant** (capturing a pawn in passing).
- [ ] **Task 1.12** — Implement **Promotion** rules (promoting a Pawn upon reaching the back rank).
- [ ] **Task 1.13** — Implement additional draw rules: **threefold repetition**, **50-move rule**, and **insufficient mating material** (King vs. King, King+Bishop vs. King, etc.).
- [ ] **Task 1.14** — `ChessGame` — Unified API: `TryMakeMove(Move)`, `GetLegalMoves(Square)`, `GetGameResult()`, along with events `OnMoveMade`, `OnCheck`, `OnGameOver`.
- [ ] **Task 1.15** — `FenSerializer` — Read and write FEN format strings (highly useful for quick debugging, testing, and importing positions from lichess.org).

**Phase 1 Criteria for Completion:**
- All tasks are backed by Edit Mode Tests (especially testing classic mating patterns/draw setups, e.g., Fool's Mate, Scholar's Mate, stalemate positions).
- A complete game can be played entirely via code/console output (no UI) without violating any rules.

---

## Phase 2 — Unity User Interface (Local 2-Player Mode)

**Goal:** Players can see the chessboard, and click or drag pieces to move them according to `ChessCore` rules.

- [ ] **Task 2.1** — Create a chessboard prefab (light/dark squares) and chess piece prefabs (6 types × 2 colors, utilizing assets or temporary primitives).
- [ ] **Task 2.2** — `BoardView`: Instantiate the chessboard and pieces dynamically in the scene based on `GameState.Board`, synchronizing visual elements on the `OnMoveMade` event.
- [ ] **Task 2.3** — `InputController`: Raycast to capture clicks on pieces/squares and resolve them into a `Square`.
- [ ] **Task 2.4** — Select a piece to highlight **valid target squares** (`MoveHighlighter`) using `ChessGame.GetLegalMoves()`.
- [ ] **Task 2.5** — Move pieces by clicking on highlighted valid squares → call `ChessGame.TryMakeMove()` → update UI.
- [ ] **Task 2.6** — `PromotionUI`: Display a popup selection menu (Queen/Rook/Bishop/Knight) when a Pawn is promoted.
- [ ] **Task 2.7** — Show Check/Checkmate/Stalemate status on the UI (e.g., overlay banner or highlighting the King red).
- [ ] **Task 2.8** — `MoveHistoryUI`: Renders the move log in standard algebraic notation (e.g., `e4`, `Nf3`, `O-O`).
- [ ] **Task 2.9** — Menu options: New Game, Undo move, and displaying victory/loss/draw screen when the game ends.

**Phase 2 Criteria for Completion:** Two players can play a complete chess match on the same computer using the Unity interface, adhering fully to the rules, without looking at the console or code.

---

## Phase 3 — AI Opponent

**Goal:** Players can choose to play against an AI opponent with selectable difficulty levels.

- [ ] **Task 3.1** — `BoardEvaluator`: Basic board evaluation based on piece material values (Pawn=1, Knight/Bishop=3, Rook=5, Queen=9).
- [ ] **Task 3.2** — Positional evaluation: Integrate Piece-Square Tables (PST) so the AI prioritizes controlling the center.
- [ ] **Task 3.3** — `MinimaxSearcher`: Implement a basic Minimax search algorithm with fixed depth (e.g., 2-3 ply).
- [ ] **Task 3.4** — Implement **Alpha-Beta Pruning** to optimize the search space and allow deeper search.
- [ ] **Task 3.5** — `DifficultySettings`: Map difficulty levels (Easy/Medium/Hard) to search depths + introduce noise/randomness (e.g., Easy mode occasionally plays suboptimal moves).
- [ ] **Task 3.6** — Unity integration: Run AI search on a background thread or via coroutine to prevent freezing the main Unity UI thread.
- [ ] **Task 3.7** — Menu updates: Allow choosing "2 Players" vs. "Play vs. AI" along with the difficulty slider.

**Phase 3 Criteria for Completion:** Player can select "Play vs. AI", and the AI makes reasonable moves within an acceptable response time (<2-3 seconds on Hard), never playing illegal moves.

---

## Phase 4 — Polish & UX Enhancements

**Goal:** Clean up the look and feel, making the game ready for demo/sharing.

- [ ] **Task 4.1** — Sound effects: piece movement, captures, checks, game over.
- [ ] **Task 4.2** — Smooth piece animations: Use tweening rather than instant snapping during movement.
- [ ] **Task 4.3** — Save/Load feature: Serialize game state to a local file or PlayerPrefs using `FenSerializer`.
- [ ] **Task 4.4** — PGN Import/Export: Basic chess game logs (optional).
- [ ] **Task 4.5** — Responsive UI layouts for different resolutions and aspect ratios.
- [ ] **Task 4.6** — Board rotation: Automatically rotate the board view based on whose turn it is (optional).
- [ ] **Task 4.7** — Standalone builds: Build and run test executables (Windows/Mac/WebGL) to verify performance.

**Phase 4 Criteria for Completion:** A stable, polished build that runs smoothly without placeholder art or audio assets.

---

## Phase 5 — (Optional / Future) Online Multiplayer

**Goal:** Allow two players to connect and play online.

- [ ] **Task 5.1** — Choose networking solution (Unity Netcode for GameObjects / Mirror / Photon Fusion).
- [ ] **Task 5.2** — Matchmaking/Lobby: Join/create rooms using invite codes.
- [ ] **Task 5.3** — Synchronize moves over the network, using the server-side `ChessCore` as the authority to prevent cheating.
- [ ] **Task 5.4** — Handle disconnection, resignation, and draw offers.
- [ ] **Task 5.5** — (Beyond MVP) Accounts, ELO ratings, and leaderboards.

**Note:** This phase requires hosting/server infrastructure and involves additional complexity and potential costs. Evaluate the necessity of this step carefully.

---

## Progress Overview (Manual Update)

| Phase | Status |
|---|---|
| Phase 0 — Initialization | ⬜ Not started |
| Phase 1 — Core Logic | ⬜ Not started |
| Phase 2 — Local 2-Player UI | ⬜ Not started |
| Phase 3 — AI Opponent | ⬜ Not started |
| Phase 4 — Polish | ⬜ Not started |
| Phase 5 — Online (Optional) | ⬜ Not started |
