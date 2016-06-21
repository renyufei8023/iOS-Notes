###光标控制
* ctrl + a: 到行首
* ctrl + e: 行末
* ctrl + f/b: 前进后退，相当于左右方向键，但是显然比移开手按方向键更快
* ctrl + p: 上一条命令，相当于方向键上
* ctrl + r: 搜索命令历史，这个大家都应该很熟悉了
* ctrl + d: 删除当前字符
* ctrl + h: 删除之前的字符
* ctrl + w: 删除光标前的单词
* ctrl + k: 删除到文本末尾
* ctrl + t: 交换光标处文本
* ⌘ + —/+/0: 调整字体大小
* ⌘ + r:清屏，其实是滚到新的一屏，并没有清空。ctrl + l 也可以做到。

###窗口操作
* 新建窗口：shift + command + d（横向）command + d（竖向）
* 关闭窗口：shift + command + w
* 前一个窗口：command + `
* 后一个窗口：command + ~
* 进入窗口 1,2,3：option + command + 编号

###标签页操作
* 新建标签页: Command + T
* 关闭标签页: Command + W
* 前一个标签页: Command + 左方向键，Shift + Command + [
* 后一个标签页: Command + 右方向键，Shitf + Command + ]
* 进入标签页1，2，3…: Command + 标签页编号
*Expose 标签页: Option + Command + E（将标签页打撒到全屏，并可以全局搜索所有的标签页）

###面板操作
垂直分割: Command + D
* 水平分割: Shift + Command + D
* 前一个面板: Command + [
* 后一个面板: Command + ]
* 切换到上/下/左/右面板: Option + Command + 上下左右方向键

###其他功能
* 支持自定义全局快捷键用于显示和隐藏iTerm2 Preference -> Keys －> Show/hide iTerm2 with a system-wide hotkey 打上勾之后
* 进入和退出全屏: Command + Enter
* 查看当前终端中光标的位置: Command + /
* 命令自动补全: Command + ;（很少用这个，还是感觉Zsh的补全更好用）
* 开启和关闭背景半透明: Command + u
* 清屏（重置当前终端）: Command + r


###位置书签
在当前会话中按Command + Shift + m可以保存当前位置，之后可以按Command + Shift + j跳回这个位置。

###粘贴历史
使用Command + Shift + h 可以呼出粘贴历史，支持模糊检索。还可以设置将粘贴历史保存在磁盘上（Preferences -> General）

###即时回放

使用Command + Opt + b 打开即时回放，按Esc退出。即时回放可以记录终端输出的状态，让你“穿越时间”查看终端内容。默认每个会话最多储存4MB的内容，可以在设置中更改（Preferences -> Genernal -> Instant Replay）。
