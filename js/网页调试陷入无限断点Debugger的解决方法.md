# 网页调试陷入无限断点(Debugger)的解决方法


1.(function anonymous() {debugger})

在网页打开控制台后，发现下面这种源码的无限 debugger
```javascript
(function anonymous(
) {
debugger
})
```

针对这种情况的处理步骤如下

1.浏览器控制台点击Console

2.输入一下代码，然后回车
```javascript
(function(){}).constructor === Function
```

3.如果返回的是true，继续输入，并回车
```javascript
Function.prototype.constructor = function(){}
```

4.切换回 Sources 选项卡，点击继续执行，无限 debugger 的问题就解决了

注意：如果第二步返回的是false,则此方法不可用


其他的一些 js 无限 debugger 的解决可以参考下面这篇文章尝试解决：
[绕过JavaScript debugger三种解决方法](https://blog.csdn.net/wh445306/article/details/103638742)、[解决浏览器调试无限debugger](https://zhuanlan.zhihu.com/p/584684377)

