> 翻译官方文档: [iOS Human Interface Guidelines-Preview](https://developer.apple.com/ios/human-interface-guidelines/)

## 概述
### 设计原则
作为一个APP设计师,你有机会推出一个很酷的作品,并晋升到APP Store Top排行榜.为了达到这个目的,你需要满足用户对APP的质量和功能的高期望.  
有三个主要主题使iOS区别于其他平台:  

* **清晰**.贯穿着整个系统,任何字号的文本都是清晰的,图标是清晰易懂的,装饰是恰当好处的,和突出重点功能驱动着设计, 负空间,颜色,字体,图形和界面元素巧妙地突出重要内容和传达交互性.  
* **遵从**.流体运动和简洁,精美的界面帮助用户理解内容并与之进行交互,而不是跟用户作对.应用内容通常充满整个屏幕,毛玻璃效果经常暗示更多.最低限度地使用边框(bezel),渐变和阴影能保持界面明亮且清新,与此同时确保应用内容是最核心的.  
* **深度**.独特的视觉层次和真实的动作传达层次感,赋予活力和促进理解.触摸和可发现性能提高乐趣,能够访问功能和额外内容,且没有丢失上下文.当用户通过内容导航(navigate)时,过场(Transitions)提供了深度感.(译注:导航栏`UINavigationController`在`push`一个`UIViewController`时,过场动画`Transitions animation`能给用户一种深度感)

为了最大化影响力和范围.从考虑你的APP ID开始,就请牢记下列的准则:  
* **美学完整性**  
  美学完整性代表APP外貌和行为与功能完美地融为一体.例如,一个APP在为帮助用户执行严肃的任务时,通过使用微妙的,不起眼的图形,标准控制和可预测行,能让他们保持专注.另一方面,一个沉浸式APP,例如游戏,可以传递一个迷人的外表,保证内容有趣且令人兴奋的,同时刺激用户的探索欲望.  

* **一致性**  
  一个始终如一的APP通过系统提供的界面元素,知名的图标,标准的文本格式和同一的术语实现熟悉的标准和模式.APP以用户期望的方式结合了特性与行为.  

* **[直接操纵](http://blog.csdn.net/richardbao2000/article/details/1442031)**  
  单屏幕内容的直接操纵吸引用户和促进理解.用户在转动屏幕或使用手势影响屏幕内容时体验直接操纵.通过直接操纵,他们可以看到动作能立即地,明显地展示结果.  

* **反馈**  
  反馈接收动作和显示结果让用户保持消息灵通.内置的iOS APP提供明显的反馈来响应用户的动作.交互元素在轻击时短暂地高亮,进度指示器显示长时间运转操作的状态,动画和声音都能帮助用户识别当前动作的结果.  

* **[隐喻](http://xueshu.baidu.com/s?wd=paperuri%3A%280b2f8030b39e769dc4e0e39eda2795f3%29&filter=sc_long_sign&tn=SE_xueshusource_2kduw22v&sc_vurl=http%3A%2F%2Fwww.doc88.com%2Fp-9972675554480.html&ie=utf-8&sc_us=11208142479284131959)**   
  用户能在一个APP的可视对象和动作隐喻了熟悉的经验--不论是否来自现实或虚拟世界的环境中快速上手.隐喻在iOS上很有用是因为用户在手机屏幕上进行物理交互.用户挪开视图,暴露内容下方的内容.他们拖动和滑动内容.他们打开开关,移动滑动条,滚动选择器的数值.他们甚至翻书页和翻杂志.

* **用户控制**  
  贯穿整个iOS系统,是用户-而不是APP-在控制.APP可以猜测一个行动或警告一些危险的结果,但是app经常错误地接管用户的决策.最优秀的APP能在让用户做决策与避免不必要的结果之间找到一个合理的平衡.通过保持交互元素可熟悉的和可预见的,批准破坏性行为,轻松地取消操作,甚至是进行中的操作等,一个APP可以让用户感觉他们在控制他们的APP.

###iOS 10的新内容  
在iOS 10,你可以建立比以前更厉害的APP.  
![power image](https://developer.apple.com/ios/human-interface-guidelines/images/whatsnew_messaging.png)

* **搜索屏幕和Home屏幕上的小部件(widget)** 小部件提供节省时间的,有用的信息或不用打开APP就能使用特定功能.在过去,用户添加小部件到通知中心(Notification Center)来快速访问.现在,用户添加小部件到搜索屏幕,而用户只需在Home屏幕和锁屏时向右滑动就能看到搜索屏幕.当用户使用3D Touch按压Home屏幕上的APP图标时,还可以在弹出的动作列表中加入小部件.小部件的设计与行为已发生改变.确保回顾和相对应地更新已存在的设计.查看[小部件](https://developer.apple.com/ios/human-interface-guidelines/extensions/widgets/)

* **集成Message** 通过实现出现在一个Messages聊天界面下方和能让用户分享APP特有的内容给好友的信息扩展(Message extension),APP可以与Message集成.APP可以分享文字,照片,视频,贴纸,甚至Message游戏之类的交互内容.查看[消息传送](https://developer.apple.com/ios/human-interface-guidelines/extensions/messaging/)  

![siri](https://developer.apple.com/ios/human-interface-guidelines/images/whatsnew_siri.png)  

* **集成Siri** APP可以集成Siri,让用户使用语音来执行APP的特定动作,例如打电话,发送信息,开始锻炼等.查看[Siri](https://developer.apple.com/ios/human-interface-guidelines/features/siri/)  

* **扩展通知中心** 当用户使用3D Touch按压通知中心或在一个无锁状态的设备上下拉通知中心时,使用一个扩展的详细视图来增强通知中心.使用这个扩展视图让用户快速获得更多通知中心的信息并且在没有丢失上下文的情景下,立即执行动作.查看[通知中心](https://developer.apple.com/ios/human-interface-guidelines/features/notifications/)  

### 界面要点
大多数iOS APP使用UIKit中的组件构建.UIKit,是一个定义了共同接口元素的编程框架.这个框架让app实现在不同系统上的统一的外观,同时提供高度自定义方案.UIKit元素灵活且熟悉.他们适应力强,足够使你设计一个在任何iOS设备上都看起来不错的APP,而且在系统引入新界面时,能自动更新.UIKit的界面元素适合下面这三种分类:  
* **Bars** 告诉用户在APP中的位置,提供导航栏和可能包含按钮和其他初始化动作,交流信息的元素.  

* **Views** 包含用户需要看到的主要内容,例如文本,图形,动画,和交互元素.视图可以允许下列行为:滚动,插入动作,删除动作,排列动作.

* **Control** 初始化动作和传递信息.按钮,开关,输入框,进度指示器都是控制的例子.

除了定义iOS的界面,UIKit还定义APP可以适应的功能.通过这个框架,APP可以在触摸屏上响应手势,使绘画,可得性,打印这些特性生效.  

iOS跟其他编程框架和技术紧密结合,如Apple Pay,HealthKit,和ResearchKit,让你设计强大的APP.
