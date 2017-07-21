
> 本文翻译: [Core Foundation design concept](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFDesignConcepts/CFDesignConcepts.html#//apple_ref/doc/uid/10000122-SW1)

### 简介
Core Foundation 是一组慨念源自OC基础框架的编程接口,并使用c语言实现的库.为了实现这个库,Core Foundation用C语言实现一个限制对象模型.Core Foundation定义不透明类型来封装数据和函数,之后就称他们为"对象".  
Core Foundation对象设计的编程接口具有易用性和可复用性.在普通层次下,Core Foundation有以下特性:  

* 能够在繁杂多样的框架和库中共享数据和代码  
* 使得在一定程度上独立于操作系统成为可能
* 支持使用unicode字符串的国际化
* 提供一些API和其他有用的功能,包括一个插件架构,XML属性列表等

Core Foundation使OS X上不同的框架和库共享代码与数据成为可能.应用,库,框架让你在平常C编程中，定义混合Core Foundation类型的接口；因此,它们可以计算数据--就像Core Foundation对象--通过这些接口设计算各自的数据.  
Core Foundation在已确定的服务和Cocoa的Foundation框架之间提供"toll-free bridging"(无代价桥接). toll-free bridging能将函数参数中的Core Foundation对象替换成Cocoa对象或者反过来.    
一些Core Foundation类型和函数是在不同操作系统上特定代码实现的抽象. 使用这些API能更容易适配不同平台.  
date与number类型抽象了时间工具,并提供绝对时间和公历时间的之间的转换工具.它还抽象了数字值,并为这些内部表示不同的值提供的互转工具.  
Core Foundation给应用开发带来其中一个主要的好处是支持国际化.通过框架中的字符串对象,Core Foundation使国际化在OS X与Cocoa上接口的编写与实现变得更简单,健壮和一致.这个特性的本质部分是一种类型--CFString, 代表一个16bit的Unicode字符数组. CFString对象能灵活地支持百万字节的字符, 且使用一些足够简单的低层的接口就能计算字符数据.它达到了跟与之关联的标准C字符串差不多的性能.  
你应该阅读这个文档来了解Core Foundation之下的基本设计原则和Core Foundation对象跟Cocoa(Touch)对象之间的交互.

### 不透明类型
Core Foundation对象模型基于不透明类型,且支持封装和多态函数.  
基于不透明类型的对象的各个字段隐藏于客户端,但是这些类型的函数能提供大多数字段值.Figure 1 描绘了不透明类型所隐藏的数据且仅展示其接口给客户端.   

> Note: "Class"不是用来指向不透明类型的,尽管类和不透明类型概念上相似,可很多人可能被这个词混淆.然而,Core Foundation文档所指的类型是特定的,这些类型的数据承载实例就称之为"对象"

Core Foundation有很多不透明类型,而且它们的名字说明了它们的预期用途.例如,`CFString` 是一个用来"展示"和操作Unicode字符数组的不透明类型(CF是Code Foundation的前缀). `CFArray`是基于下标的泛型集合.一个不透明类型支持的函数,常量和其他次要的数据类型通常定义在其类型对应名字的头文件中.例如`CFArray.h`,`CFArray`包含定义符号.  
![Figure 1  An opaque type](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFDesignConcepts/Art/opaquetypes_2x.png)

#### 不透明类型的优点
部分情况下,一个不透明类型似乎要通过令人沮丧的结构内容直接存取来增加一些没有必要的限制. 此外，似乎不透明的类型可能会影响项目性能相关的开销.但是不透明类型的好处要大于这些表面的局限性.  
不透明类型为底层功能的实现提供更好的灵活性和抽象性.通过隐藏结构字段的详细信息,Core Foundation减少发在客户端(Xcode)编码时,字段详情被更改后代码出错的几率.此外,如果暴露不透明类型并允许优化,可能会造成困惑(译注: 其他人可能看不懂其优化代码,还不如封装起来).例如`CFString` "正式"代表`UniChar`的16bit字符数组.然而, `CFString`可能选择储存一个ASCII范围内8bit数值的字符串.复制一个不可变的对象可能(经常发生)导致共同引用一个对象而不是内存中两个独立的对象.(查看[Core Foundation内存管理编程指南](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/CFMemoryMgmt.html#//apple_ref/doc/uid/10000127i)).  
继续`CFString`这个例子,使用一个不透明类型来储存字符似乎太笨重.事实证明,并不是这样的,CPU消耗的资源比使用简单的C字符数组高不了多少,况且经常比他们低.另外,不透明并不一定说明这一个不透明类型从不提供直接访问内容的机制.`CFString`,对于它的实例,提供了`CFStringGetCStringPtr`函数来直接访问.  
最后,你可以在一定的程度上自定义一些不透明类型.例如,集合类型允许为集合的每一个成员上为唤醒函数定义一个回调.集合类型允许调用函数时定义一个回调(译注:callback,可以理解为函数指针)，并让每个集合成员调用.

### 对象引用
你可以通过引用来指向一个Core Foundation对象(不透明类型).对于头文件的中的每一个不透明类型,你将会注意到一行或两行相似的,类似下面的语句:  

    typedef const struct __CFArray * CFArrayRef;
    typedef struct __CFArray * CFMutableArrayRef;

声明这些指针指向(私有的)不可变和可变版本的结构.Core Foundation中许多函数的参数和返回值采用对象引用类型,而且从不使用`typedef`私有结构的类型. 例如:  

    CFStringRef CFStringCreateByCombiningStrings(CFAllocatorRef alloc, CFArrayRef array, CFStringRef separatorString);

查看 [对象的种类](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFDesignConcepts/Articles/VarietyOfObjects.html#//apple_ref/doc/uid/20001109-CJBEJBHH)获取更多信息,可变的的还有其他种类的不透明对象.  
每一个Core Foundation不透明类型给他们的对象定义一个唯一类型ID,如在`CFArray`对象上的`CFArrayRef`.类型ID是类型为`CFTypeID`的整型,这个ID标识了一个Core Foundation所属的不透明类型.在各种上下文中使用类型ID, 如在你操作各式各样的容器时使用.Core Foundation提供一些编程性接口,用于获取和计算类型ID的值.  

> 重要:由于类型ID的值可以随着版本的变化而变化,你的类型ID应该不能依赖已保存的或写死的类型ID,也不应该被任何能观察到的属性赋值(例如赋给它一个简单的整形:23333).  

另外,Core Foundation定义了一个通用的对象引用(object-reference)类型, `CFTypeRef`,类似其他面向对象语言的基(root)类.这个通用的引用作为一个多态函数的参数与返回值的占位符类型,可以指向任何Core Foundation对象.查看[多态函数](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFDesignConcepts/Articles/PolymorphicFunctions.html#//apple_ref/doc/uid/20001108-CJBEJBHH)获取更多关于这个课题的信息.查看[Core Foundation的内存管理指南](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/CFMemoryMgmt.html#//apple_ref/doc/uid/10000127i)讨论使用对象引用时内存管理的相关问题.

### 多态函数
Core Foundation提供一些多态函数,这些函数可以将任意Core Foundation对象作为参数和(有一个例子:`CFRetain`)可以返回任意Core Foundation对象.这些参数和返回值的类型是`CFTypeRef`,是一个通用的对象引用(object-reference)类型.`CFType`是类似于其他面向对象语言中的根类,因为它的函数可以被其他所有对象复用.  
你可以使用多态函数对所有的Core Foundation对象进行一些通用操作:  

* 引用计数  
  `CFType`提供几个多态函数来计算和获取对象的引用计数.查看[Core Foundation的内存管理指南](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/CFMemoryMgmt.html#//apple_ref/doc/uid/10000127i)获取更多的关于这些函数的信息.  
* 比较对象
  `CFEqual`函数能比较任何两个Core Foundation对象(查看[比较对象](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFDesignConcepts/Articles/Comparing.html#//apple_ref/doc/uid/20001112-CJBEHAAG)).等式成立的基本条件取决于同类型对象的比较.例如,如果比较两个`CFString`对象,该测试涉及逐个字符的比较.  
* 哈希对象
  `CFHash`函数返回一个唯一的能够标识Core Foundation对象的哈希码(查看[比较对象](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFDesignConcepts/Articles/Comparing.html#//apple_ref/doc/uid/20001112-CJBEHAAG)).你可以把哈希码当哈希表数据结构中的表地址.如果两个对象相等(由`CFEqual`函数决定),他们必须有相同的哈希值.  
* 核查对象
  `CFType`提供核查对象的函数,从而了解它们的内容以及他们"所属"的类型.`CFCopyDescription`函数返回一个描述该对象的字符串(更切确地说,是一个`CFString`对象的引用).`CFCopyTypeIDDescription`函数需要提供一个`CFTypeID`参数而不是`CFTypeRef`.这个函数返回一个描述标识一个不透明类型的类型ID的字符串引用.这些函数主要用来协助调试.查看[核查对象](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFDesignConcepts/Articles/Inspecting.html#//apple_ref/doc/uid/20001113-CJBEHAAG)获取更多相关的函数.  
  你还可以通过`CFGetTypeID`函数获得不透明对象的类型ID,通过这个ID决定一个通用类型对象所属的不透明类型,再将已知类型ID对应的值相比较.查看[检查对象](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFDesignConcepts/Articles/Inspecting.html#//apple_ref/doc/uid/20001113-CJBEHAAG)获取更多关于这个任务的信息.

### 对象的种类
不透明类型拥有多达3种基本种类,或者"口味".(译注:这里为了跟之前的对象类型(object type)区分,故意使用种类或'口味').基于可扩展性和可编辑性的特点分为以下:  

* 不可变和固定大小  
* 可变和固定大小  
* 可变和不固定大小  

可变对象是可编辑的,意味着他们的内容可以修改.不可变对象不能编辑的;一旦创建之后就无法再改变.对不可变对象的任何修改都会导致一些(译注:编译器)错误.一个固定大小的可变对象有一个最大值来限制其大小的增长;就`CFString`来说,一个`CFString`可能是字符串中的字符个数,对于一个容器来说,其限制可能是元素的数量.  
一些不透明类型,例如`CFString`和`CFArray`,可以创建所有三种`口味`的对象.大部分不透明类型可以创建不可变的,固定大小的对象,至少有一个无限制的创建函数来创建无限制对象(如`CFArrayCreate`).决定可变对象的可变大小或不可变大小的是`CreateMutable`函数的容量(`capacity`)参数和长度最大值(`maximum-length `)参数;任何正整数都能生成对固定大小的对象,但是参数为0则生成一个可变大小对象.  
使用带有"Mutable"名字的类型指向可变对象,例如,`CFMutableStringRef`.

### 命名约定
Core Foundation中主要编程接口的约定是使用不透明类型的名字中最密切相关的符号作为符号的前缀.对于函数,这个前缀不仅标识这该函数"属于"那个类型,而且经常用来标识函数动作(action)的目标(target)对象的类型(type).(有一个例外是那些以`k`作为前缀的常量).在头文件中有一些例子:  

    /* from CFDictionary.h */
    CF_EXPORT CFIndex CFDictionaryGetCountOfKey(CFDictionaryRef dict, const void *key);
    /* from CFString.h */
    typedef UInt32 CFStringEncoding;
    /* from CFCharacterSet.h */
    typedef enum {
      kCFCharacterSetControl = 1,
      kCFCharacterSetWhitespace,
      kCFCharacterSetWhitespaceAndNewline,
      kCFCharacterSetDecimalDigit,
      kCFCharacterSetLetter,
      kCFCharacterSetLowercaseLetter,
      kCFCharacterSetUppercaseLetter,
      kCFCharacterSetNonBase,
      kCFCharacterSetDecomposable,
      kCFCharacterSetAlphaNumeric,
      kCFCharacterSetPunctuation,
      kCFCharacterSetIllegal
      } CFCharacterSetPredefinedSet;

除上述之外,Core Foundation中有一小部分编程接口约定是有关于不透明类型和内存管理的.  

* Get,Copy和Create之间有着很重大的区别,在于函数的返回值的命名.如果你使用Get函数,只是无法确定对象的生命周期.为了确保对象能够持续存在,你可以持有(retain)它(使用`CFRetain`函数),或者在一些情况下,复制(copy)一份.如果你使用Copy或Create函数,你必须负责释放对象(使用`CFRelease`函数).更多详情请查看[Core Foundation内存管理指南](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/CFMemoryMgmt.html#//apple_ref/doc/uid/10000127i)  
* 部分Core Foundation对象有它们自己的命名约定,并以此加强通用操作的一致性.例如,集合(collection)将下列动词嵌入函数名,已表示集合中的元素的特定操作.

  * `Add`意味'如果存在就添加,如果存在就不做任何事'(针对某一个集合实例)
  * `Replace`意味'如果存在就替换,如果不存在就不做任何事'
  * `Set`意味'如果不存在就添加,如果存在就替换'
  * `Remove`意味'如果存在就删除,如果不存在就不做任何事'

* `CFIndex`类型用于下标(index),计数(count),长度(length),和大小(size),并作为函数的参数或者返回值.这个类型(整形)的值(当前是32位),可以随着处理器地址大小的增长而增长.在指针大小不同的架构上,如果是64位处理器,CFIndex可能是64位,这取决于`int`的大小.在Core Foundation参数中使用相同类型`CFIndex`的变量进行交互,可以确保您的代码有更高的源码兼容性.  
* 一些Core Foundation头文件似乎定义不透明类型,但实际上却包含便利函数而不是关联一些特定的类型.恰当的例子是`CFPropertyList.h`.`CFPropertyList`是下列属性列表类型中任意一个类型的占位符类型:`CFString`,`CFDate`,`CFBoolean`,`CFNumber`,`CFDate`,`CFArray`和`CFDictionary`.
* 除非特殊说明,否则所有传递引用参数并打算返回值都可以接受`NULL`.这表明调用者不需要关心函数的返回值.

### 其他类型
Core Foundation定义若干个数据类型,并在函数中使用.这些类型存在的目的是抽象原始值.这个原始值可能随处理器地址空间改变而改变.例如`CFIndex`类型,应用于下标(index), 计数(count),长度(length),和大小(size)参数.`CFOptionFlags`类则用于位字段参数,`CFHashCode`类型包含从`CFHash`函数和已确定的哈希回调中返回的哈希结果.  
其他基本类型用于传入函数和函数返回的比较值与范围值.`CFRange`是一个指定线性队列的任意一部分的结构,从字符串中的数组到集合中的元素.对于比较函数,`CFComparisonResult`类型定义枚举常量来代表适当的返回值(等于,小于,大于).部分Core Foundation函数传入一个回调到比较函数中去;如果你需要自定义比较操作,函数必须符合`CFComparatorFunction`中指定的类型(译注:函数的参数必须与(const void *val1, const void *val2, void *context)相同).  

> 重要: 只要其值是整形值，那么其类型一定是Core Foundation类型,特别是`CFIndex`和`CFTypeID`,可以随着处理器寻址大小的增长而增长.在Core Foundation参数中使用相同类型`CFIndex`的变量进行交互,可以让确保您的代码有更高的源码兼容性.

Core Foundation中体统的其他不透明类型在其他主题中讨论.

### 比较对象
你可用`CFEqual`函数来比较两个Core Foundation对象.如果两个对象在本质上是相等的,函数将返回一个`boolean`类型的`true`值."本质上"相等取决于相同类型的对象的比较.当你比较两个`CFString`对象,不管他们的编码和是否可变,当他们逐个字符完全匹配时, Core Foundation则认为他们本质上相等.两个`CFArray`对象比较,当它们有相同数量的元素和数组中每一个元素对象与对应数组中的元素本质上相等,这时会认为他们本质上相等.很明显,比较对象必须是相同类型(不管可变或不可变)才会认为是相等的.  
下面代码片段给你展示了可能使用`CFEqual`函数比较一个常量与传入参数是否相等.  
**Listing** 1 比较Core Foundation对象

    void stringTest(CFStringRef myString) {
      Boolean equal = CFEqual(myString, CFSTR(“Kalamazoo”));
      if (!equal) {
        printf(“They’re not equal!");
      }
      else {
        printf(“They’re equal!”):
      }
    }

### 核查对象
Core Foundation对象的主要特征是它们都基于一个不透明(或私有)类型;可能因此难以直接核查对象的内部数据.然而,基础服务提供了两个函数来检查Core Foundation对象.这些函数返回对象及对象类型的描述信息.  
为了找出Core Foundation对象的内容,调用`CFCopyDescription`函数并传进其对象.然后打印所引用的字符串对象所"包含"的字符序列.

**Listing** 1 使用`CFCopyDescription`

    void describe255(CFTypeRef tested) {
      char buffer[256];
      CFIndex got;
      CFStringRef description = CFCopyDescription(tested);
      CFStringGetBytes(description,
        CFRangeMake(0, CFStringGetLength(description)),
        CFStringGetSystemEncoding(), '?', TRUE, buffer, 255, &got);
        buffer[got] = (char)0;
        fprintf(stdout, "%s", buffer);
        CFRelease(description);
      }

这个例子显示了一种打印描述信息(description)的途径.你不应该使用`CFStringGetBytes`,而是使用`CFString`函数来获取真正的字符串.  
为了确定一个"未知"对象的类型,用`CFGetTypeID`函数取得它的类型ID,将值与已知的类型ID进行比较,直到匹配.可以使用`CFGetTypeID`获取一个对象的类型ID.每一个不透明类型还定义了一个`CFTypeGetTypeID`表格的函数(例如`CFArrayGetTypeID`);这个函数返回给定类型的类型ID.在此之前,你可以测试一下一个`CFType`对象是否是指定不透明类型的成员.  

    CFTypeID type = CFGetTypeID(anObject);
    if (CFArrayGetTypeID() == type)
      printf(“anObject is an array.”);
    else
      printf(“anObject is NOT an array.”);

为了打印Core Foundation对象的类型在调试状态下的相关信息.使用`CFGetTypeID`函数来获取这个类型ID,然后通过这个值传入`CFCopyTypeIDDescription`函数:  

    /* aCFObject is any Core Foundation object */
    CFStringRef descrip = CFCopyTypeIDDescription(CFGetTypeID(aCFObject));


> 注意: 字符串基础功能中包含两个函数:`CFShow`和`CFShowStr`,都声明在`CFString.h`头文件中.在调试模式下,你可以调用它们打印Core Foundation的描述.  

> 重要: `CFCopyDescription`和`CFCopyTypeIDDescription`只在调试模式下使用.不要创建任何依赖它们的代码,因为描述信息与他们的格式随时改变.

### 无代价桥接类型
在Core Foundation框架与Foundation框架中有若干可相互转换的数据类型.可相互装换的数据类型也被称为无代价桥接(toll-free bridged)数据类型.这意味着你可以使用相同的数据结构作为参数传进Core Foundation调用的函数和作为一个Objective-C消息发送的接收器.例如,`NSLocale`(查看[NSLocale Class Reference](https://developer.apple.com/reference/foundation/nslocale)), 与之对应的Core Foundation的`CFLocal`(查看[CFLocale](https://developer.apple.com/reference/corefoundation/1666963-cflocale))是可相互转换的.  
不是所有的数据类型都是无代价桥接的,甚至通过他们的名字可能猜得出他们是有代价桥接的.例如`NSRunLoop`转换到`CFRunLoop`不是无代价桥接,`NSBundle`转换至`CFBundle`不是无代价桥接.`NSDateFormate`转换到`CFDateFormatter`也不是.table 1提供了一张提供的无条件桥接的表.

> 注意: 在你使用corefoundation的集合时,如果安装(install)一个自定义回调(call back),包括`NULL`回调.从Objective-C访问时,该自定义回调的内存管理的行为是没有定义的.

#### 强制类型转换和对象生命期的语义(Casting and Object Lifetime Semantics)  
通过无条件桥接,当你在一个方法中,看到有些参数,例如`NSLocale *`参数,可以传进一个`CFLocaleRef`;在函数看到`CFLocaleRef`参数,你可以传一个`NSLocale`实例.不过你必须为编译器提供其他信息: 首先, 将一个类型强制转换(cast)为其他类型;另外,你可能还要指示出对象的声明期语义.  
编译器理解Objective-C方法返回的Core Foundation类型和遵循过去Cocoa命名约定(查看[高级内存管理编程指南](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html#//apple_ref/doc/uid/10000011i)).例如,编译器知道在iOS中,通过`UIColor`本身并没有`CGColor`方法来返回一个`CGColor`. 你必须使用适当的强制类型转换(cast),就像下面所展示的:  

    NSMutableArray *colors = [NSMutableArray arrayWithObject:(id)[[UIColor darkGrayColor] CGColor]];
    [colors addObject:(id)[[UIColor lightGrayColor] CGColor]];

编译器不会自动管理Core Foundation对象的生命期.选择强制类型转换(cast)(在`objc/runtime.h`中定义)和Core Foundation风格的宏(在`NSObject.h`中定义)其中一个,告诉编译器关于对象的持有者.  

* `__bridge`将一个指向Objective-C与Core Foundation两者之一的指针相互转换,没有转换持有权.
* `__bridge_retained`或`CFBridgingRetain`强制类型转换(cast)指向Objective-C的指针为Core Foundation指针并把持有权转给你.  
  你负责调用`CFRelease`或相关函数来放弃对象的持有权.  
* `__bridge_transfer`或`CFBridgingRelease`移动一个非Objective-C指针到Objective-C,并且将对象持有权交给ARC.  
  ARC 有负责放弃对象的持有权.  
上述看起来就像下面这个例子:  

      NSLocale *gbNSLocale = [[NSLocale alloc] initWithLocaleIdentifier:@"en_GB"];
      CFLocaleRef gbCFLocale = (__bridge CFLocaleRef)gbNSLocale;
      CFStringRef cfIdentifier = CFLocaleGetIdentifier(gbCFLocale);
      NSLog(@"cfIdentifier: %@", (__bridge NSString *)cfIdentifier);
      // Logs: "cfIdentifier: en_GB"

      CFLocaleRef myCFLocale = CFLocaleCopyCurrent();
      NSLocale *myNSLocale = (NSLocale *)CFBridgingRelease(myCFLocale);
      NSString *nsIdentifier = [myNSLocale localeIdentifier];
      CFShow((CFStringRef)[@"nsIdentifier: " stringByAppendingString:nsIdentifier]);

下一个例子展示Core Foundation内存管理函数的用法.这些函数由Core Foundation内存管理规则主宰:  

    - (void)drawRect:(CGRect)rect {

      CGContextRef ctx = UIGraphicsGetCurrentContext();
      CGColorSpaceRef colorSpace = CGColorSpaceCreateDeviceGray();
      CGFloat locations[2] = {0.0, 1.0};
      NSMutableArray *colors = [NSMutableArray arrayWithObject:(id)[[UIColor darkGrayColor] CGColor]];
      [colors addObject:(id)[[UIColor lightGrayColor] CGColor]];
      CGGradientRef gradient = CGGradientCreateWithColors(colorSpace, (__bridge CFArrayRef)colors, locations);
      CGColorSpaceRelease(colorSpace);  // Release owned Core Foundation object.

      CGPoint startPoint = CGPointMake(0.0, 0.0);
      CGPoint endPoint = CGPointMake(CGRectGetMaxX(self.bounds), CGRectGetMaxY(self.bounds));
      CGContextDrawLinearGradient(ctx, gradient, startPoint, endPoint,
                                  kCGGradientDrawsBeforeStartLocation | kCGGradientDrawsAfterEndLocation);
                                  CGGradientRelease(gradient);  // Release owned Core Foundation object.
    }

#### 无代价桥接类型
**table** 1 提供一个可在Core Foundation和Foundation之间相互装换的数据类型的列表.对于每一对,表中来列举了它们之间无代价桥接变得有效时OS X的版本.  
**Table** 1 在Core Foundation和Foundation之间相互装换的数据类型  

| Core Foundation type | Foundation Class | Availability |
| :------------- | :------------ | :----------|
| CFArrayRef       | NSArray     |  OS X v10.0 |
| CFAttributedStringRef       | NSAttributedString     |  OS X v10.4 |
| CFCalendarRef       | NSCalendar     |  OS X v10.4 |
| CFCharacterSetRef       | NSCharacterSet     |  OS X v10.0 |
| CFDataRef       | NSData     |  OS X v10.0 |
| CFDateRef       | NSDate     |  OS X v10.0 |
| CFDictionaryRef       | NSDictionary     |  OS X v10.5 |
| CFErrorRef       | NSError     |  OS X v10.5 |
| NSLocale       | NSLocale     |  OS X v10.4 |
| CFMutableArrayRef       | NSMutableArray     |  OS X v10.0 |
| CFMutableAttributedStringRef       | NSMutableAttributedString     |  OS X v10.4 |
| CFMutableCharacterSetRef       | NSMutableCharacterSet     |  OS X v10.0 |
| CFMutableDataRef       | NSMutableData     |  OS X v10.0 |
| CFMutableDictionaryRef       | NSMutableDictionary     |  OS X v10.0 |
| CFMutableSetRef       | NSMutableSet     |  OS X v10.0 |
| CFMutableStringRef       | NSMutableString     |  OS X v10.0 |
| CFNumberRef       | NSNumber     |  OS X v10.0 |
| CFReadStreamRef       | NSInputStream     |  OS X v10.0 |
| CFRunLoopTimerRef       | NSTimer     |  OS X v10.0 |
| CFSetRef       | NSSet     |  OS X v10.0 |
| CFStringRef       | NSString     |  OS X v10.0 |
| CFTimeZoneRef       | NSTimeZone     |  OS X v10.0 |
| CFURLRef       | NSURL     |  OS X v10.0 |
| CFWriteStreamRef       | NSOutputStream     |  OS X v10.0 |
