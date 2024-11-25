# Minesweeper Game

 **Language: [English](README.md), [中文](README_zh.md)**

## Project Overview
This is a Minesweeper game built with Vue.js and Vite. The game features preset difficulty levels, customizable grid dimensions and mine counts, and supports left-click, right-click (or long-press) marking, as well as touch operations.

## Technology Stack
- **Frontend Framework**: Vue.js
- **Build Tool**: Vite
- **Programming Language**: JavaScript
- **Styling**: CSS Grid Layout
## Features
1. **Preset Difficulty Levels**:
   - Beginner: 9x9 grid with 10 mines
   - Intermediate: 16x16 grid with 40 mines
   - Advanced: 16x30 grid with 99 mines
   - Expert: 30x30 grid with 180 mines
   - Master: 42x30 grid with 230 mines
2. **Custom Settings**:
   - Players can customize the number of rows, columns, and mines for the game.
3. **Left-Click Functionality**:
   - Clicking an unrevealed cell with the left mouse button will reveal the cell and the number of adjacent mines if the cell is safe.
   - Clicking on a mine will result in game restart and reveal all mines.
4. **Right-Click (or Long-Press) Functionality**:
   - Right-clicking (or long-pressing) an unrevealed cell allows players to flag it. Right-clicking (or long-pressing) again will unflag it.
5. **Win Condition**:
   - The game is won when all non-mine cells are revealed.
## How to Use
1. **Clone the Project**:
   ```bash
    git clone https://github.com/tsrdzy/minesweeper
    cd minesweeper
2. **Install Dependencies**:
   ```bash
    npm install
3. **Run the Project**:
   ```bash
    npm run dev
4. **Access the Project**:
   ```bash
    Open your browser and visit http://localhost:3000 (or whatever local server address Vite provides).