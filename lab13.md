## ——基本设计思路
    首先，我们得想想贪吃蛇游戏的结构，四周的边框，边框里的蛇，和随机出现的食物而这些所有的部分，都是局限在边框这一范围内。因此，我们可以先创建一个二维数组。贪吃蛇的控制功能：通过各种条件的判断，实现对游戏蛇的左移、右移、下移、上移、自由移动，贪吃蛇的加长功能。游戏显示更新功能：当贪吃蛇左右移动、上下移动，以及身体加长时要清除先前的贪吃蛇图像，用新坐标重绘贪吃蛇。游戏分数更新功能：在游戏玩家进行游戏过程中，需要按照一定的游戏规则给游戏玩家的贪吃蛇增长。

![设计图](https://raw.githubusercontent.com/wuzy57/wuzy-homework/gh-pages/%E8%B4%AA%E5%90%83%E8%9B%87.png)

## ——贪吃蛇的代码
    #include <stdio.h>
    #include <stdlib.h>
    #include <time.h>

    char BLANK_CHAR      = ' ';
    char WALL_CHAR       = '*';
    char SNAKE_HEAD_CHAR = 'H';
    char SNAKE_BODY_CHAR = 'X';
    char FOOD_CHAR       = '$';

    char map[12][13] = 
    {
     "************",
     "*          *",
     "*          *",
     "*          *",
     "*          *",
     "*          *",
     "*          *",
     "*          *",
     "*          *",
     "*          *",
     "*          *",
     "************",
    };
    //此为初始状态 
    int snakeHeadX = 1, snakeHeadY = 1;
    int snakeBodyX[100] = {0}, snakeBodyY[100] = {0};
    int snakeBodyLen = 0;
    int snakeTailIndex = -1;
    int willBeLonger = 0;

    int foodX = 0, foodY = 0;

    int gameRunning = 1;

    void printMap() {
    system("cls");
    int i=0;
    for (i = 0; i < 12; ++i) {
    printf("%s\n", map[i]);
    }
    }

    void initGame() {
    // 蛇初始化 
    snakeHeadX = snakeHeadY = 1;
    map[snakeHeadX][snakeHeadY] = SNAKE_HEAD_CHAR;
    snakeBodyLen = 0;
    gameRunning = 1;
    snakeTailIndex = -1;
    willBeLonger = 0;
    // 食物初始化 
    gameRunning = 1;

    printMap();
    }

    void gameOver() 
    {
    printf("GAME OVER!!\n");
    gameRunning = 0;
    }

    void snakeMove(char control) 
    {
    map[snakeHeadX][snakeHeadY] = BLANK_CHAR;  // 蛇头的前行 
    int prevSnakeHeadX = snakeHeadX;
    int prevSnakeHeadY = snakeHeadY;
    switch (control) {
    case 'w':
    snakeHeadX--;
    break;
    case 'a':
    snakeHeadY--;
    break;
    case 's':
    snakeHeadX++;
    break;
    case 'd':
    snakeHeadY++;
    break;
    default:
    return;
    }//蛇的行动 
    map[snakeHeadX][snakeHeadY] = SNAKE_HEAD_CHAR;

    int moved = 0;
    // 蛇变长 
    if (willBeLonger)
    {
    willBeLonger = 0;
    moved = 1;
    int i ;
    for (i = snakeBodyLen - 1; i > snakeTailIndex; --i)
	 {
      snakeBodyX[i + 1] = snakeBodyX[i];
      snakeBodyY[i + 1] = snakeBodyY[i];
    }
    snakeBodyX[snakeTailIndex + 1] = prevSnakeHeadX;
    snakeBodyY[snakeTailIndex + 1] = prevSnakeHeadY;
    if (snakeTailIndex < 0) snakeTailIndex = 0;
    map[prevSnakeHeadX][prevSnakeHeadY] = SNAKE_BODY_CHAR;
    snakeBodyLen++;
    }

    if (snakeBodyLen <= 0 || moved) return;
    map[snakeBodyX[snakeTailIndex]][snakeBodyY[snakeTailIndex]] = BLANK_CHAR;
    snakeBodyX[snakeTailIndex] = prevSnakeHeadX;
    snakeBodyY[snakeTailIndex] = prevSnakeHeadY;
    map[prevSnakeHeadX][prevSnakeHeadY] = SNAKE_BODY_CHAR;
    // 移动尾巴 
    snakeTailIndex = (snakeTailIndex - 1 + snakeBodyLen) % snakeBodyLen;
    }

    int main() {
    // 初始化时间种子 
    srand(time(NULL));

    initGame();

    char control = 0;
    while (gameRunning) {
    scanf(" %c", &control); 
    snakeMove(control);
    printMap();
    }

    return 0;
    }
