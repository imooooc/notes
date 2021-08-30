# 一天一个MAC小技巧

##  在 Finder 的右键菜单中添加「Open in VSCode」

> 一般在finder中查看项目的时候，因为代码没有高亮显示，所以看起来不方便，然后就要选择一个编辑器打开它，但是又得先打开编辑器，然后再在编辑器里打开本地目录找到这个项目的位置，每次这么做总觉得很麻烦，网上一找果然有解决办法。

本文记录如何利用 macOS 的「自动操作」，在 Finder 右键菜单中添加「Open in VSCode」功能。



操作步骤如下：

- 打开 Automator.app（自动操作）
- 选择 Quick Action（快速操作）
  ![img](https://liam.page/uploads/images/computer-skills/quick-action.jpg)
- 配置工作流
  - Workflow recieves current（工作流接受当前），选择 files or folders（文件或文件夹）
  - in（位于），选择 Finder（访达）
    ![img](https://liam.page/uploads/images/computer-skills/workflow-recieves.jpg)
- 新增一个 Open Finder Items（打开访达项目）的操作
  - open with（打开方式），选择 Visual Studio Code.app
    ![img](https://liam.page/uploads/images/computer-skills/open-finder-items.jpg)
- 保存为 `Open in VSCode`
  ![img](https://liam.page/uploads/images/computer-skills/save-open-in-vscode.jpg)

此时，在 Finder 中选中任意目录，而后右键 -> Quick Actions（快速操作）-> `Open in VSCode` 即可用 VSCode 打开选中的目录。

![img](https://liam.page/uploads/images/computer-skills/open-in-vscode.jpg)



-----

## 自定义一个创建文件的功能

在Finder中只能新建目录，但不能新建文本文件，这一点很不方便，以前我是直接在terminal中通过vim 创建一个文本文件的，但不是大多数人都会使用vim的，Mac难道就没有像windows那样的可以新建文本文件的功能吗，其实有的，只不过需要配置一下。

[原文链接](https://www.zhihu.com/question/20883777/answer/81780928)

Automator 新建一个 Application

![img](https://doc.vmoli.com/md/2021-08-29-084020.png)


添加一个动作 "Run AppleScript"

![img](https://doc.vmoli.com/md/2021-08-29-84026.jpg)


代码如下

```applescript
on run {input, parameters}

	tell application "Finder"
	set selection to make new file at (get insertion location)
	end tell

	return input
end run
```



保存到 "应用程序"文件夹, 名字姑且叫 "New File.app" 吧.

Finder 工具栏右键, 自定义, 然后把 New File.app 拖上去, 大功告成,

![img](https://doc.vmoli.com/md/2021-08-29-084021.jpg)![img](https://doc.vmoli.com/md/2021-08-29-084024.jpg)


需要时候点击一下, 即可在当前文件夹生成一个空文件.



========================================

如果你用 Alfred 这个神器, 直接敲 new 就可以了.
如图, 刚刚上面造的小工具也可以用 Alfred 调用, 同样好使.

![img](https://doc.vmoli.com/md/2021-08-29-84023.jpg)![img](https://doc.vmoli.com/md/2021-08-29-084025.jpg)
