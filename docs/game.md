# 2048游戏
```jsx
/**
 * inline: true
 */

import React, { useState, useEffect } from 'react';

const App = () => {
  const createBoard = () => {
    const board = Array(4)
      .fill(null)
      .map(() => Array(4).fill(0));
    addRandomTile(board);
    addRandomTile(board);
    return board;
  };

  const addRandomTile = (board) => {
    const emptyCells = [];
    board.forEach((row, i) => {
      row.forEach((cell, j) => {
        if (cell === 0) emptyCells.push({ i, j });
      });
    });
    const { i, j } = emptyCells[Math.floor(Math.random() * emptyCells.length)];
    board[i][j] = Math.random() < 0.9 ? 2 : 4;
  };

  const mergeTiles = (row) => {
    const newRow = row.filter((cell) => cell !== 0);
    for (let i = 0; i < newRow.length - 1; i++) {
      if (newRow[i] === newRow[i + 1]) {
        newRow[i] *= 2;
        newRow.splice(i + 1, 1);
      }
    }
    return [...newRow, ...Array(4 - newRow.length).fill(0)];
  };

  const move = (board, direction) => {
    const newBoard = board.map((row) => [...row]);
    let moved = false;

    if (direction === 'left' || direction === 'right') {
      newBoard.forEach((row, i) => {
        const originalRow = [...row];
        newBoard[i] =
          direction === 'left'
            ? mergeTiles(row)
            : mergeTiles(originalRow.reverse()).reverse();
        if (JSON.stringify(originalRow) !== JSON.stringify(newBoard[i]))
          moved = true;
      });
    } else {
      for (let j = 0; j < 4; j++) {
        const originalCol = newBoard.map((row) => row[j]);
        const newCol =
          direction === 'up'
            ? mergeTiles(originalCol)
            : mergeTiles(originalCol.reverse()).reverse();
        for (let i = 0; i < 4; i++) {
          if (newBoard[i][j] !== newCol[i]) moved = true;
          newBoard[i][j] = newCol[i];
        }
      }
    }

    if (moved) addRandomTile(newBoard);
    return newBoard;
  };

  const [board, setBoard] = useState(createBoard());

  useEffect(() => {
    const handleKeyDown = (event) => {
      switch (event.key) {
        case 'ArrowLeft':
          setBoard((prevBoard) => move(prevBoard, 'left'));
          break;
        case 'ArrowRight':
          setBoard((prevBoard) => move(prevBoard, 'right'));
          break;
        case 'ArrowUp':
          setBoard((prevBoard) => move(prevBoard, 'up'));
          break;
        case 'ArrowDown':
          setBoard((prevBoard) => move(prevBoard, 'down'));
          break;
        default:
          break;
      }
    };

    window.addEventListener('keydown', handleKeyDown);
    return () => window.removeEventListener('keydown', handleKeyDown);
  }, []);

  const cellStyles = (value) => ({
    width: '70px',
    height: '70px',
    margin: '5px',
    backgroundColor: value ? getCellColor(value) : '#eee4da',
    fontSize: '24px',
    display: 'flex',
    alignItems: 'center',
    justifyContent: 'center',
    borderRadius: '5px',
    color: value > 4 ? '#fff' : '#000',
  });

  const getCellColor = (value) => {
    switch (value) {
      case 2:
        return '#eee4da';
      case 4:
        return '#ede0c8';
      case 8:
        return '#f2b179';
      case 16:
        return '#f59563';
      case 32:
        return '#f67c5f';
      case 64:
        return '#f67c5f';
      case 128:
        return '#f9e79f';
      case 256:
        return '#f9e79f';
      case 512:
        return '#f9e79f';
      case 1024:
        return '#f9e79f';
      case 2048:
        return '#fff';
      default:
        return '#ccc';
    }
  };

  return (
    <div style={{ textAlign: 'center', width: '300px', margin: '50px auto' }}>
      <h1>2048 Game</h1>
      <div
        style={{
          backgroundColor: '#bbada0',
          borderRadius: '5px',
          padding: '10px',
        }}
      >
        {board.map((row, rowIndex) => (
          <div style={{ display: 'flex' }} key={rowIndex}>
            {row.map((cell, cellIndex) => (
              <div style={cellStyles(cell)} key={cellIndex}>
                {cell || ''}
              </div>
            ))}
          </div>
        ))}
      </div>
    </div>
  );
};

export default App;
```
