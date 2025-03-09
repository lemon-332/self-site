# 贪吃蛇

```jsx
/**
 * inline: true
 */
import React, { useEffect, useState } from 'react';

const SnakeGame = () => {
  const [snake, setSnake] = useState([{ x: 3, y: 3 }]);
  const [food, setFood] = useState({ x: 5, y: 5 });
  const [direction, setDirection] = useState({ x: 1, y: 0 });
  const [gameOver, setGameOver] = useState(false);
  const [score, setScore] = useState(0);
  const gridSize = 20;

  const handleKeyDown = (event) => {
    switch (event.key) {
      case 'ArrowUp':
        if (direction.y !== 1) setDirection({ x: 0, y: -1 });
        break;
      case 'ArrowDown':
        if (direction.y !== -1) setDirection({ x: 0, y: 1 });
        break;
      case 'ArrowLeft':
        if (direction.x !== 1) setDirection({ x: -1, y: 0 });
        break;
      case 'ArrowRight':
        if (direction.x !== -1) setDirection({ x: 1, y: 0 });
        break;
      default:
        break;
    }
  };

  const moveSnake = () => {
    setSnake((prev) => {
      const newSnake = [...prev];
      const head = {
        x: newSnake[0].x + direction.x,
        y: newSnake[0].y + direction.y,
      };

      // 检测碰撞
      if (
        head.x < 0 ||
        head.x >= gridSize ||
        head.y < 0 ||
        head.y >= gridSize ||
        newSnake.some((segment) => segment.x === head.x && segment.y === head.y)
      ) {
        setGameOver(true);
        return prev; // 游戏结束，不更新蛇
      }

      newSnake.unshift(head);

      // 检测食物是否被吃到
      if (head.x === food.x && head.y === food.y) {
        setScore((prev) => prev + 1);
        generateFood(newSnake);
      } else {
        newSnake.pop(); // 移除尾部
      }

      return newSnake;
    });
  };

  const generateFood = (snake) => {
    let newFood;
    do {
      newFood = {
        x: Math.floor(Math.random() * gridSize),
        y: Math.floor(Math.random() * gridSize),
      };
    } while (
      snake.some(
        (segment) => segment.x === newFood.x && segment.y === newFood.y,
      )
    );
    setFood(newFood);
  };

  useEffect(() => {
    window.addEventListener('keydown', handleKeyDown);
    const interval = setInterval(moveSnake, 200);
    return () => {
      window.removeEventListener('keydown', handleKeyDown);
      clearInterval(interval);
    };
  }, [direction]);

  return (
    <div style={styles.gameContainer}>
      {gameOver ? (
        <div style={styles.gameOver}>Game Over! Score: {score}</div>
      ) : (
        <>
          {snake.map((segment, index) => (
            <div
              key={index}
              style={{
                ...styles.segment,
                left: `${segment.x * 20}px`,
                top: `${segment.y * 20}px`,
                backgroundColor: index === 0 ? '#00FF00' : '#32CD32', // 头部和身体颜色不同
              }}
            />
          ))}
          <div
            style={{
              ...styles.food,
              left: `${food.x * 20}px`,
              top: `${food.y * 20}px`,
            }}
          />
        </>
      )}
      <div style={styles.score}>Score: {score}</div>
    </div>
  );
};

const styles = {
  gameContainer: {
    position: 'relative',
    width: '800px',
    height: '400px',
    background: 'linear-gradient(135deg, #1D2671, #C33764)', // 渐变背景
    overflow: 'hidden',
    border: '2px solid #fff',
    borderRadius: '10px',
    boxShadow: '0 0 20px rgba(0, 0, 0, 0.5)',
  },
  segment: {
    position: 'absolute',
    width: '20px',
    height: '20px',
    borderRadius: '4px',
    transition: 'background-color 0.2s ease', // 使颜色变化更加平滑
  },
  food: {
    position: 'absolute',
    width: '20px',
    height: '20px',
    backgroundColor: '#FF5733', // 食物颜色
    borderRadius: '50%', // 圆形食物
    boxShadow: '0 0 5px rgba(255, 87, 51, 0.7)', // 食物阴影
  },
  gameOver: {
    color: '#fff',
    fontSize: '24px',
    position: 'absolute',
    top: '50%',
    left: '50%',
    transform: 'translate(-50%, -50%)',
    textAlign: 'center',
  },
  score: {
    position: 'absolute',
    top: '10px',
    left: '10px',
    fontSize: '18px',
    color: '#fff',
    textShadow: '1px 1px 5px rgba(0, 0, 0, 0.7)', // 得分的阴影
  },
};

export default SnakeGame;
```
