@(笔记与心得)[Swift]

#Protocol and value-oriented programming - 2016 WWDC 419


##Key Point: Local Reasoning 
什么是 Local Reasoning ？今年的 WWDC 多次提到了这一概念。Local Reasoning 是指，当你看到项目中的一段代码时，你不需要去考虑项目中其他部分的代码，即局部性。符合 Local Reasoning 要求的代码易于维护、易于测试，即使是第一次查看项目的人，也能较为容易得理解代码的结构。

Local Reasoning 非常重要，今年 WWDC 419 中讲到所有关于 Protocol 和 value-oriented 的内容，几乎都是为了实现 Local Reasoning。

为了更好得说明 Local Reasoning 涉及的概念，我将创建一个有关汽车的小型项目。在这个 App 中，我们可以查看汽车速度、外观等信息。

##使用 Class 所带来的问题
首先，我们尝试用 Class 来构造项目中的 Model，用来表示有关汽车的信息，包括速度、颜色以及品牌：

```swift
class Car {
	var speed: CGFloat
	var color： UIColor
	var brand： String

	...
}

var car1 = Car(...)
var car2 = car1
car2.speed = 220.0
```

可以看到，由于 Class 是 Reference Semantics，当我们修改第二辆车的信息，第一辆车 car1 也会改变。这显然不符合 Local Reasoning 的要求，当我们修改项目中的一部分代码，可以会影响另一部分代码，最终造成程序变得非常的复杂，难以管理。同时，由于不同的变量间存在的依赖关系，我们也很难单独得测试某一段代码。

为了解决上面说到的问题，我们使用 Struct 来替代 Class：

```swift
struct Car {
	var speed: CGFloat
	var color： UIColor
	var brand： String

	...
}

var car1 = Car(...)
var car2 = car1
car2.speed = 220.0
```
现在，当我们修改 car2 中的信息时，car1 不会改变了。

##如何在 view Controller 中应用 value type

假设我们在用户查看车辆具体信息时，播放一段背景音乐，并放大车辆的图片，并在用户回到主界面时，停止播放音乐。于是，每一次用户在两个不同的界面切换时，我们都需要播放/停止音乐并放大/缩小图片，并且这两个状态下的代码分散在 view Controller 的不同部分。当不同状态下需要调整的参数不止两个，越来越多时，我们的 view Controller 会变得非常混乱。

借助 value type，可以很好得解决这个问题，我们一起来看看这一次 WWDC 上给出的解决方案：

首先，创建一个 enum 数据类型来表示不同的状态，我在这里用 `viewing` 来表示主界面的状态，用 `detail` 来表示显示具体信息的界面 ：

```swift
class CarViewController : UIViewController {
	var state: State


	...
}
enum State {
	case viewing
	case detail
}
```
将所有的状态放到一个数据类型中后，我们就不用担心会遗漏某一个状态了。接下来，我们创建一个函数来专门处理不同状态下参数的变化。

```swift
func StateDidChange() {
	switch state {
		case .viewing:
			music.stop()
			myImage.smaller()
		case .detail:
			music.start()
			myImage.bigger()
	}
}
```

通过上面两个步骤，我们将所有不同的状态及不同状态下参数的变化统一起来，放在一个地方管理，避免了代码分散造成的混乱。类似的，我们可以使用这种方法管理 Model 。



