---
title: "Notes_Vim"
date: 2017-09-26
categories:
- Notebook
tags:
- Vim
thumbnailImagePosition: left
thumbnailImage: //img3.wikia.nocookie.net/__cb4/vim/images/8/89/Wiki-wordmark.png
---
This Vim Nootebook is kept for my self-learning about Vim. When learning to use linux, Vim is a must.
<!--more-->

摘自《鸟哥Linux私房菜》

# **Vim**程序编辑器
## 为何要学 vim
* 所有的 Unix Like 系统都会内置 vi 文书编辑器，其他的文书编辑器则不一定会存在；
* 很多个别软件的编辑接口都会主动调用 vi （例如未来会谈到的 crontab, visudo, edquota
  等指令）；
* vim 具有程序编辑的能力，可以主动的以字体颜色辨别语法的正确性，方便程序设计；
* 因为程序简单，编辑速度相当快速。

## Vi的使用
* **一般指令模式**
    以 vi 打开一个文件就直接进入一般指令模式了（这是默认的模式，也简称为一般模式）。在这个模式中， 你可以使用“上下左右”按键来移动光标，你可以使用“删除字符”或“删除整列”来处理文件内容， 也可以使用“复制、贴上”来处理你的文件数据。
* **编辑模式**
    在一般指令模式中可以进行删除、复制、贴上等等的动作，但是却无法编辑文件内容的！要等到你按下“i, I, o, O, a, A, r, R”等任何一个字母之后才会进入编辑模式。注意了！通常在  Linux 中，按下这些按键时，在画面的左下方会出现“ INSERT 或 REPLACE ”的字样，此时才可以进行编辑。而如果要回到一般指令模式时，则必须要按下“Esc”这个按键即可退出编辑模式。
* **命令行命令模式**
    在一般模式当中，输入“ : / ? ”三个中的任何一个按钮，就可以将光标移动到最下面那一列。在这个模式当中， 可以提供你“搜寻数据”的动作，而读取、存盘、大量取代字符、离开 vi 、显示行号等等的动作则是在此模式中达成的！

## 简易操作
1. 使用 “vi filename” 进入一般指令模式
```vim
/bin/vi welcome.txt   ##create file welcom.txt
```
2. 按下 i 进入编辑模式，开始编辑文字
3. 按下【ESC】按钮回到一般指令模式
4. 进入命令行界面，文件存储并离开 vi 环境
```vim
:wq
```

## 按键说明

### 一般指令模式可用的按钮说明：光标移动、复制黏贴、搜索取代

#### 移动光标的方法

| 按键               | 功能                                       |
| ---------------- | ---------------------------------------- |
| h 或 向左方向键（←）     | 光标向左移动一个字符                               |
| j 或 向下方向键（↓）     | 光标向下移动一个字符                               |
| k 或 向上方向键（↑）     | 光标向上移动一个字符                               |
| l 或 向右方向键（→）     | 光标向右移动一个字符                               |
| [Ctrl] + [f] | 屏幕“向下”移动一页，相当于 [Page Down]按键 （常用）    |
| [Ctrl] + [b] | 屏幕“向上”移动一页，相当于 [Page Up] 按键（常用）      |
| [Ctrl] + [d]     | 屏幕“向下”移动半页                               |
| [Ctrl] + [u]     | 屏幕“向上”移动半页                               |
| +                | 光标移动到非空白字符的下一列                           |
| -                | 光标移动到非空白字符的上一列                           |
| n<space>         | n<space>那个 n 表示“数字”，例如 20 。按下数字后再按空白键，光标会向右移动这一列的 n 个字符。例如 20<space> 则光标会向后面移动 20个字符距离。 |
| 0 或功能键[Home] | 这是数字“ 0 ”：移动到这一列的最前面字符处（常用）          |
| $ 或功能键[End]  | 移动到这一列的最后面字符处（常用）                    |
| H                | 光标移动到这个屏幕的最上方那一列的第一个字符                   |
| M                | 光标移动到这个屏幕的中央那一列的第一个字符                    |
| L                | 光标移动到这个屏幕的最下方那一列的第一个字符                   |
| G            | 移动到这个文件的最后一列（常用）                     |
| nG               | n 为数字。移动到这个文件的第 n 列。例如20G 则会移动到这个文件的第 20 列（可配合:set nu） |
| gg           | 移动到这个文件的第一列，相当于 1G 啊!（常用）                |
| n<Enter>         | n 为数字。光标向下移动 n 列（常用）                     |

#### 搜寻与取代

| 按键                        | 功能                                       |
| ------------------------- | ---------------------------------------- |
| /word                 | 向光标之下寻找一个名称为 word 的字串。例如要在文件内搜寻 vbird 这个字串，就输入/vbird 即可！ （常用） |
| ?word                 | 向光标之上寻找一个字串名称为 word 的字串。                 |
| n                         | 这个 n 是英文按键。代表“<u>重复前一个搜寻的动作</u>”。举例来说， 如果刚刚我们执行 /vbird 去向下搜寻 vbird 这个字串，则按下n 后，会向下继续搜寻下一个名称为 vbird 的字串。如果是执行 ?vbird 的话，那么按下 n则会向上继续搜寻名称为 vbird 的字串！ |
| N                         | 这个 N 是英文按键。与 n 刚好相反，为“反向”进行前一个搜寻动作。 例如 /vbird 后，按下 N 则表示“向上”搜寻 vbird 。 |
| :n1,n2s/word1/word2/g | n1 与 n2 为数字。在第 n1 与 n2 列之间寻找word1 这个字串，并将该字串取代为 word2！举例来说，在 100 到 200 列之间搜寻 vbird并取代为 VBIRD则：“:100,200s/vbird/VBIRD/g”。（常用） |
| :1,$s/word1/word2/g   | 从第一列到最后一列寻找 word1 字串，并将该字串取代为 word2 ！（常用） |
| :1,$s/word1/word2/gc  | 从第一列到最后一列寻找 word1 字串，并将该字串取代为 word2 ！且在取代前显示提示字符给使用者确认 （confirm） 是否需要取代！（常用） |

#### 删除、复制与黏贴

| 按键           | 功能                                       |
| ------------ | ---------------------------------------- |
| x, X     | 在一列字当中，x 为向后删除一个字符 （相当于 [del] 按键）， X 为向前删除一个字符（相当于 [backspace] 亦即是倒退键） （常用） |
| nx           | n 为数字，连续向后删除 n 个字符。举例来说，我要连续删除 10 个字符， “10x”。 |
| dd       | 删除光标所在的那一整列（常用）                          |
| ndd          | n 为数字。删除光标所在的向下 n 列，例如20dd 则是删除 20 列 （常用） |
| d1G          | 删除光标所在到第一列的所有数据                          |
| dG           | 删除光标所在到最后一列的所有数据                         |
| d$           | 删除光标所在处，到该列的最后一个字符                       |
| d0           | 那个是数字的 0 ，删除光标所在处，到该列的最前面一个字符            |
| yy       | 复制光标所在的那一列（常用）                           |
| nyy          | n 为数字。复制光标所在的向下 n 列，例如20yy 则是复制 20 列（常用） |
| y1G          | 复制光标所在列到第一列的所有数据                         |
| yG           | 复制光标所在列到最后一列的所有数据                        |
| y0           | 复制光标所在的那个字符到该列行首的所有数据                    |
| y$           | 复制光标所在的那个字符到该列行尾的所有数据                    |
| p, P     | p 为将已复制的数据在光标下一列贴上，P 则为贴在光标上一列！ 举例来说，我目前光标在第 20 列，且已经复制了 10 列数据。则按下 p 后， 那 10 列数据会贴在原本的 20 列之后，亦即由 21 列开始贴。但如果是按下 P呢？ 那么原本的第 20 列会被推到变成 30列。 （常用） |
| J            | 将光标所在列与下一列的数据结合成同一列                      |
| c            | 重复删除多个数据，例如向下删除 10 列，[10cj ]             |
| u        | 复原前一个动作。（常用）                             |
| [Ctrl]+r | 重做上一个动作。（常用）                             |
| .        | 不要怀疑！这就是小数点！意思是重复前一个动作的意思。 如果你想要重复删除、重复贴上等等动作，按下小数点“.”就好了！ （常用） |

### 一般指令模式切换到编辑模式可用的按钮说明
| 按键           | 功能                                       |
| ------------ | ---------------------------------------- |
| i, I | 进入插入模式（Insert mode）：i为“从目前光标所在处插入”， I为“在目前所在列的第一个非空白字符处开始插入”。 （常用）|
| a, A | 进入插入模式（Insert mode）：a为“从目前光标所在的下一个字符处开始插入”， A 为“从光标所在列的最后一个字符处开始插入”。（常用）|
| o, O | 进入插入模式（Insert mode）：这是英文字母 o 的大小写。o为“在目前光标所在的下一列处插入新的一列”； O 为在目前光标所在处的上一列插入新的一列！（常用）|
| r, R | 进入取代模式（Replacemode）：r 只会取代光标所在的那一个字符一次；R会一直取代光标所在的文字，直到按下 ESC 为止；（常用）|
| [Esc] | 退出编辑模式，回到一般指令模式中（常用）|

### 一般指令模式切换到命令行界面的可用按钮说明
| 按键           | 功能                                       |
| ------------ | ---------------------------------------- |
| :w | 将编辑的数据写入硬盘文件中（常用）|
| :w! | 若文件属性为“只读”时，强制写入该文件。不过，到底能不能写入， 还是跟你对该文件的文件权限有关啊！|
| :q | 离开 vi （常用）|
| :q! | 若曾修改过文件，又不想储存，使用 ! 为强制离开不储存盘案。|
| :wq | 储存后离开，若为 :wq! 则为强制储存后离开 （常用） |
| ZZ | 这是大写的 Z 喔！若文件没有更动，则不储存离开，若文件已经被更动过，则储存后离开！|
| :w [filename] | 将编辑的数据储存成另一个文件（类似另存新文件）|
| :r [filename] | 在编辑的数据中，读入另一个文件的数据。亦即将“filename” 这个文件内容加到光标所在列后面|
| :n1,n2 w [filename]| 将 n1 到 n2 的内容储存成 filename 这个文件。|
| :! command | 暂时离开 vi 到命令行界面下执行 command 的显示结果！例如 “:! ls /home”即可在 vi 当中察看 /home 下面以ls 输出的文件信息！|
| vim 环境的变更| vim 环境的变更|
| :set nu | 显示行号，设置之后，会在每一列的字首显示该列的行号|
| :set nonu | 与 set nu 相反，为取消行号！|