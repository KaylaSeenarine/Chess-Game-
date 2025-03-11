# Chess Game

A console-based chess game in Python.

## Features

- Standard chessboard setup with all pieces in their starting positions.
- Basic movement rules for each piece (pawn, rook, knight, bishop, queen, king).
- Turn-based input system.
- Visual grid display with coordinate labels to help you move pieces.

## How to Play

## Run the Game 
   In your terminal, run:
   ```bash
   python chess_game.py

## Viewing the Board 
The board is displayed with row and column numbers. 
      0  1  2  3  4  5  6  7
    ----------------------------
0  |  R  N  B  Q  K  B  N  R
1  |  P  P  P  P  P  P  P  P
2  |  .  .  .  .  .  .  .  .
3  |  .  .  .  .  .  .  .  .
4  |  .  .  .  .  .  .  .  .
5  |  .  .  .  .  .  .  .  .
6  |  p  p  p  p  p  p  p  p
7  |  r  n  b  q  k  b  n  r

## Making a Move 
Moves are entered using four numbers:
start_row start_col end_row end_col

For example, to move the White pawn from (1,4) to (3,4):
Type: 1 4 3 4

## Quit the Game
Type q when prompted to exit.
