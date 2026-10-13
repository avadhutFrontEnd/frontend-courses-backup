---
title: "Build: Tic-Tac-Toe Game"
source: https://www.geekyfrontend.in/learn/react-interview-kit/build-tic-tac-toe-game
course: React Interview Kit
tags: [geeky-frontend, react-interview-kit]
---

# Build: Tic-Tac-Toe Game

*2 min read · Updated 23 Apr 2026 Topic 60 of 62 Bookmark Mark done Problem · Topic 60 of 62*

**Source:** [https://www.geekyfrontend.in/learn/react-interview-kit/build-tic-tac-toe-game](https://www.geekyfrontend.in/learn/react-interview-kit/build-tic-tac-toe-game)

---

**Problem:** Build a playable Tic-Tac-Toe game with win detection and reset.

**What This Tests:** 2D state management, game logic, derived state (winner), conditional rendering.

jsx

jsx

```
function TicTacToe() {
  const [board, setBoard] = useState(Array(9).fill(null));
  const [isXTurn, setIsXTurn] = useState(true);

  const winner = calculateWinner(board);
  const isDraw = !winner && board.every(cell => cell !== null);

  const handleClick = (index) => {
    if (board[index] || winner) return;
    const newBoard = [...board];
    newBoard[index] = isXTurn ? 'X' : 'O';
    setBoard(newBoard);
    setIsXTurn(!isXTurn);
  };

  const reset = () => {
    setBoard(Array(9).fill(null));
    setIsXTurn(true);
  };

  let status;
  if (winner) status = `Winner: ${winner}`;
  else if (isDraw) status = "It's a draw!";
  else status = `Next player: ${isXTurn ? 'X' : 'O'}`;

  return (
    <div style={{ textAlign: 'center' }}>
      <h2>{status}</h2>
      <div style={{ display: 'grid', gridTemplateColumns: 'repeat(3, 80px)', gap: 4, justifyContent: 'center' }}>
        {board.map((cell, i) => (
          <button
            key={i}
            onClick={() => handleClick(i)}
            style={{ width: 80, height: 80, fontSize: 28, fontWeight: 'bold', cursor: cell || winner ? 'default' : 'pointer' }}
          >
            {cell}
          </button>
        ))}
      </div>
      <button onClick={reset} style={{ marginTop: 16 }}>Reset Game</button>
    </div>
  );
}

function calculateWinner(board) {
  const lines = [
    [0,1,2], [3,4,5], [6,7,8],
    [0,3,6], [1,4,7], [2,5,8],
    [0,4,8], [2,4,6],
  ];
  for (const [a, b, c] of lines) {
    if (board[a] && board[a] === board[b] && board[a] === board[c]) {
      return board[a];
    }
  }
  return null;
}
```

**Key Concepts:** Flat array for 2D grid, derived state for winner and draw, preventing clicks on filled cells or after game ends, pure function for win detection, CSS Grid for layout.

---

## Learn More

- [Tutorial: Tic-Tac-Toe (official) react.dev](https://react.dev/learn/tutorial-tic-tac-toe)
- [Build tic-tac-toe in React YouTube](https://www.youtube.com/watch?v=4Gt_YyGf6B0)
- [Master React by building tic-tac-toe YouTube](https://www.youtube.com/watch?v=ZF4hD1ffitU)
- [Tic-tac-toe — beginners tutorial YouTube](https://www.youtube.com/watch?v=c8dXnuVwmA8)
