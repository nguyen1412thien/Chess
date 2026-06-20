# Chess — Unity & C# Chess Game

Welcome to the **Chess** repository. This is a complete, fully-featured chess game built using **Unity (C#)** designed with a focus on clean code, software engineering best practices, and a strict separation of concerns.

---

## 🌟 Key Architecture & Philosophy

The core design philosophy of this project is the **Separation of Logic and Presentation**. 

Unlike typical Unity games where game logic is tightly coupled with `MonoBehaviour` and GameObjects, this project separates them into distinct boundaries:

1. **`ChessCore` (Pure C#)**: Contains the full implementation of FIDE chess rules, board representation, valid move generation, check/checkmate detection, castling, en passant, promotion, and draw conditions. It has **no dependency** on the Unity engine (`UnityEngine`).
2. **`ChessAI` (Pure C#)**: An independent chess engine implementation using Minimax search and Alpha-Beta pruning, relying only on `ChessCore`.
3. **`UnityIntegration` (Unity Engine)**: Handles user input (clicks/drags), renders the board and pieces, plays sounds and animations, and updates the UI based on the game state.

### 🚀 Benefits
- **Super Fast Testing**: Edit Mode Tests can verify all chess rules and AI logic instantly without needing to load Unity scenes.
- **Portability**: The core chess engine can be easily reused in non-Unity environments (e.g., a dedicated C# headless multiplayer server, console applications).
- **Robustness**: Prevents spaghetti code and visual bugs from corrupting game rules.

---

## 📂 Project Structure

```
Chess/
├── Docs/                               # Project documentation & roadmap
│   ├── README.md                       # This file
│   ├── EN/                             # Detailed English docs
│   │   ├── 01-INTRODUCTION.md          # Concept, goals, and tech stack
│   │   ├── 02-ARCHITECTURE.md          # Data flow and assembly boundaries
│   │   ├── 03-PHASE-PLAN.md            # Detailed task roadmap
│   │   └── 04-CONVENTIONS.md           # Coding standards & git conventions
│   └── VI/                             # Detailed Vietnamese docs (to be added)
│
└── Assets/
    └── Scripts/
        ├── ChessCore/                  # Pure C# Chess Rules Engine
        ├── ChessAI/                    # Pure C# Minimax AI
        ├── UnityIntegration/           # Unity Visuals, Prefabs & UI
        └── Tests/                      # Unit Tests (EditMode & PlayMode)
```

---

## 🗺️ Development Roadmap

Development is organized into the following logical phases:

* **Phase 0 — Project Initialization**: Repository setup, directory structure, assembly definitions (`.asmdef`), and testing framework configurations.
* **Phase 1 — Core Chess Logic (`ChessCore`)**: Pure C# rules verification via unit tests (En Passant, Castling, Promotion, Checkmate, etc.).
* **Phase 2 — Unity UI & Local 2-Player**: Board/piece prefabs, click/drag inputs, highlights, promotion popups, move log, and local hotseat gameplay.
* **Phase 3 — AI Opponent**: Chess position evaluation, Minimax search with Alpha-Beta pruning, and multi-thread integration.
* **Phase 4 — Polish & UX**: Tweening animations, sounds, save/load game state, and UI responsiveness.
* **Phase 5 — Online Multiplayer (Optional)**: Lobby, matchmaking, and authority-backed server validation.

For details, tasks, and criteria of completion, please see the [03-PHASE-PLAN.md](./EN/03-PHASE-PLAN.md).

---

## 🛠️ Getting Started & Conventions

Before contributing or modifying the codebase, please review the rules and coding guidelines in [04-CONVENTIONS.md](./EN/04-CONVENTIONS.md). 
- All core logic code should be developed using a **test-first** approach.
- Maintain strict boundaries between assemblies (`ChessCore` must never import `UnityEngine`).
