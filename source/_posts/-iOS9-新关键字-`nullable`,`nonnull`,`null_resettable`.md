他们有一下的优点:
 1. 用来修饰属性,或者方法的参数,方法的返回值, 迎合swift
 2. 可以增加代码的可读性,减少沟通成本.
 3. 修饰参数可以不用使用断言  

 他们只能修饰对象,不能修饰基本数据类型.  
 **注:这些代码只作用于编码阶段,编译器对代码的限制和对代码的提示.对于编译之后的代码没有任何影响.**


* `nullable` 表示该属性可以为空    

      // 方式一
      @property (nonatomic, strong, nullable) NSString *name;
      // 方式二
      @property (nonatomic, strong) NSString *_Nullable name;
      // 方式三
      @property (nonatomic, strong) NSString *__nullable name;

* `nonnull` 同理

      // 方式一
      @property (nonatomic, strong, nonnull) NSString *name;
      // 方式二
      @property (nonatomic, strong) NSString *_Nonnull name;
      // 方式三
      @property (nonatomic, strong) NSString *__nonnull name;

* `null_resettable` get:不能为空, set可以为空  
*注意: 如果使用`null_resettable`,必须重写get方法或者set方法,处理传值为空的情况*

* `null_unspecified` 不确定是否为空  
*注:这个关键字不会用到*

* `NS_ASSUME_NONNULL_BEGIN`和`NS_ASSUME_NONNULL_END`. 这是一个宏(语法糖),在这两个宏中间,你对应的定义的所有属性,除非你自己声明`nullable`,其他属性默认都有`nonnull`

另外,方法中,关键字书写规范类似之前的属性的定义方式,顺序不能打乱  

    - (nonnull NSString *)method:(nonnull NSString *)str;
    - (NSString * _Nonnull)method2:(NSString * _Nonnull)str;
