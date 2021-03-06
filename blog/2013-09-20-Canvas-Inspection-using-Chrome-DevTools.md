[翻译]使用Chrome开发者工具调试Canvas
===

####简介
使用过Canvas元素的人都知道Canvas很难调试。使用Canvas通常要调用一长串难以跟踪的API。  
```js
function draw(){
  context.clearRect(0, 0, 258, 258);
  context.fillStyle = '#EEEEEE';
  context.beginPath();
  context.arc(129, 129, 127, 0, 6.28, true);
  context.closePath();
  context.fill();
}
```
有时候你希望记录发送给Canvas上下文的指令，并且逐条执行指令。幸运的是在Chrome开发者工具里有一个新的Canvas审查特性可以帮你做到这一点。  

在这篇文章里，我会给你演示怎样使用这个特性来调试你的Canvas行为。它支持2D和WebGL上下文，所以无论你在使用哪个，都可以使用它直接获取调试信息。  

####准备开始
在Chrome地址栏打开`about:flags`页面，并选中“启用开发者工具实验”（英文：Enable Developer Tools experiments，以上是Chrome中文版翻译）。  
![Figure 1 - Enabling Developer Tools Experiments in about:flags](http://www.html5rocks.com/static/demos/canvas-inspection/images/enable-canvas-inspection.png)  

接下来，打开开发者工具并点击右下角的齿轮图标，在新打开配置页面中可以找到**实验（Experiments）**一栏，然后在该栏中启用**Canvas inspection（Canvas 审查）**。  
![Figure 2 - Enabling Canvas inspection in DevTools’ experiments](http://www.html5rocks.com/static/demos/canvas-inspection/images/experiments.png)  

为了让这些修改生效，你必须关闭然后再打开开发者工具。当开发者工具再次打开时，找到Profiles一栏，你可以看到Canvas Profile选项，它是禁用状态，如果某个页面有你想要调试的Canvas，你可以按下Enable按钮然后页面会重新载入，准备记录Canvas调用。  
![Figure 3 - Switching on the Canvas Profiler](http://www.html5rocks.com/static/demos/canvas-inspection/images/canvas-profiler.png)  

然后你需要决定你是记录单帧（Single Frame），还是看起来很像开发者工具中时间轴的连续多帧（Consecutive Frames）。  

||一帧表示页面中的一次事件循环，涉及到执行Javascrit代码，事件处理，更新DOM，修改样式，布局和重绘。为了让动画更加平滑，你要让每帧耗时小于1/60秒，即16.6毫秒。

单帧仅仅记录本帧结束前所有的API调用，多帧则记录你手动停止之前的所有API调用。选择哪一种取决于你如何使用canvas元素。对于一个持续的动画，选择单帧更好，而那些用于响应用户事件的短暂动画，就应该使用多帧。  

![Figure 4 - Choosing how many frames to capture](http://www.html5rocks.com/static/demos/canvas-inspection/images/frames.png)  

这样我们就做完了所有的准备工作可以记录帧了。  

####记录帧
按下Start按钮，像平常一样与应用交互，你可以看到它已经在记录了。如果你要记录多帧，那你还需要回到开发者工具按下Stop按钮来结束记录。  

现在你可以看到左侧有一个profile列表中有了一个新的profile，里面记录了所有canvas元素上下文的调用。点击profile就可以看到如下界面：  

![Figure 5 - A canvas profile in DevTools](http://www.html5rocks.com/static/demos/canvas-inspection/images/profile.png)  

你可以看到一组已经记录下来的帧，并且一步一步地浏览它们。一旦你点击了其中一个，你就可以看到该帧结束时Canvas的屏幕截图。如果有多个Canvas元素，你可以通过屏幕截图下面的菜单选择显示哪个Canvas。  

![Figure 6 - Choosing your canvas context](http://www.html5rocks.com/static/demos/canvas-inspection/images/expanded-menu.png)

在帧里你可以看到多个绘画调用组（Draw Call Group），每个绘画调用组包含一个绘画调用，并且是该组的最后一个调用。那什么是绘画调用呢？对于一个2D上下文来讲，它包括clearRect(), drawImage(), fill(), stroke(), putImageData()和其他文字渲染方法；对于一个WebGL上下文来讲，它包括clear(), drawArrays()和drawElements()。本质上讲，任何会修改当前绘画缓存内容的调用都是绘画调用（如果你不熟悉图形编程，你可以认为缓存就是一个我们正在操作的像素位图）。  

你可以在帧，绘画调用组和调用这三个级别依次查看该列表。无论哪一种，你都可以看到那时的上下文，这意味着你可以迅速发现和修正Bug。  

![Figure 7 - navigation buttons for convenient list hopping](http://www.html5rocks.com/static/demos/canvas-inspection/images/replaytime.png)

####找出属性变化
另一个有用特性就是可以找出两次调用之间属性和变量发生了什么变化。  

（译者注：在Chrome29上没有找到该按钮，但是canary Chrome31上可以找到）点击右侧按钮![](http://www.html5rocks.com/static/demos/canvas-inspection/images/sidebar.png)会打开一个新的面板，随着一步一步地查看API调用，你可以看到属性的变化，当鼠标放在任何Buffer和数组上时，你可以看到他们的内容。  

![](http://www.html5rocks.com/static/demos/canvas-inspection/images/diff.gif)  

####注意了！
现在你已经知道了怎样在Chrome开发者工具中调试Canvas。如果你对这个工具有任何回馈，请[提交一个bug](http://crbug.com/new)或者写信给[Chrome开发者工具工作组](https://groups.google.com/forum/#!forum/google-chrome-developer-tools)，告诉我们，你发现了bug或者在调试过程中你想看到哪些信息，因为只有开发者的回馈才能让它更好。
