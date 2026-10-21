---
title: Python 学习笔记：PyAutoGUI 模块
tags:
  - Python
  - 编程
abbrlink: 3769387627
date: 2026-10-19 10:43:04
---

# 简介

在日常的工作和学习中，我们经常遇到需要一些在系统界面上进行重复性操作的情况，比如某些游戏需要刷任务，某些程序会检测在线时长，需要定期动一下鼠标。还有某些网站允许浏览其内容，但无法下载，只能通过不断截图来保存。

对于重复性的操作，最好就是用脚本程序来代劳。而说到脚本，最方便最实用的当属 Python 了，况且 Python 有很多已经造好的轮子（模块），我们只需要稍加学习，就能拿来用了。

PyAutoGUI 就是这样一个方便的轮子。利用它，我们可以实现大多数桌面操作的自动化。

# PyAutoGUI的安装

安装 PyAutoGUI 模块的过程很简单，如果已经安装了Python 和 Pip 模块，那只需要联网，打开命令行，输入下面这条命令，再按回车执行即可：
```python
pip install pyautogui
```
如果是 Anaconda，则需要输入以下命令：
```python
conda install conda-forge::pyautogui
```
对于不同的操作系统，情况可能略有差别，但以上两种适合 Windows 和绝大部分 Linux 系统。

# 一些关于界面的基本知识

界面，或者说，图形用户界面（Graphical User Interface，简称 GUI）是操作系统与用户通过显示器交换信息的窗口。操作系统将图形化的信息显示在屏幕上，用户在屏幕上利用键鼠向操作系统传达指令，执行复杂的操作。界面的形状取决于显示器，除了某些特殊情形，目前大多数显示器都是矩形，长宽比一般为 16:9、4:3、16:10 等等。

对于显示器来说，最重要的参数莫过于分辨率。分辨率是指显示器在宽度和高度两个方向上有多少像素。以最常见的 1080p 显示器为例，其分辨率为 1920\*1080，即长 1920 个像素，宽 1080 个像素。

有了分辨率以后，我们就可以建立相应的屏幕坐标系。与数学不同，计算机领域普遍取屏幕左上角为坐标原点，$x$ 轴正方向向右，$y$ 轴正方向向下。这样我们就可以用坐标表示屏幕上的每一个像素点，如图所示：
{% image screenAxis.webp 屏幕坐标系 %}

屏幕坐标系非常重要，PyAutoGUI 在界面上不同位置执行的操作基本上都要依据屏幕坐标系来完成。

# PyAutoGUI 模块的导入

和其他Python模块一样，PyAutoGUI 需要用 import 语句导入：

```python
import pyautogui as pag
```

为了方便，我们可以取一个别名 "pag"。

# 获取屏幕分辨率

一般来说，用户知道自己的屏幕分辨率，显示器说明书上会标注。但考虑到程序会运行在不同的机器上，对不同的屏幕，操作也会有所不同。如果每换一台机器都要用户自己去调整，那就太麻烦了。所以有必要让程序获取屏幕分辨率的大小，从而根据不同情况来进行调整。屏幕分辨率可通过 PyAutoGUI 模块中的 size 函数获取：
```python
import pyautogui as pag

screenWidth, screenHeight = pag.size()
print("屏幕分辨率为", screenWidth, "*", screenHeight)
```
得到的结果为
```python
屏幕分辨率为 1920*1080
```
模块还提供了onScreen函数，用来判断坐标是否位于屏幕上：
```python
# 判断给定的坐标 (x, y) 是否位于屏幕上，返回值为布尔类型(Ture of False)
pag.onScreen(x, y)
```

# 基本的鼠标操作

## 移动鼠标

移动鼠标有两种方式：一种是直接把鼠标移动到指定位置，如屏幕右上角；另一种则是把鼠标移动到一个相对位置，比如当前位置向右 100 个像素点。与之对应，在 PyAutoGUI 模块中，有两个函数负责移动鼠标，一个通过绝对位置来移动：
```python
# 把鼠标移动到屏幕坐标（200，200）处
pag.moveTo(200, 200, duration=1)
```
另一个则通过相对位置：
```python
# 把鼠标从原来位置分别向右、向下移动200个像素点
pag.move(200, 200, duration=1)
```
duration 为持续时间，即移动过程所耗费的时间。其类型为浮点数，默认参数 0.25 秒。

## 获取鼠标信息

类似于屏幕，程序有时也需要知道关于鼠标当前位置的信息，PyAutoGUI 模块内置了 position 函数用于获取当前鼠标所在位置：
```python
x,y = pag.position()
print(f"当前鼠标所在位置为: ({x}, {y})")
```
得到的结果为
```python
当前鼠标所在位置为: (1423, 756)
```

## 拖拽鼠标

与移动鼠标类似，拖拽鼠标也有两种方式，一种是拖拽到指定位置：
```python
# 在当前位置按住鼠标左键，拖拽到屏幕坐标（200，200）处
pag.dragTo(200, 200, duration=1, button='left')
```
另一种是拖拽到相对位置：
```python
# 在当前位置按住鼠标左键，拖拽到向右、向下移动200个像素点处
pag.drag(200, 200, duration=1, button='left')
```
button 有左键('left')、中键('middle')、右键('right')三种选择，默认值为左键。

## 点击鼠标

鼠标的点击有单击和双击两种操作。其中，单击的函数为 click：
```python
# clicks 为点击次数，默认值为1。interval 是每次点击的间隔时间，默认为0。
pag.click(x=200, y=200, clicks=2, interval=0, duration=0, button='left')
```
双击的函数为 doubleClick：
```python
pag.doubleClick(x=200, y=200, duration=0, button='left')
```

## 鼠标单击的分步操作

单击鼠标其实包含两个动作，即按下和松开，PyAutoGUI 模块也内置了两个函数与之对应。其中，按下为 mouseDown：
```python
pag.mouseDown(button='left')
```
松开为 mouseUp：
```python
pag.mouseUp(button='left')
```

# 键盘操作

## 输入字符串

PyAutoGUI 模块提供了 write 函数用于模拟键盘输入字符串：
```python
pag.write('Hello, World!', interval=0.2)
```
interval 为每次输入字符的间隔时间，默认值为 0。需要注意的是，这个函数不能直接输入中文，而是需要使用 Unicode 编码。此外，由于是模拟键盘输入，如果间隔时间过短，可能会出现丢失字符的情况，所以尽量不要使用默认的间隔时间。

## 按键操作

按键操作，对应的函数为 press：

```python
pag.press('enter', presses=2, interval=0.2)
```
presses 为按键次数，默认为 1。interval 是两次按键间隔时间，默认为 0。

## 按键的分步操作

和鼠标一样，按键也可以分别进行按下与松开的操作，其函数为 keyDown 与 keyUp：

```python
pag.keyDown('a')
pag.keyUp('a')
```

## 热键（组合键）操作

对于复制粘贴（ctrl+c，ctrl+v）这类组合键，PyAutoGUI 模块提供了 hotkey 函数：
```python
pag.hotkey('ctrl', 'c', interval=0.2)
```

## 按键名称

PyAutoGUI 提供了所有按键对应的名称，以字符串数组的形式保存在 KEYBOARD_KEYS 变量中：
```python
print(pag.KEYBOARD_KEYS)
['\t', '\n', '\r', ' ', '!', '"', '#', '$', '%', '&', "'", '(', ')', '*', '+', ',', '-', '.', '/', '0', '1', '2', '3', '4', '5', '6', '7', '8', '9', ':', ';', '<', '=', '>', '?', '@', '[', '\\', ']', '^', '_', '`', 'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z', '{', '|', '}', '~', 'accept', 'add', 'alt', 'altleft', 'altright', 'apps', 'backspace', 'browserback', 'browserfavorites', 'browserforward', 'browserhome', 'browserrefresh', 'browsersearch', 'browserstop', 'capslock', 'clear', 'convert', 'ctrl', 'ctrlleft', 'ctrlright', 'decimal', 'del', 'delete', 'divide', 'down', 'end', 'enter', 'esc', 'escape', 'execute', 'f1', 'f10', 'f11', 'f12', 'f13', 'f14', 'f15', 'f16', 'f17', 'f18', 'f19', 'f2', 'f20', 'f21', 'f22', 'f23', 'f24', 'f3', 'f4', 'f5', 'f6', 'f7', 'f8', 'f9', 'final', 'fn', 'hanguel', 'hangul', 'hanja', 'help', 'home', 'insert', 'junja', 'kana', 'kanji', 'launchapp1', 'launchapp2', 'launchmail', 'launchmediaselect', 'left', 'modechange', 'multiply', 'nexttrack', 'nonconvert', 'num0', 'num1', 'num2', 'num3', 'num4', 'num5', 'num6', 'num7', 'num8', 'num9', 'numlock', 'pagedown', 'pageup', 'pause', 'pgdn', 'pgup', 'playpause', 'prevtrack', 'print', 'printscreen', 'prntscrn', 'prtsc', 'prtscr', 'return', 'right', 'scrolllock', 'select', 'separator', 'shift', 'shiftleft', 'shiftright', 'sleep', 'space', 'stop', 'subtract', 'tab', 'up', 'volumedown', 'volumemute', 'volumeup', 'win', 'winleft', 'winright', 'yen', 'command', 'option', 'optionleft', 'optionright']
```

# 屏幕截图

PyAutoGUI 提供了 screenshot 函数用来对屏幕截图：
```python
pag.screenshot('image.png') # 全屏截图，文件保存为png格式
pag.screenshot('D:\\image\\img1.png')   # 按路径+文件名保存
```

对于屏幕部分区域截图需要加一个参数 region：

```python
# region 的类型为列表，其格式为 [起始x坐标, 起始y坐标, x扩展距离, y扩展距离]
pag.screenshot('image.png', region=[200, 200, 1400, 1000])
```

# 屏幕定位

有些时候，程序需要点击位置并不固定的按钮或文本框。对这类情况，PyAutoGUI 模块提供了 locateOnScreen 函数用于定位需要点击的位置：
```python
# 需要提供一个图片用来匹配屏幕，返回值为元组，包含(左上角x坐标, 左上角y坐标, 宽度, 高度)
pag.locateOnScreen('img.png')
```

此外，还有一个 locateCenterOnScreen 函数，用于定位中心点：
```python
pag.locateCenterOnScreen('img.png')
```

# 实例应用

## 重复点击

在某些时候，会需要重复点击屏幕上的某处位置，这就很适合用脚本来完成。相应的代码如下：
```python
import pyautogui
import time

n = int(input('请输入点击次数：'))
t = int(input('请输入时间间隔（单位：秒）：'))
print('5秒后开始，请切换窗口，并将鼠标移到指定位置！')
time.sleep(5)

for i in range(n):
    pyautogui.click()
    time.sleep(t)
```

## 自动翻页截屏

某些文档或书籍，只能在浏览器上在线阅读而不允许下载，可以用截屏的方式保存。但碰到几十上百页的情况，手动翻页截屏就很麻烦了。用脚本就可以很方便完成这种任务。相应代码如下：
```python
import pyautogui
import time
          
n = int(input('请输入总页数：'))
t = int(input('请输入时间间隔（单位：秒）：'))
# 注意把视图调整为单页
input('请把鼠标指针挪到页面左下角，再按回车...')
x1,y1 = pyautogui.position()
input('请把鼠标指针挪到页面右上角，再按回车...')
x2,y2 = pyautogui.position()
print('截屏将在5秒后开始，请切换窗口！')
time.sleep(5)
        
for i in range(n):
    # 这里直接保存在脚本的目录了，也可以自行设置路径，格式建议 jpeg
    pyautogui.screenshot('page' + str(i) + '.jpeg', [x1, y1, x2, y2])
    # 翻页一般用方向键即可，也可以用 pagedown 键，视具体情况而定
    pyautogui.press('down')
    time.sleep(t)
```

以上就是比较常见的一些应用，大家可以根据实际的需要自己编写相应的脚本。