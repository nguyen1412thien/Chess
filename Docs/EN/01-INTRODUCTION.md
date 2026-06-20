# 01 — Project Introduction

## 1. Context & Concept

`Chess` is a project to build a complete **chess** game using **Unity (C#)**, with the following goals:

- A 3D board (or a simple 2D board first, upgraded to 3D later) visually displayed in Unity.
- Two-player local match (local 2-player/hotseat) — fully complying with FIDE rules.
- Player vs. AI mode with multiple difficulty levels.
- (Extension) Online multiplayer via Unity Netcode / Mirror / Photon in later phases.

The goal is **not just to make it "run"**, but to build a codebase that **clearly separates the game rules logic (pure C#, independent of Unity) from the presentation layer (Unity GameObject/MonoBehaviour)**. This ensures easy testing, high scalability, and reusability of the logic if we want to build a console or non-Unity version in the future.

## 2. Technology Stack

| Category | Selection |
|---|---|
| Engine | Unity (Latest LTS, e.g., Unity 6 / 2022 LTS) |
| Language | C# |
| Input | Unity Input System (New) instead of Legacy Input |
| UI | Unity UI (UGUI) for menus/HUD; UI Toolkit as an alternative |
| Testing | Unity Test Framework (NUnit) for the pure C# logic part |
| Version Control | Git + GitHub |
| (Extension) Multiplayer | Unity Netcode for GameObjects, or Mirror/Photon (decided in Phase 5) |

> Note: Unity supports testing pure C# logic (without `MonoBehaviour`) extremely well using **Edit Mode Tests** — we will leverage this to test chess rules independently of scenes/GameObjects.

## 3. Project Objectives

- **Rules Compliance:** Implement complete FIDE chess rules: piece movement, check, checkmate, draw conditions (stalemate, threefold repetition, 50-move rule, insufficient mating material), castling, en passant, and promotion.
- **Good User Experience:** Click/drag-and-drop pieces, highlight valid moves, show move history, and play effects for capture/check/checkmate.
- **AI Opponent:** Playable AI with selectable difficulties (Easy – Medium – Hard).
- **Clean Architecture:** The game rules logic (`ChessCore`) is pure C# and does **not** reference `UnityEngine`, allowing:
  - Fast execution of Edit Mode Tests (no scene loading required).
  - Easy debugging and refactoring.
  - Reusability when extending to other platforms.

## 4. Project Scope

### In Scope (MVP → Extension)
- 8x8 chessboard with all 6 types of pieces, and correct movement rules for each.
- Detection of check, checkmate, and various draw conditions.
- Castling, en passant, and promotion.
- Local 2-player mode (hotseat — taking turns on the same machine).
- AI opponent mode (Minimax + Alpha-Beta Pruning).
- Unity UI/UX: board, pieces, valid move highlights, move history, and New Game/Undo menu.
- Save/load game state using the FEN standard (useful for both save/load features and testing).

### Out of Initial Scope (Reserved for later phases / Optional)
- Real-time online multiplayer.
- User accounts and rating leaderboards.
- Advanced 3D animations and special effects.
- Integration of external chess engines (like Stockfish) for game analysis.

## 5. Target Audience

- Project Author — serves as a step-by-step implementation checklist in Unity.
- Collaborators/Reviewers — to quickly understand the concept, architecture, and progress.
- AI Coding Assistants (Claude/Antigravity) — serves as the "source of truth" when generating C# scripts for each task.
