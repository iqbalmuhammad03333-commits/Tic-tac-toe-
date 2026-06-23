# Tic-tac-toe-
TIC TAC TOE GAME - GUI FORM VERSION
Using Tkinter for graphical interface
With Minimax Algorithm for AI opponent
"""

import tkinter as tk
from tkinter import messagebox
import time
from typing import Optional, List


class TicTacToeAI:
    """AI player using Minimax algorithm"""
    
    def __init__(self, difficulty: int = 5):
        self.difficulty = difficulty
    
    def get_best_move(self, board: List[str]) -> Optional[int]:
        """Get the best move using Minimax"""
        best_score = float('-inf')
        best_move = None
        
        for i in range(9):
            if board[i] == ' ':
                board[i] = 'O'
                score = self.minimax(board, 0, False)
                board[i] = ' '
                
                if score > best_score:
                    best_score = score
                    best_move = i
        
        return best_move
    
    def minimax(self, board: List[str], depth: int, is_maximizing: bool) -> int:
        """Minimax algorithm"""
        winner = self.check_winner(board)
        
        if winner == 'O':
            return 10 - depth
        elif winner == 'X':
            return depth - 10
        elif self.is_board_full(board):
            return 0
        
        if depth >= self.difficulty:
            return 0
        
        if is_maximizing:
            max_score = float('-inf')
            for i in range(9):
                if board[i] == ' ':
                    board[i] = 'O'
                    score = self.minimax(board, depth + 1, False)
                    board[i] = ' '
                    max_score = max(score, max_score)
            return max_score
        else:
            min_score = float('inf')
            for i in range(9):
                if board[i] == ' ':
                    board[i] = 'X'
                    score = self.minimax(board, depth + 1, True)
                    board[i] = ' '
                    min_score = min(score, min_score)
            return min_score
    
    @staticmethod
    def check_winner(board: List[str]) -> Optional[str]:
        """Check if there's a winner"""
        winning_combinations = [
            [0, 1, 2], [3, 4, 5], [6, 7, 8],  # Rows
            [0, 3, 6], [1, 4, 7], [2, 5, 8],  # Columns
            [0, 4, 8], [2, 4, 6]               # Diagonals
        ]
        
        for combo in winning_combinations:
            if board[combo[0]] == board[combo[1]] == board[combo[2]] != ' ':
                return board[combo[0]]
        
        return None
    
    @staticmethod
    def is_board_full(board: List[str]) -> bool:
        """Check if board is full"""
        return ' ' not in board


class TicTacToeGUI:
    """GUI for Tic Tac Toe game"""
    
    def __init__(self, root):
        self.root = root
        self.root.title("Tic Tac Toe - Human vs AI")
        self.root.geometry("600x700")
        self.root.configure(bg='#2C3E50')
        self.root.resizable(False, False)
        
        self.board = [' ' for _ in range(9)]
        self.ai = TicTacToeAI(difficulty=5)
        self.buttons = []
        self.game_over = False
        self.human_wins = 0
        self.ai_wins = 0
        self.draws = 0
        
        self.setup_ui()
    
    def setup_ui(self):
        """Setup the user interface"""
        # Title
        title_frame = tk.Frame(self.root, bg='#2C3E50')
        title_frame.pack(pady=15)
        
        title = tk.Label(
            title_frame,
            text="♟ TIC TAC TOE ♟",
            font=("Arial", 28, "bold"),
            bg='#2C3E50',
            fg='#3498DB'
        )
        title.pack()
        
        subtitle = tk.Label(
            title_frame,
            text="Human (X) vs AI (O)",
            font=("Arial", 12),
            bg='#2C3E50',
            fg='#ECF0F1'
        )
        subtitle.pack()
        
        # Game Board Frame
        board_frame = tk.Frame(self.root, bg='#2C3E50')
        board_frame.pack(pady=10)
        
        # Create 3x3 button grid
        for i in range(9):
            btn = tk.Button(
                board_frame,
                text='',
                font=("Arial", 24, "bold"),
                width=6,
                height=3,
                bg='#ECF0F1',
                fg='#000',
                activebackground='#3498DB',
                command=lambda pos=i: self.human_move(pos),
                relief=tk.RAISED,
                bd=2
            )
            
            row = i // 3
            col = i % 3
            btn.grid(row=row, column=col, padx=5, pady=5)
            self.buttons.append(btn)
        
        # Status Frame
        status_frame = tk.Frame(self.root, bg='#2C3E50')
        status_frame.pack(pady=10)
        
        self.status_label = tk.Label(
            status_frame,
            text="Your turn (X)",
            font=("Arial", 14, "bold"),
            bg='#2C3E50',
            fg='#2ECC71'
        )
        self.status_label.pack()
        
        # Statistics Frame
        stats_frame = tk.Frame(self.root, bg='#34495E')
        stats_frame.pack(fill=tk.X, padx=20, pady=10)
        
        self.stats_label = tk.Label(
            stats_frame,
            text="You: 0 | AI: 0 | Draws: 0",
            font=("Arial", 11),
            bg='#34495E',
            fg='#ECF0F1'
        )
        self.stats_label.pack(pady=8)
        
        # Buttons Frame
        buttons_frame = tk.Frame(self.root, bg='#2C3E50')
        buttons_frame.pack(pady=15)
        
        new_game_btn = tk.Button(
            buttons_frame,
            text="New Game",
            font=("Arial", 11, "bold"),
            bg='#3498DB',
            fg='white',
            padx=15,
            pady=8,
            command=self.new_game,
            cursor="hand2"
        )
        new_game_btn.pack(side=tk.LEFT, padx=5)
        
        reset_stats_btn = tk.Button(
            buttons_frame,
            text="Reset Stats",
            font=("Arial", 11, "bold"),
            bg='#E74C3C',
            fg='white',
            padx=15,
            pady=8,
            command=self.reset_stats,
            cursor="hand2"
        )
        reset_stats_btn.pack(side=tk.LEFT, padx=5)
        
        quit_btn = tk.Button(
            buttons_frame,
            text="Quit",
            font=("Arial", 11, "bold"),
            bg='#95A5A6',
            fg='white',
            padx=15,
            pady=8,
            command=self.root.quit,
            cursor="hand2"
        )
        quit_btn.pack(side=tk.LEFT, padx=5)
    
    def human_move(self, position: int):
        """Handle human move"""
        if self.game_over:
            messagebox.showinfo("Game Over", "Game is over! Click 'New Game' to play again.")
            return
        
        if self.board[position] != ' ':
            messagebox.showwarning("Invalid Move", "Position already taken!")
            return
        
        self.board[position] = 'X'
        self.update_button(position, 'X')
        
        # Check if human won
        if self.check_winner('X'):
            self.end_game("You Win! 🎉")
            self.human_wins += 1
            return
        
        # Check for draw
        if ' ' not in self.board:
            self.end_game("Draw! 🤝")
            self.draws += 1
            return
        
        # AI's turn
        self.status_label.config(text="AI thinking...", fg='#F39C12')
        self.root.update()
        time.sleep(1)
        
        ai_move = self.ai.get_best_move(self.board)
        if ai_move is not None:
            self.board[ai_move] = 'O'
            self.update_button(ai_move, 'O')
            
            # Check if AI won
            if self.check_winner('O'):
                self.end_game("AI Wins! 😔")
                self.ai_wins += 1
                return
            
            # Check for draw
            if ' ' not in self.board:
                self.end_game("Draw! 🤝")
                self.draws += 1
                return
        
        self.status_label.config(text="Your turn (X)", fg='#2ECC71')
    
    def update_button(self, position: int, player: str):
        """Update button display"""
        self.buttons[position].config(text=player)
        if player == 'X':
            self.buttons[position].config(fg='#3498DB')
        else:
            self.buttons[position].config(fg='#E74C3C')
        self.buttons[position].config(state='disabled')
    
    def check_winner(self, player: str) -> bool:
        """Check if player won"""
        winning_combinations = [
            [0, 1, 2], [3, 4, 5], [6, 7, 8],  # Rows
            [0, 3, 6], [1, 4, 7], [2, 5, 8],  # Columns
            [0, 4, 8], [2, 4, 6]               # Diagonals
        ]
        
        for combo in winning_combinations:
            if all(self.board[i] == player for i in combo):
                # Highlight winning combination
                for i in combo:
                    self.buttons[i].config(bg='#2ECC71')
                return True
        
        return False
    
    def end_game(self, message: str):
        """End the game"""
        self.game_over = True
        self.status_label.config(text=message, fg='#F39C12')
        
        # Disable all buttons
        for btn in self.buttons:
            btn.config(state='disabled')
        
        self.update_stats()
    
    def new_game(self):
        """Start a new game"""
        self.board = [' ' for _ in range(9)]
        self.game_over = False
        
        for btn in self.buttons:
            btn.config(
                text='',
                state='normal',
                bg='#ECF0F1',
                fg='#000'
            )
        
        self.status_label.config(text="Your turn (X)", fg='#2ECC71')
    
    def reset_stats(self):
        """Reset game statistics"""
        if messagebox.askyesno("Reset", "Reset all statistics?"):
            self.human_wins = 0
            self.ai_wins = 0
            self.draws = 0
            self.update_stats()
    
    def update_stats(self):
        """Update statistics display"""
        stats_text = f"You: {self.human_wins} | AI: {self.ai_wins} | Draws: {self.draws}"
        self.stats_label.config(text=stats_text)


def main():
    """Main entry point"""
    root = tk.Tk()
    gui = TicTacToeGUI(root)
    root.mainloop()


if __name__ == "__main__":
    main()
    
