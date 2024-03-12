+++
title = "[note] MIT-the missing semester —— Vim 学习笔记" 
date = 2024-03-12T15:50:50+08:00
draft = false
hidden = false
tag = ["vim","note","编程经验"]
slug = 'VIM Note'

+++
# Vim

始于1976年的Vi编辑器

持续使用，使用编辑器超过20h，可以达到之前一样的开发效率，并且在以后的coding中愈发感受到它的魅力

Vim是基于命令行当前最流行的编辑器

有多个操作模式

# Vim的哲学

Vim 是一个*多模态*编辑 器，Vim避免了使用鼠标（因为太慢了），甚至避免了使用键盘上下左右键，因为那样会导致过多的手指的移动。

这样的Vim使用的哲学使得**vim成为了一个可以跟上你的思维模式的编辑器**，因为你可以专心于coding而不是反复得切换鼠标键盘，或者是将你的finger移动到各种各样得地方上去。

# 一些解释

在课程中以及vim的一些教程中常常会出现一些符号，这代表不同的组合键，有以下几点。

1. **裸键(bare key)** 当我们说 `i` 时，就是单单指按下`i` 键
  
2. **组合键** ，拿ctrl+V举例，有三种方式，分别是`^V` ,`Ctrl-V`,`<C-V>`
  

# 编辑模式

当你打开vim时，默认打开的是普通模式(normal)。

在插入模式中按下的大多数键将进入文本缓冲区，而在普通模式中则不会

从普通模式按下`i`进入插入模式，再按下`<Esc>`回到普通模式

从普通模式按下`r`进入替换模式，再按下`<Esc>`回到普通模式

从普通模式按下`v`进入可视化模式，再按下`<Esc>`回到普通模式

从普通模式按下`:`进入命令模式，再按下`<Esc>`回到普通模式

- **普通模式(normal)**：移动光标、阅读内容、从文件到文件的跳转
  
- **插入模式(insert)**：插入文本
  
- **替换模式(replace)**：覆盖文本而不是插入文本
  
- **可视化模式（visual `v`一般、`<S-V>`行、`<C-V>`块）**：选中文本块
  
- **命令模式(command)**：用于执行命令
  

![](https://github.com/lihepei/scholarpei_blog/blob/main/scholarpei-blog/static/blog-picture/vim-note/1.png?raw=true)

由于在使用Vim时会很多次用到`ESC`键，而它在键盘的角落，所以推荐将大小写锁定键绑定为ESC键（或者用原生的`ctrl+[`）

在vim配置文件中插入`inoremap jk <esc>` ，则是讲jk映射为esc

vim支持参数输入，例如 `vim text.md`将会打开该目录下的text文件

# 操作方法

### 插入文本

切换到插入模式，插入任何你想要的文本

### 命令行

正常模式键入`:`打开命令模式，下面是常见命令。

- `:q` 退出当前窗口（quit），只有只剩一个窗口才退出vim
  
  - `:qa` 退出所有窗口(quit all)
- `:w` 保存（write）
  
  - `:w {filename}` 保存到目录下的filename文件中
- `:wq` 保存并退出
  
- `:e {filename}` 打开要编辑的文件
  
- `:ls` 显示打开的缓存
  
- `:!{command}` 在vim中执行**外部命令**,比如`:!ls`
  
- `:help {标题}` 打开帮助文档
  
  - `:help :w` 打开`:w`的帮助文档
    
  - `:help w` 打开`w` 移动的帮助文档
    

### 缓存，标签页，窗口

Vim 会维护一系列打开的文件，称为 “缓存”。一个 Vim 会话包含一系列标签页，每个标签页包含 一系列窗口（分隔面板）。每个窗口显示一个缓存。跟网页浏览器等其他你熟悉的程序不一样的是， 缓存和窗口不是一一对应的关系；窗口只是视角。一个缓存可以在*多个*窗口打开，甚至在同一 个标签页内的多个窗口打开。这个功能其实很好用，比如在查看同一个文件的不同部分的时候。

Vim 默认打开一个标签页，这个标签也包含一个窗口。

# 普通模式

vim界面本身是一种程序语言，键入命令本身就是一种操作，这些操作使得vim的编辑十分高效

### 信息

- `^G` 显示光标当前行位置以及文件状态信息
  
- `:help <command>` 给出命令的帮助文档
  

### 移动

- 基本移动：`hjkl` 左下上右
  
- 词的移动：`w` 下一个词，`b` 词初，`e` 词尾
  
- 行的移动： `0` 行初，`^` 第一个非空格字符，`$` 行尾
  
- 段的移动：`{` 以及`}` 是按段移动
  
- 翻页： `^U`向上翻页(up) ，`^D` 向下翻页(down)
  
- 屏幕： `H` 屏幕首行(Highest)，`M`屏幕中间(middle)，`L` 屏幕底部(lowest)
  
- 文件： `gg`文件头，`G`文件尾
  
- 查找： `f{字符}`向后查找这行字符(find)，`F{字符}`向前，`t{字符}`向后跳转到该字符光标前一位(to)，`T{字符}` 向前跳转到光标后一位
  
- 搜索: 
  
  - `/{正则表达式}`, `n` 是对后面匹配的式子搜索，`N`是对前面匹配的式子搜索
    
  - `? {正则表达式}` 逆序搜索
    
- 杂项： `%` （找到配对并移动到另一边，比如括号或者 /* */ 之类的注释对）
  
- 移动到行数： `:{行数}<CR>` 或者 `{行数}G` ({行数} 为行数)
  
- 返回： `^O`回到之前的位置，`^I` 回到较新的位置
  

### 编辑

- `O` 在上方 `o` 在下方插入行，并转到insert模式
  
- `a` 光标移动到下一位并转到插入模式
  
- `A` 光标转到行末并转到插入模式
  
- `u` 撤销(undo) ，`U` 恢复到本行的初始状态
  
- `^r` 重做(redo)
  
- `d{移动命令}` 删除 {移动命令}
  
  - 例如，`dw` 删除词，`d$` 删除到行尾，`d0` 删除到行头，`dd`删除当前行。
    
  - 当删除整行`dd`时，该行会被保存到Vim寄存器中，按`p`则会复制到光标所在行的下一行
    
- `c{移动命令}` 改变 {移动命令}，相当于d命令然后转到插入模式，`cc`删除当前行并转到插入模式
  
- `x`删除光标的字符（相当于`dl`）
  
- `s`替换字符并转到插入模式（相当于`xi`）
  
- `r{字符}`替换光标字符为指定字符
  
  - `:r {filename} ` 将filename文件内容提取到光标处
    
  - `R` 进入替换模式，相当于删除一个字符插入一个字符的模式
    
- `y` 复制 "yank"是vim中复制的专用术语 ；`yy` 复制当前行；同样可以搭配移动命令，如`yw`
  
- `p` 粘贴 (paste)
  
- `v` 进入可视化模式，并进行操作
  
  - 选中文字，进行复制、删除、改变等操作
    
  - `V`进入行可视化，选择则是每行
    
  - `^v`进入块可视化，选择是矩形形状的
    
- `~`对选择的文本将大小写反转（先再可视化模式选择文本）
  
- `.` 重复上一次操作的命令
  

### 替换

- `:s/sth1/sth2`替换命令将光标所在行第一个匹配替换为sth2，如果是`:s/sth1/sth2/g` 则是替换全行的匹配串
  
- `:#,#s/sth1/sth2/g` #,#代表的是替换操作若干行的首尾行
  
- `:%s/sth1/sth2/g` 替换整个文件的匹配串
  
- `:%s/sth1/sth2/gc` 替换整个文件，并且对每个替换**提示是否替换**
  

### 计数

在操作前加上数字，会执行操作若干次

EX.

- `3w` 向后移动三个词
  
- `5j`向下移动5行
  
- `7dw` 删除后面7个词
  

### 修饰语

用修饰语改变名词的意思，修饰语有`i` 内部的，`a` 周围/包括

- `ci(` 改变当前括号内的内容
  
- `ci[` 改变当前方括号内的内容
  
- `da'`删除一个单引号字符串，包括周围的单引号
  

### 多窗口

- 用`:sp` / `:vsp` 来分割窗口
  
- 同一个缓存可以在多个窗口中显示
  
- `^W` 在不同窗口之间跳转
  

### 设置类命令

- `:set ic` igore case 忽略大小写
  
- `:set noic` 不要忽略大小写
  
- `is` incsearch 查找短语时显示部分匹配
  
- `hls` hlsearch 高亮显示所有的匹配短语
  

# 自己做的配置

搭配了vim 的easymotion，在vscode中vim的配置文件`setting.json`中修改

### easymotion

```
// settings.json
{
    "vim.leader": " ",

    "vim.easymotion": true,
    "vim.easymotionKeys": "hklyuiopnm,qwertzxcvbasdgjf;",
    "vim.easymotionJumpToAnywhereRegex": "\\b[A-Za-z0-9]|[A-Za-z0-9]\\b|_.|#.|[a-z][A-Z]",
    "vim.normalModeKeyBindingsNonRecursive": [
        {
            // toggle easy motion, but two word
            "before": [ "f" ],
            "after": [ "leader", "leader", "2", "s" ],
        },
        {
            // toggle easy motion
            "before": [ "s" ],
            "after": [ "leader", "leader",  "s" ],
        },
    ],
}
```

`vim.normalModeKeyBindingsNonRecursive` 这个的意思是， vim 的 normal 模式下的快捷键配置，并且覆盖原本 vim 的配置 (NonRecursive)

easymotion设置快速跳转，用 `f` 代替两个字母的快速跳转，`s` 代替一个字母的快速跳转

### 代码跳转

```
"vim.normalModeKeyBindingsNonRecursive": [
    // ...
    {
        // get info
        "before": [ "leader", "i" ],
        "commands": [ "editor.action.showHover" ],
    },
]
```

`[leader] + i`显示代码信息，**替代鼠标悬停获取对象信息**

```
"vim.normalModeKeyBindingsNonRecursive": [
    {
            // goto error
            "before": [ "g", "e" ],
            "commands": [ "editor.action.marker.nextInFiles" ],
    },
],
```

`ge` ：goto error (warning, info)， **跳转到项目有 Error，Warning 或 Info 的地方**。这个能帮我们快速定位因为我们修改代码导致出问题的地方。