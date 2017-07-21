### 前言
工厂模式应用非常广泛,经常可以在一些复杂的应用或框架上看见其影子.正所谓
> 没见过猪跑,但我吃过猪肉.

你可以在NSString看到工厂方法.尝试执行以下代码:

    NSString *str = @"string 1";
    NSLog(@"string 1 class : %@", [str class]);
    NSString *str2 = [NSString stringWithUTF8String:"string 2"];
    NSLog(@"string 2 class :%@", [str2 class]);
可以在控制台中看到以下日志:

    string 1 class : __NSCFConstantString
    string 2 class :NSTaggedPointerString

很明显,在分别调用不同的类方法生成了两个继承NSString的子类.在OC中,如果你看到一个类有着丰富多样的类方法来生成对象,那么它就有可能使用工厂方法.在上一次面试中,面试官问到了这个问题.我发现自己表达得很烂.as we know:
> 如果你不能将知识通过简洁的语言表达出来,那说明你还没掌握这个知识.

### 分类
一般来说,分为三个类:简单工厂,工厂方法,抽象工厂.由于简单工厂和工厂方法类似,就放在本篇讲,而工厂方法在[下一篇](http://www.jianshu.com/p/c05f00cebcb5)介绍.
他们在四人帮中([Gang of four](https://en.wikipedia.org/wiki/Design_Patterns))的定义如下:
* 简单工厂 -- 定义一个用于创建对象的没有暴露的接口,让子类决定哪一个类.
* 工厂方法 -- 定义一个用于创建对象的接口,让子类决定哪一个类.Factory Method使一个类实例化延迟到其子类.
* 抽象工厂 -- 提供一个创建一系列相关或相互依赖对象的接口,而无需指定他们具体的类.

*注:'让子类决定'的意思是在编码时无法确定具体实例化哪一个类,需在运行时(runtime)通过交互或者通过参数决定实例化哪一个类.*
### 一个例子
假如现在我们有这么一个需求:
公司正在做一个商城APP叫Bao Si APP,简称BS.在BS中,当用户未完善个人信息的时候,下单需要跳转到完善个人信息界面中完善个人信息.
这个信息界面是一个表单,包括姓名地址,性别,手机号码,当前银行卡之类.这个应该是比较简单的,所以你就开始动手写了.当你写到一半的时候,PM跑过来跟你说:"之后我们继续讨论了一下,你把这个表单改为动态的.现在的表单比较简单,以后我们需要添加其他的表单信息,比如说生日.有些信息可能变得很重要,所以这个表单是由后台的运营人员控制的.只要后台一更改,前台就得跟着改变."
这时候,你应该放下你手中的刀,并坐下来听一首<[The Wolven Storm](http://music.163.com/#/song?id=33735522)>,慢慢思考人生.
我们可以将上述的需求总结:
1. 动态性.客户端的配置应该由后台配置,我们通过这个配置来生成对应的表单.每一个表单的控件类型都由一个唯一标识符`type`控制.让`type`决定创建对应的控件对象.**即我们不知道我们要创建的具体的类**.
2. 多态性.每一个表单控件中都有相同的标题,以及类似的方法:`getValue`,`isValidate`(为了简单起见,假设这里就只有两个方法).每个控件的`getValue`方法又都不同.对于地址,需要返回`Label.text`中的值.对于日期,则需要`textfield.text`. **将一些责任(方法)委托给子类实现**.

基本上符合这两个点的时候需求的时候,就能使用简单工厂模式了.

### 简单工厂模式
按照工厂模式,因为我们不知道所要创建的具体的类,所以希望通过一个工厂类来帮我们生成对应的产品类,所以在客户端中调用工厂方法中的类方法`controlWithType:`并传入一个参数.这时候就需要知道具体什么方法,只关心从控件中取出来的值.  
于是可以得到以下的UML结构图.
![simple factory](https://cl.ly/1P1U310P0k0r/download/simple%20factory.png)

*注:在当前的需求中,每一个控件都有一个公共属性`titleLabel`,这个属性明显不属于抽象产品类.所以为了灵活起见,定义了一个基类继承抽象产品类`ControlProtocol`.这样,子类也就有了`titleLabel`,子类也能继承对应的方法.*

简单工厂有两种方法实现:
1. 参数化方法. 即在工厂类里面写一个带type类型的生产方法,在这个方法中写判断逻辑.

       @interface AKFactory : NSObject
       - (AKBasicControl *)controlWithType:(NSString *)type;
       @end

2. 为每一个产品类写一个生产方法.这种方法将原本的判断逻辑延迟到了客户端实现.

       @interface AKFactory : NSObject
       - (AKBasicControl *)TextField;
       - (AKBasicControl *)Button;
       @end

当我们从后台获取配置的时候,就可以使用工厂方法生成我们所需要的控件.在这个例子中,我们使用的是静态配置来生成一个动态表单.客户端通过调用工厂方法生成相应的控件并展示在动态表单上面.这里我们不需要知道他们具体的类型是什么,只需要按照要求检查数据的合法性与获取数据即可.
详细的代码我已经托管到了github上面:[FactoryMethodExample](https://github.com/johnMaster/FactoryMethodExample).

### 工厂方法模式
当我们写好并投入使用时.果然,PM又跑过来说:"这个表单的控件种类太少了吧.再添加个开关控件,就加一个而已,很简单的".你听了之后内心毫无波动,甚至还想笑.好吧,看个萝莉思考一下.
![the loli](https://cl.ly/0g2J323E070s/download/IMG_0271.JPG)

首先,很容易想到一个解决方案,那就是修改`AKFactory`类的类方法`controlWithType:`, 在这个方法中添加如类似一下的代码:

    if ([type isEqualToString:@"radio"]) {
      return [[AKRadio alloc] init];
    }

这样子确实可以快速解决问题.不过这也太low了,我们学习设计模式除了要解决一些复杂问题之外,还有一个目的就是装逼.这样子写会被高手鄙视的.但是话说回来.这样子写其实是有问题的,因为当一个类完成之后,在不到万不得已的情况下,我们不应该去修改它.这就是开闭原则([Open/closed principle](https://en.wikipedia.org/wiki/Open/closed_principle)).
> Software entities should be open for extension,but closed for modification. --  Object Oriented Software Construction

翻译过来就是：“软件实体应当对扩展开放，对修改关闭."
对运行良好的类,我们不应该去修改它.在改变代码之后,不仅维护变得更复杂了,而且无法知道之前的功能是否会失效.所以干脆禁止修改,以免触发隐藏Bug.所以我们需要将原来的简单工厂模式代码,并使其容易扩展.
由于需要扩展,那我们将不能委托`AKFactory`生成产品,而是通过继承抽象(协议)工厂中的构造方法来为每一个产品类定制单独的构造方法.
可以得到以下UML图:
![Factory method](https://cl.ly/1K0p020O0X0A/download/factory%20method.png)  
继承`AKFactory`并重载`+ makeControl`得到`AKDateFactory`和`AKTextFieldFactory`.在客户端调用其具体工厂类生成对应的对象.例如:在客户端调用`AKTextFieldFactory`类中的`+ makeControl`生成`AKLabel`类.但是,不像简单工厂,判断控件类型的逻辑需要我们在客户端实现.
现在,我们要扩展一个新的控件就简单多了.只不要继承`AKFactory`为`AKSwitchFactory`,并重载`+ makeControl`.然后,只需要在客户端中使用就行了.这样既不修改原来的类,也能扩展新功能.终于可以好好装逼了.
具体代码在[FactoryMethodExample](https://github.com/johnMaster/FactoryMethodExample/tree/factory_method)的[factory method](https://github.com/johnMaster/FactoryMethodExample/tree/factory_method)分支下.
### 总结
当无法确定需要创建的具体的类以及这些类具有相似的方法时,就能使用简单工厂模式或工厂方法模式.工厂方法定义了一个接口,并让子类决定生成哪个子类,将类的实例化延迟到子类中,解决编程中无法确定具体类的问题.
简单工厂与工厂方法之间的区别是将原来的判断逻辑转移到转移到其他文件实现. 而产品类却都是一样.在实际的项目中,假设只需要一些静态的几个产品类,如VIP与非VIP两种情况,使用简单工厂即可.但假如遇到当前这个例子这用需求,就应该使用工厂方法模式.

此外,如果需求更加复杂,可能需要用到抽象工厂,请看我下一篇文章[设计模式:抽象工厂--Objective-C实现](http://www.jianshu.com/p/c05f00cebcb5)

### 引用文章
[Factory method pattern](https://en.wikipedia.org/wiki/Factory_method_pattern)
