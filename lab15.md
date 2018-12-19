## ——智能蛇简略介绍
![智能蛇](https://img-blog.csdn.net/20171227181659682?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3lzdV9saWFuZ3dq/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

如图，智能蛇即为自己去吃食物的贪吃蛇。
## ——智能蛇的实行方法
    先判断是否可以吃到食物
    如果不能吃到食物，则让蛇头跟着蛇尾巴走，虽然绕了远路，但是可以保证不吃到自己，这样子跟着尾巴直到吃食物。
    如果能吃到食物，在判断会不会吃到自己的尾巴，如果会的话，则解决方法如上，还是让蛇跟着蛇尾巴走，直到吃到食物。
    如果可以吃食物，不会吃到自己的尾巴，就直接去吃食物即可，而且可以依靠最短路径过去，则大大缩短时间，提高了效率。
    其实，在追着自己尾巴走的时候，常常会遇到走哪个方向去找尾巴，其中最保险的是走离食物最远的方向去吃食物，则可以有足够大的空间去吃食物。
简单伪代码如下：
```C 
//Hx,Hy: 头的位置 
//Fx,Fy：食物的位置 
function whereGoNext(Hx,Hy,Fx,Fy) 
{
//用数组movable[3]={“a”,”d”,”w”,”s”} 记录可走的方向 
//用数组distance[3]={0,0,0,0} 记录离食物的距离 
//分别计算蛇头周边四个位置到食物的距离。H头的位置，F食物位置 
//例如：假设输入”a” 则distance[0] = |Fx – (Hx-1)| + |Fy – Hy| 
//如果 Hx-1，Hy 位置不是Blank，则 distance[0] = 9999 
//选择distance中存最小距离的下标p，注意最小距离不能是9999 
//返回 movable[p] 
}
以下决定不自杀的伪代码
void calculate() 
{ //计算并记录蛇头与食物距离 
int i = 0, x, y; 
for(i = 0; i < 4; i++) { 
if(movable[i] == 1) { //如果该方向能走，则记录下沿该方向走一步后与食物的距离 
x = A[sum - 1].x + dx[i]; 
y = A[sum - 1].y + dy[i]; 
distance[i] = abs(foodx-x)+abs(foody-y); 
} else { //如果不能走则把距离设置为9999 
distance[i] = 9999; 
} 
```
再加上以下的贪吃蛇代码：
```C
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
```
### 一个简单的智能蛇就完成了~