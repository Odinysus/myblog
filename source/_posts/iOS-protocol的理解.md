protocol 的本质类似一个抽象类,这个声明了一些纯虚方法.并且没有属性,在java中,这个叫接口类,在c++中,这个叫抽象类. (在ruby中,跟它本质不一样,但是有点的类似,叫鸭子类型) 在编码中,通过继承协议,实现了协议中描述的这一套方法.

协议分为@require @option 两种,@require是必须实现的,例如,我们继承哺乳动物的一套功能. 呼吸,走路,进食,生育.功能中,必须实现呼吸和进食和生育,否则该生物将无法生存. 

>猜测:protocol在底层实现中应该是C++的抽象类,比如有个类Class A 有一个方法  id<UITableViewDataSource> dataSource
只要ClassB 实现了协议(类似继承抽象类)时候就能将instance B 赋值给dataSource了.(因为class B已经继承了UITableViewDataSource这个抽象类了,
Class B作为子类能赋值给基类属性) 

在iOS中,iOS使用委托这个设计方法,两者结合.形成一个直观的行为,Class A委托B实现Class A中的一些方法.这种设计使OC这一点变得非常灵活.但是,必须吐槽,如果只是单纯实现一两个协议,那很完美,在某些复杂的情况下,由于需要实现多个协议,使ViewController变得很臃肿并难以维护.

对于某些对数据依赖比较少的协议,可以从viewcongroller分离开来.
