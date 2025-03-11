# ============================================
#               PIECE CLASSES
# ============================================

class Piece:
    def __init__(self, color):
        self.color = color  # "white" or "black"

    def __str__(self):
        # By default, show a question mark if not overridden
        return '?'

    def get_valid_moves(self, board, current_position):
        """
        Return a list of (row, col) tuples where this piece can legally move.
        This is overridden by each specific piece type.
        """
        return []


class Pawn(Piece):
    def __init__(self, color):
        super().__init__(color)
        self.symbol = 'P' if color == 'white' else 'p'

    def __str__(self):
        return self.symbol

    def get_valid_moves(self, board, current_position):
        """
        Pawns move one square forward if empty, 
        optionally two squares from the starting row, 
        and capture diagonally.
        """
        valid_moves = []
        row, col = current_position

        # White moves "down" the board (increasing row index),
        # Black moves "up" the board (decreasing row index).
        direction = 1 if self.color == 'white' else -1

        # One step forward
        forward_row = row + direction
        if 0 <= forward_row < 8:
            # If the square directly ahead is empty
            if board.board[forward_row][col] is None:
                valid_moves.append((forward_row, col))

                # Two steps forward if on starting row
                start_row = 1 if self.color == 'white' else 6
                if row == start_row:
                    two_step_row = row + 2 * direction
                    if board.board[two_step_row][col] is None:
                        valid_moves.append((two_step_row, col))

        # Diagonal captures
        for diagonal_col in [col - 1, col + 1]:
            diagonal_row = row + direction
            if 0 <= diagonal_row < 8 and 0 <= diagonal_col < 8:
                piece = board.board[diagonal_row][diagonal_col]
                # If there's an enemy piece, we can capture it
                if piece is not None and piece.color != self.color:
                    valid_moves.append((diagonal_row, diagonal_col))

        return valid_moves


class Rook(Piece):
    def __init__(self, color):
        super().__init__(color)
        self.symbol = 'R' if color == 'white' else 'r'

    def __str__(self):
        return self.symbol

    def get_valid_moves(self, board, current_position):
        """
        Rooks move any number of squares along a rank or file, 
        stopping if they encounter another piece.
        """
        valid_moves = []
        row, col = current_position

        directions = [(1, 0), (-1, 0), (0, 1), (0, -1)]  # down, up, right, left
        for dr, dc in directions:
            r, c = row, col
            while True:
                r += dr
                c += dc
                if not (0 <= r < 8 and 0 <= c < 8):
                    break  # Off the board

                piece = board.board[r][c]
                if piece is None:
                    # Empty square, valid move, keep going
                    valid_moves.append((r, c))
                else:
                    # There's a piece. If it's the enemy, we can capture.
                    if piece.color != self.color:
                        valid_moves.append((r, c))
                    # Stop exploring in this direction after hitting any piece
                    break
        return valid_moves


class Knight(Piece):
    def __init__(self, color):
        super().__init__(color)
        self.symbol = 'N' if color == 'white' else 'n'

    def __str__(self):
        return self.symbol

    def get_valid_moves(self, board, current_position):
        """
        Knights move in an L-shape: 2 squares in one direction and 1 in the perpendicular.
        They can jump over pieces.
        """
        valid_moves = []
        row, col = current_position
        moves = [
            (row + 2, col + 1), (row + 2, col - 1),
            (row - 2, col + 1), (row - 2, col - 1),
            (row + 1, col + 2), (row + 1, col - 2),
            (row - 1, col + 2), (row - 1, col - 2),
        ]

        for (r, c) in moves:
            if 0 <= r < 8 and 0 <= c < 8:
                piece = board.board[r][c]
                # Empty or enemy piece is valid
                if piece is None or piece.color != self.color:
                    valid_moves.append((r, c))

        return valid_moves


class Bishop(Piece):
    def __init__(self, color):
        super().__init__(color)
        self.symbol = 'B' if color == 'white' else 'b'

    def __str__(self):
        return self.symbol

    def get_valid_moves(self, board, current_position):
        """
        Bishops move any number of squares diagonally, 
        stopping if they encounter another piece.
        """
        valid_moves = []
        row, col = current_position

        directions = [(1, 1), (1, -1), (-1, 1), (-1, -1)]  # diagonals
        for dr, dc in directions:
            r, c = row, col
            while True:
                r += dr
                c += dc
                if not (0 <= r < 8 and 0 <= c < 8):
                    break  # Off the board

                piece = board.board[r][c]
                if piece is None:
                    valid_moves.append((r, c))
                else:
                    # Enemy piece can be captured
                    if piece.color != self.color:
                        valid_moves.append((r, c))
                    break
        return valid_moves


class Queen(Piece):
    def __init__(self, color):
        super().__init__(color)
        self.symbol = 'Q' if color == 'white' else 'q'

    def __str__(self):
        return self.symbol

    def get_valid_moves(self, board, current_position):
        """
        Queens move like both rooks and bishops: any number 
        of squares along rank, file, or diagonal.
        """
        valid_moves = []
        row, col = current_position

        directions = [
            (1, 0), (-1, 0), (0, 1), (0, -1),  # rook-like
            (1, 1), (1, -1), (-1, 1), (-1, -1) # bishop-like
        ]
        for dr, dc in directions:
            r, c = row, col
            while True:
                r += dr
                c += dc
                if not (0 <= r < 8 and 0 <= c < 8):
                    break  # Off the board

                piece = board.board[r][c]
                if piece is None:
                    valid_moves.append((r, c))
                else:
                    if piece.color != self.color:
                        valid_moves.append((r, c))
                    break
        return valid_moves


class King(Piece):
    def __init__(self, color):
        super().__init__(color)
        self.symbol = 'K' if color == 'white' else 'k'

    def __str__(self):
        return self.symbol

    def get_valid_moves(self, board, current_position):
        """
        Kings move one square in any direction (8 possible squares),
        as long as it's on the board and not occupied by a friendly piece.
        (No check logic in this simple version.)
        """
        valid_moves = []
        row, col = current_position
        directions = [
            (1, 0), (-1, 0), (0, 1), (0, -1),
            (1, 1), (1, -1), (-1, 1), (-1, -1)
        ]
        for dr, dc in directions:
            r, c = row + dr, col + dc
            if 0 <= r < 8 and 0 <= c < 8:
                piece = board.board[r][c]
                # Empty or enemy piece
                if piece is None or piece.color != self.color:
                    valid_moves.append((r, c))

        return valid_moves


# ============================================
#               BOARD CLASS
# ============================================

class Board:
    def __init__(self):
        self.board = self.create_board()
        self.current_player = 'white'  # Track whose turn it is

    def create_board(self):
        """
        Create an 8x8 grid and place all pieces in standard positions.
        """
        board = [[None for _ in range(8)] for _ in range(8)]

        # --- Place White Pieces ---
        board[0][0] = Rook('white')
        board[0][1] = Knight('white')
        board[0][2] = Bishop('white')
        board[0][3] = Queen('white')
        board[0][4] = King('white')
        board[0][5] = Bishop('white')
        board[0][6] = Knight('white')
        board[0][7] = Rook('white')

        for col in range(8):
            board[1][col] = Pawn('white')

        # --- Place Black Pieces ---
        board[7][0] = Rook('black')
        board[7][1] = Knight('black')
        board[7][2] = Bishop('black')
        board[7][3] = Queen('black')
        board[7][4] = King('black')
        board[7][5] = Bishop('black')
        board[7][6] = Knight('black')
        board[7][7] = Rook('black')

        for col in range(8):
            board[6][col] = Pawn('black')

        return board

    def display(self):
        """
        Print the board in a simple text format.
        """
        for row in self.board:
            print(" ".join(str(piece) if piece else '.' for piece in row))
        print()  # Blank line after the board

    def make_move(self, start_row, start_col, end_row, end_col):
        """
        Attempt to move the piece from (start_row, start_col) to (end_row, end_col).
        Returns True if the move is successful, False otherwise.
        """
        # 1) Check if start position is valid
        if not (0 <= start_row < 8 and 0 <= start_col < 8):
            print("Invalid start position.")
            return False

        piece = self.board[start_row][start_col]
        if piece is None:
            print("No piece at the start position!")
            return False

        # 2) Check if it’s the current player's piece
        if piece.color != self.current_player:
            print(f"It is {self.current_player}'s turn, not {piece.color}'s turn!")
            return False

        # 3) Generate valid moves for this piece
        valid_moves = piece.get_valid_moves(self, (start_row, start_col))

        # 4) Check if end position is in the piece’s valid moves
        if (end_row, end_col) not in valid_moves:
            print("Invalid move for this piece.")
            return False

        # 5) If valid, move the piece
        self.board[end_row][end_col] = piece
        self.board[start_row][start_col] = None

        # 6) Switch the current player
        self.current_player = 'black' if self.current_player == 'white' else 'white'
        return True


# ============================================
#           MAIN GAME LOOP
# ============================================

if __name__ == '__main__':
    game_board = Board()

    while True:
        game_board.display()
        print(f"{game_board.current_player}'s turn.")

        move_input = input("Enter your move (start_row start_col end_row end_col), or 'q' to quit: ")
        if move_input.lower() == 'q':
            print("Exiting game.")
            break

        # Parse the input
        try:
            start_row, start_col, end_row, end_col = map(int, move_input.split())
        except ValueError:
            print("Invalid input format. Please enter four integers.")
            continue

        # Attempt the move
        success = game_board.make_move(start_row, start_col, end_row, end_col)
        if not success:
            print("Move failed. Try again.")
