# 八行代码实现贪吃蛇游戏，祝所有工控大朋友节日快乐

一个贪吃蛇，就着上下左右按键已经模糊的按键就能玩一个下午，是童年美好的记忆。或许我们再也找不回当初那简单的快乐，但愿你长成一个优秀的大人，也愿你永远保有童真，儿童节快乐，工控大朋友们！

![诺基亚贪吃蛇](https://pic.imgdb.cn/item/60b3cf2239f6859bc23d2ed8.jpg)

为了最简洁化复刻贪吃蛇游戏，此次使用ST语言编程，将GX Work3缓存存储器监视界面当作游戏界面，使用初始化赋值功能，语句也尽量简略，最终八行代码实现完整的贪吃蛇游戏体验。

#### Ⅰ、效果展示

图中展示为倍速播放后效果，触摸屏只当作方向按键使用，也可以直接再GX Work3中更改方向软元件的值来调整方向，不过这操作就很反人类了。

![仿真GIF](https://pic.imgdb.cn/item/60b3cf2239f6859bc23d2efb.gif)

#### Ⅱ、实现原理

**贪吃蛇程序核心逻辑如下：**

2.1、游戏界面分辨率是16*21，蛇和食物都是由1个bit点表示，食物会闪烁；

2.2、初始化时蛇的长度是1，蛇初始的移动的方向是上，用一个一维数组aSnakeData代表蛇身数据，用wSnakeLength表示蛇身长度，数组的第一个坐标是蛇头位置，aSnakeData[wSnakeLength]表示蛇尾位置；

2.3、正常移动时根据移动方向将新蛇头位置置位，将蛇尾位置复位，如果蛇吃到了食物，即蛇头的坐标等于食物的坐标，此次不复位蛇尾，就产生了蛇长度增加的效果；

2.4、食物被吃掉后，随机在空的位置再生成一个；

2.5、当蛇撞上自身或墙壁，游戏结束，用新蛇头的位置是否已被置位来判断的。

![贪吃蛇编程逻辑](https://pic.imgdb.cn/item/60b3d2b039f6859bc266ee18.png)

#### Ⅲ、程序调试

```python
//蛇身移动
MOVP(SM415,(aSnakeData[0]-K16)*BOOL_TO_WORD(wDirection=0)+(aSnakeData[0]+K16)*BOOL_TO_WORD(wDirection=1)+(aSnakeData[0]+K1)*BOOL_TO_WORD(wDirection=2)+(aSnakeData[0]-K1)*BOOL_TO_WORD(wDirection=3),wSnakeHead);
WSFLP(SM415,wSnakeHead,K50,K1,aSnakeData[0]);

//是否吃到果实
DMOVP(SM415 AND wSnakeHead=wFruitCache,WORD_TO_DWORD(wSnakeLength+K1),D31);

//果实新生
FMOV(MEF(SM415) AND INV(aDisplay[ABS(SD420) MOD K304]) AND wFruitCache=K0,ABS(SD420) MOD K304,K2,D32);

//重新开局，是否吃到墙壁自己
BMOV(SM402 OR MEP(SM415 AND aDisplay[wSnakeHead] AND wFruitCache>K0),D100,K90,D0);

//蛇尾消除
RST(MEF(SM415) AND wFruitCache>K0,aDisplay[aSnakeData[wSnakeLength]]);

//蛇头置位
SET(MEF(SM415),aDisplay[wSnakeHead]);

//果实闪烁
OUT(wFruitCache>K0 AND SM412,aDisplay[wFruitCache]);
```

#### Ⅳ、源码分享

**程序如果Bug，烦请指出，有其它想法意见，欢迎交流探讨🤝**

**我私人微信号为：fastudy163，个人公众号在微信搜索【干工控】即可找到。**

![干工控微信号](https://pic.imgdb.cn/item/60b3e00639f6859bc2f14ef2.png)
