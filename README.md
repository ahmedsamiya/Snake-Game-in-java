# Snake-Game-in-java

import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.KeyAdapter;
import java.awt.event.KeyEvent;
import java.util.LinkedList;
import java.util.Random;


    
public class SnakeGame extends JPanel implements ActionListener {
    private final int WIDTH = 600;
    private final int HEIGHT = 400;
    private final int BOX_SIZE = 10;
    private final int ALL_BOXES = (WIDTH * HEIGHT) / (BOX_SIZE * BOX_SIZE);
    
    private LinkedList<Point> snake;
    private Point food;
    private char direction;
    private boolean gameOver;
    private Timer timer;

    public SnakeGame() {
        setPreferredSize(new Dimension(WIDTH, HEIGHT));
        setBackground(Color.BLACK);
        setFocusable(true);
        addKeyListener(new KeyAdapter() {
            public void keyPressed(KeyEvent e) {
                switch (e.getKeyCode()) {
                    case KeyEvent.VK_UP:
                        if (direction != 'S') direction = 'W';
                        break;
                    case KeyEvent.VK_DOWN:
                        if (direction != 'W') direction = 'S';
                        break;
                    case KeyEvent.VK_LEFT:
                        if (direction != 'D') direction = 'A';
                        break;
                    case KeyEvent.VK_RIGHT:
                        if (direction != 'A') direction = 'D';
                        break;
                }
            }
        });
        
        initGame();
    }

    private void initGame() {
        snake = new LinkedList<>();
        snake.add(new Point(5, 5));
        direction = 'D';
        spawnFood();
        timer = new Timer(100, this);
        timer.start();
        gameOver = false;
    }

    private void spawnFood() {
        Random rand = new Random();
        food = new Point(rand.nextInt(WIDTH / BOX_SIZE), rand.nextInt(HEIGHT / BOX_SIZE));
        while (snake.contains(food)) {
            food = new Point(rand.nextInt(WIDTH / BOX_SIZE), rand.nextInt(HEIGHT / BOX_SIZE));
        }
    }

    @Override
    protected void paintComponent(Graphics g) {
        super.paintComponent(g);
        if (gameOver) {
            g.setColor(Color.RED);
            g.drawString("Game Over! Press R to Restart", WIDTH / 6, HEIGHT / 2);
            return;
        }
        
        g.setColor(Color.GREEN);
        for (Point p : snake) {
            g.fillRect(p.x * BOX_SIZE, p.y * BOX_SIZE, BOX_SIZE, BOX_SIZE);
        }
        
        g.setColor(Color.RED);
        g.fillRect(food.x * BOX_SIZE, food.y * BOX_SIZE, BOX_SIZE, BOX_SIZE);
    }

    @Override
    public void actionPerformed(ActionEvent e) {
        if (!gameOver) {
            move();
            checkCollision();
            repaint();
        }
    }

    private void move() {
        Point head = new Point(snake.getFirst());
        switch (direction) {
            case 'W':
                head.translate(0, -1);
                break;
            case 'S':
                head.translate(0, 1);
                break;
            case 'A':
                head.translate(-1, 0);
                break;
            case 'D':
                head.translate(1, 0);
                break;
        }

        snake.addFirst(head);
        if (head.equals(food)) {
            spawnFood();
        } else {
            snake.removeLast();
        }
    }

    private void checkCollision() {
        Point head = snake.getFirst();
        if (head.x < 0 || head.x >= WIDTH / BOX_SIZE || head.y < 0 || head.y >= HEIGHT / BOX_SIZE || snake.subList(1, snake.size()).contains(head)) {
            gameOver = true;
            timer.stop();
        }
    }

    public static void main(String[] args) {
        JFrame frame = new JFrame("Snake Game");
        SnakeGame game = new SnakeGame();
        frame.add(game);
        frame.pack();
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setVisible(true);
        frame.setResizable(false);
        
        game.addKeyListener(new KeyAdapter() {
            public void keyPressed(KeyEvent e) {
                if (game.gameOver && e.getKeyCode() == KeyEvent.VK_R) {
                    game.initGame();
                    game.repaint();

    
}
            }
        });

    }
}
