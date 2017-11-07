# 记忆卡片小游戏

## 启动游戏

直接使用浏览器（chrome或firefox）打开index.html即可开始愉快的玩耍了，如果安装有Python的话，也可以切换到想项目根目录，然后通过命令`python -m SimpleHTTPServer 8080`启动web服务器，并在浏览器中打开localhost:8080开始游戏。

## 游戏操作方法
使用鼠标点击卡片。

## 游戏规则
游戏板包含16张卡片，每个卡片都隐藏有一个不同的符号并随机排列。玩家需要每次点开任意两个卡片，找到匹配的卡片，所有卡片都匹配成功后游戏结束。完成游戏使用的步骤越少，最后得到的星星越多。

* 16步（含）以内：三颗星星
* 32步（含）以内：两颗星星
* 48步（含）以内：一颗星星
* 其他：没有星星