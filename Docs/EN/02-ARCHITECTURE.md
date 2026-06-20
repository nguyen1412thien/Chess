# 02 — System Architecture (Unity / C#)

## 1. Design Principles

1. **Separation of Logic and Presentation:** The chess game rules reside entirely within pure C# classes (`ChessCore`), with **no** `using UnityEngine` and **no** inheritance from `MonoBehaviour`. The Unity side (`ChessUnity` / `UnityIntegration`) is only responsible for:
   - Rendering the board/pieces based on the state provided by `ChessCore`.
   - Handling user inputs, converting them to `Move` objects, and sending them to `ChessCore` for processing.
2. **Single Source of Truth:** `GameState` within `ChessCore` represents the actual state of the game. Unity only rerenders the board to match this state and does not maintain its own independent state.
3. **Dedicated Assembly Definitions (`.asmdef`):** Separate `.asmdef` files are set up for `ChessCore` to: (a) strictly prevent accidental `using UnityEngine` imports, (b) speed up compilation times, and (c) allow Edit Mode Tests to run independently.
4. **Events/Actions:** Use C# events/actions to notify Unity of core updates (e.g., `OnMoveMade`, `OnCheck`, `OnCheckmate`, `OnGameOver`) instead of Unity polling the core state continuously.

## 2. Proposed Directory Structure (Unity Project)

```
Chess/                                  # Unity project root
├── Docs/                               # Documentation (this folder)
├── Assets/
│   ├── Scripts/
│   │   ├── ChessCore/                  # ★ Pure C#, NO UnityEngine dependencies
│   │   │   ├── ChessCore.asmdef
│   │   │   ├── Board.cs                # Represents the 8x8 board
│   │   │   ├── Piece.cs                # Piece struct/class (Type, Color)
│   │   │   ├── Move.cs                 # Move struct (From, To, Promotion, flags)
│   │   │   ├── GameState.cs            # Chess game state (turn, castling rights, en passant...)
│   │   │   ├── MoveGenerator.cs        # Generates legal moves for each piece
│   │   │   ├── Rules.cs                # Rules for Check/Checkmate/Stalemate/Draw
│   │   │   ├── SpecialMoves.cs         # Castling, En Passant, Promotion
│   │   │   ├── FenSerializer.cs        # FEN import/export
│   │   │   └── ChessGame.cs            # Main API: TryMakeMove(), GetLegalMoves()...
│   │   │
│   │   ├── ChessAI/                    # ★ Pure C#, depends on ChessCore
│   │   │   ├── ChessAI.asmdef
│   │   │   ├── BoardEvaluator.cs       # Board evaluation function
│   │   │   ├── MinimaxSearcher.cs      # Minimax + Alpha-Beta Pruning
│   │   │   └── DifficultySettings.cs   # Search depth configuration based on difficulty
│   │   │
│   │   ├── UnityIntegration/           # Unity-specific implementation (MonoBehaviours)
│   │   │   ├── GameManager.cs          # Initializes ChessGame, manages turns, calls AI
│   │   │   ├── BoardView.cs            # Renders board, synchronizes with GameState
│   │   │   ├── PieceView.cs            # Component attached to piece prefabs
│   │   │   ├── InputController.cs      # Captures clicks/drags, raycasts to select squares
│   │   │   ├── MoveHighlighter.cs      # Highlights valid target squares
│   │   │   ├── PromotionUI.cs          # Selection popup for pawn promotion
│   │   │   └── MoveHistoryUI.cs        # Renders the moves list
│   │   │
│   │   └── Tests/
│   │       ├── EditMode/               # Tests ChessCore & ChessAI (without loading scenes)
│   │       │   ├── MoveGeneratorTests.cs
│   │       │   ├── CheckmateTests.cs
│   │       │   ├── SpecialMovesTests.cs
│   │       │   └── FenSerializerTests.cs
│   │       └── PlayMode/               # Integration tests with scenes (basic UI interaction)
│   │
│   ├── Prefabs/                        # Piece, square, and UI prefabs
│   ├── Materials/
│   ├── Scenes/
│   │   ├── MainMenu.unity
│   │   └── Game.unity
│   └── Art/                            # Chess models/sprites and board assets
│
├── ProjectSettings/
└── Packages/
```

## 3. Data Flow

```
[Player clicks square/piece]
            │
            ▼
   InputController (Unity) — raycasts to identify selected Square
            │
            ▼
   GameManager translates to Move, then calls:
        chessGame.TryMakeMove(move)
            │
            ▼
   ChessCore.ChessGame checks validity via MoveGenerator + Rules
            │  (if valid)
            ▼
   GameState is updated (new board representation, turn changed, history, etc.)
            │
            ▼
   ChessGame fires event: OnMoveMade(move, newState)
            │
            ▼
   BoardView / PieceView / MoveHistoryUI (Unity) listen to event → updates visuals
            │
            ▼
   If playing against AI and it is the AI's turn:
        GameManager calls ChessAI.MinimaxSearcher.FindBestMove(gameState)
            │
            ▼
        Repeats the TryMakeMove() flow with the AI's chosen move
```

## 4. Core Data Model (Concept, in `ChessCore`)

```csharp
public enum Color { White, Black }
public enum PieceType { None, Pawn, Knight, Bishop, Rook, Queen, King }

public readonly struct Piece
{
    public readonly PieceType Type;
    public readonly Color Color;
    public Piece(PieceType type, Color color) { Type = type; Color = color; }
}

public readonly struct Square
{
    public readonly int File; // 0-7 (a-h)
    public readonly int Rank; // 0-7 (1-8)
}

public readonly struct Move
{
    public readonly Square From;
    public readonly Square To;
    public readonly PieceType Promotion;   // None if not promoting
    public readonly bool IsCastling;
    public readonly bool IsEnPassant;
}

public class GameState
{
    public Piece?[,] Board;              // [8,8]
    public Color Turn;
    public CastlingRights Castling;
    public Square? EnPassantTarget;
    public int HalfMoveClock;            // for 50-move rule
    public int FullMoveNumber;
    public List<Move> History;
}
```

## 5. Assembly Boundaries

| Assembly | Allowed References | NOT Allowed References |
|---|---|---|
| `ChessCore` | (None — pure C#) | `UnityEngine`, `ChessAI`, `UnityIntegration` |
| `ChessAI` | `ChessCore` | `UnityEngine`, `UnityIntegration` |
| `UnityIntegration` | `ChessCore`, `ChessAI`, `UnityEngine` | — |

These boundaries are **mandatory across the project**, enforced via `.asmdef` files from Phase 1. This separation is crucial for:
- Getting extremely fast Edit Mode Tests execution (no Unity scene loading).
- Supporting future expansion to Online Multiplayer (the server can run `ChessCore` as a headless .NET console application without Unity).

## 6. Architecture for Online Expansion (For reference in later phases)

```
Unity Client (UnityIntegration + ChessCore for display, local validation for smooth UX)
        │  Unity Netcode / Mirror / Photon
        ▼
Server (can be a Unity Dedicated Server or a .NET console app reusing ChessCore)
        │  ChessCore maintains the true "Single Source of Truth" of the game
        ▼
Synchronizes state between clients and prevents cheating (validates moves on server)
```
