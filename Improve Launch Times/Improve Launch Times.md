@(笔记与心得)[iOS]

#Improve Launch Times

在这篇文章中，我会总结一下 2016 WWDC 上关于优化 App 启动时间的演讲，整理出一些实用的内容，而演讲中偏理论的内容由于我不是很理解，这里将暂时跳过。

##你的应用启动速度需要多快

你的 App 的启动速度应该比开启应用时的动画特效的速度要快（从主屏到你的 App 转变的动画 ），更精确的答案是，400 毫秒。

需要注意的是，App 在不同设备上的启动时间是不同的，所以不要忘了测试性能较低的设备。

##区分 Warm launch 和 cold launch

Warm launch 是指 App 和数据已经在内存中时启动，而 cold launch 是指 App 不在内存中时启动。Warm launch 和 cold launch 的启动时间是不同的，并且 cold launch 的启动时间更为重要，因为 cold launch 的启动时间往往决定了你的 App 给用户的第一印象如何。

要在 Xcode 中测试 cold launch 时间的话，你应该在每一次的测试后 reboot（重新启动）设备。

##启动时间的测量

我们可以使用 `DYLD_PRINT_STATISTICS` 来测量启动时间。值得一提的是，这一环境变量在新的 OSes 中有了极大的改进，提供了更多有价值的信息。

 ![Alt text](./屏幕快照 2016-06-22 上午2.53.27.png)

设置完成之后，我们就可以在 console 中看到如下的输出了：

```
Total pre-main time: 10.6 seconds (100.0%)
         dylib loading time:  240.09 milliseconds (2.2%)
        rebase/binding time:  351.29 milliseconds (3.3%)
            ObjC setup time:  11.83 milliseconds (0.1%)
           initializer time:  10 seconds (94.3%)
           slowest intializers :
                  MyAwesomeApp :  10.0 seconds (94.2%)
```

##优化启动时间的具体策略
###Dylib Loading
嵌入动态库的代价是很昂贵的。尝试限制动态库（dylib）的数量，如果你必须要使用动态库的话，尽可能得将它们合并。

![Alt text](./屏幕快照 2016-06-22 上午10.58.36.png)


###Rebase/Binging
另一个策略是减少 _DATA pinters，在实际应用中，这意味不要过度使用 class，selectors（而是多使用 Swift 中的 structs）

![Alt text](./屏幕快照 2016-06-22 上午10.58.22.png)


###使用 Swift
使用 Swift 同样可以优化你的启动速度

