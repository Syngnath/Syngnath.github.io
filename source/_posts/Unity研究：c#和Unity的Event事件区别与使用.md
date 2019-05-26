---
title: Unity研究：C#和Unity的Event事件区别与使用
categories:
  - Unity
tags:
  - Unity
comments: true
img: 'https://synp.oss-cn-hongkong.aliyuncs.com/UnityLogo.png'
date: 2019-05-26 12:45:00
---
# 前言
现在设计游戏趋向于事件驱动，即当一个事件发生时，如主角捡到一个血包，发射一颗子弹，或者游戏里所有的物体需要进入第二阶段的状态，事件被广播给游戏中的相关对象，这样它们可以恰当做出反应，事件一般是以**数据包**形式送出，数据包包含事件的信息，如发送者，或者时间戳等，根据实际情况决定。而这些的一切都要围绕**Event**作为核心，不过在说明Event之前先了解它的前身--**delegate**。

# Delegate
## 委托的基本声明和基本使用
*C# 中的委托（Delegate）类似于 C 或 C++ 中函数的指针。委托（Delegate） 是存有对某个方法的引用的一种引用类型变量。引用可在运行时被改变。*
简单来说，**委托就是存储方法容器**。C# 基于这个基础做了一定优化封装。下面介绍下delegate基本使用:
我想声明一个叫BroadCast的委托，将来用于存放方法，它接收string参数，并且返回值为void。而声明一个委托相当于声明一个新类，所有可以声明类的地方都可以声明委托。
`public delegate void BroadCast(string msg);`
这样它就是个可以存放和它标签一样，即同样是接收string参数，返回void的方法。
接着我们使用它去定义一些事件容器，如
```
public BroadCast broadCastA;
public BroadCast broadCastB;
```
好了，接着在下面添加两个方法，一个叫BroadCastCore，用于调用委托里存放的东西，另外一个叫Func1的方法，Func1存放到broadCastA内，和broadCastA具备同样的标签，只要对broadCastA执行Invoke方法并且传递对应参数，那么里面存放的对应方法就会被调用并且接受到传递过来的参数。
```
void BroadCastCore(){
  // new一个委托对象
  broadCastA = Func1; //或者broadCastA = new broadCastA(Func1); 前者实际上等于后者，只是编译器帮忙省略了new的步骤
  // 调用委托
  broadCastA.Invoke("");
}

void Func1(string receiveMsg){
  print("I Receive the msg : receiveMsg");
}
```

## 多播委托
多播委托使用了一个叫**委托链**的概念，简单来说就是把很多方法存放到一个容易中，然后调用这个容器时容器会将里面的方法每个都调用一次。
使用 `+=` 关键词可以注册方法
broadCastA += Func1;
broadCastA += Func2;
broadCastA += Func3;
使用 `-=` 关键词可以卸掉原来注册方法
broadCastA -= Func1;
broadCastA -= Func2;
broadCastA -= Func3;
所以对相关包含委托类封装时要提供注册和卸除两个方法

但是这里有个坑，因为一旦不小心使用了 `=` 就会重置覆盖掉之前所有注册的方法如：
broadCastA += Func1;
broadCastA += Func2;
broadCastA = Func3;
事实上只存放Func3方法。

所以接下来介绍下面的 'event' 修饰关键词

## event修饰词
在原来委托定义基础上，加上event修饰词：
public event BroadCast broadCastA;
好了，现在 `=` 就会被禁止使用了，只能使用 `+=` 和 `-=` 增加和删除，所以这就是多播委托的最终形态了。

# UnityEvent
UnityEvent基于C#委托的基础作出一定的封装，并且融入到Unity框架里，既可以把它当做一个普通委托在脚本中使用，也可以把它public在Unity面板中拖拽注册事件。
## 声明和定义
创建一个继承UnityEvent的类即可如：
```
public class BossEvent : UnityEvent<string>
{
}
```
和之前上面c#委托声明 `public delegate void BroadCast(string msg);` 一个意思

接着定义一个事件容器，用属性来自动new一个Event对象
```
BossEvent informEvent;
public InformEvent 
{
    get
    {
        if (informEvent == null)
            informEvent = new BossEvent();
        return informEvent;
    }
    set { informEvent = value; }
}
```

## 事件注册
首先F12看UnityEvent写了什么
namespace UnityEngine.Events
{
    public abstract class UnityEvent<T0> : UnityEventBase
    {
        [RequiredByNativeCode]
        public UnityEvent();

        public void AddListener(UnityAction<T0> call);
        public void Invoke(T0 arg0);
        public void RemoveListener(UnityAction<T0> call);
        protected override MethodInfo FindMethod_Impl(string name, object targetObj);
    }
}

需要传入UnityAction声明的参数，尝试声明一个UnityAction发现它利用多态性封装了0-4个泛型参数的Action无返回值委托
```
public delegate void UnityAction();
public delegate void UnityAction<T0>(T0 arg0);
public delegate void UnityAction<T0, T1>(T0 arg0, T1 arg1);
...
```
那么明白本质后用起来就很简单了
- Lambada
informEvent.AddListener((msg)=>{print("Get Msg:" + msg)});
Lambada法力无边，另外注意Lambada里面方法如果调用外部变量，可能会导致GC
- 方法
informEvent.AddListener(同样签名方法名);

# EventSystem和Event
Unity里的EventSystem和Event主要是负责一些UI状态，用户输入输出，鼠标点击等事件的封装和监控。
```
using UnityEngine;

public class Example : MonoBehaviour
{
    void OnGUI()
    {
        Event m_Event = Event.current;

        if (m_Event.type == EventType.MouseDown)
        {
            Debug.Log("Mouse Down.");
        }

        if (m_Event.type == EventType.MouseDrag)
        {
            Debug.Log("Mouse Dragged.");
        }

        if (m_Event.type == EventType.MouseUp)
        {
            Debug.Log("Mouse Up.");
        }
    }
}
````

这里不再阐述，其主要是负责Unity框架内事件的监听和接口封装，有需要使用时查API即可。

# 总结
事件驱动模式可以帮助我们解决不同模块间的交流而不得不对象持有相互对象从而导致耦合性增加的问题，比较常用主要是两种：
- 订阅-广播 模式
通过封装一个<string, UnityEvent>字典，提供事件的注册和卸除接口,再创建一个数据包的类DataBundle封装数据，从而实现广播数据包给已注册的对象。
DataBundle可以封装一个<string, object>用于存放一些事件属性或者其他任意的东西，从而使事件本身带有一些数据或者一些状态，一些属性，更加多样化。

- 主动监听模式
核心思想是**谁感兴趣，谁主动监听**，一般使用观察者模式是在比较具体的模块上使用，根据实际情况增添相应的接口。
大概使用方法就是：
**枚举类EventType**：
存放事件，里面的事件对应的编号要明确写出，不要自动迭代
**管理类EventMng**：
EventType 定义的变量 eventMain
提供一个事件的监听方法
`Observe(EventType type){return eventMain==type;}`
给外部监听
提供一个事件的触发方法
`Trigger(EventType type){}`
给外部触发
Update：监控eventMain，如果eventMain不为null，则在**下一帧**清除
**兴趣类Person**:
这时候假设有个类Person对EventType里某个事件感兴趣
这时候只要在update里
if(EventMng.Instance.Observe()){
  //执行代码段
}
就能监听对应事件执行相应代码

**可以看到，主动监听模式使用起来比较简单，并且不需要事件的注册，只需要一个存放事件一帧的容器即可，简单，有效**

