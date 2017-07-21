## 前言
UITableview在iOS中的使用频率是非常高的.通常,我们只需要通过设置代理,并且在代理方法`tableView:cellForRowAtIndexPath:` 调用`dequeueReusableCellWithIdentifier:`获取`cell`并直接使用.但是从没有细致得想过其中的过程与机制,并且知道最近面试(此次面试的问题)的时候,才发现自己表述得不太好.这种感觉就好像你每天都准时吃饭(滑稽),突然有一天有人问你人为什么吃饭的时候自己却没能及时答上来的那种感觉.说明自己学得不够扎实.as we know,
> 如果你不能将知识通过简洁的语言表达出来,那说明你还没掌握这个知识.

借此机会,将这个知识点记录下来

## reuseIdentifier
对于reuseIdentifier,[官方文档](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewCell_Class/index.html#//apple_ref/occ/instp/UITableViewCell/reuseIdentifier)是这样解释的:
> The reuse identifier is associated with a `UITableViewCell` object that the table-view’s delegate creates with the intent to reuse it as the basis (for performance reasons) for multiple rows of a table view. It is assigned to the cell object in `initWithFrame:reuseIdentifier:` and cannot be changed thereafter.
    A `UITableView` object maintains a queue (or list) of the currently reusable cells, each with its own reuse identifier, and makes them available to the delegate in the `dequeueReusableCellWithIdentifier:` method.

冒死翻译一下:
这个复用标识关联`UITableviewCell`对象,在`tableview`代理中创建带有”标识符”来复用`cell`对象,而且作为`tableview`多行显示的原型(性能的原因).通过`initWithFrame:reuseIdentifier:`来指定一个`cell`对象而且在调用这个方法之后就不能修改了.在`UITableView`对象维护一个当前复用的`cell`队列(或列表),并且每一个`cell`都拥有自己的标识符,并这些`cell`能在代理对象的`dequeueReusableCellWithIdentifier:`的方法中获取.

在`tableview`新建的时候,会新建一个复用池(reuse pool).这个复用池可能是一个队列,或者是一个链表,保存着当前的`Cell`.pool中的对象的复用标识符就是`reuseIdentifier`,标识着不同的种类的`cell`.所以调用`dequeueReusableCellWithIdentifier:`方法获取`cell`.从pool中取出来的`cell`都是`tableview`展示的原型.无论之前有什么状态,全部都要设置一遍.

## 过程
在`UITableView`创建同时,会创建一个空的复用池.之后`UITableView`在内部维护这个复用池.一般情况下,有两种用法,一种是在取出一个空的cell的时候再新建一个.一种是预先注册cell.之后再直接从复用池取出来用,不需要初始化.
* `UITableview`第一次执行`tableView:cellForRowAtIndexPath:`.当前复用池为空.
`dequeueReusableCellWithIdentifier`调用中取出`cell`,并检测`cell`是否存在.
目前并不存在任何`cell`,于是新建一个`cell`,执行初始化, 并`return cell`.
代码如下:

      #define kInputCellReuseIdentifierPWD   @"password_cell"
      UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:kInputCellReuseIdentifierPWD];
      if (!cell) {
          cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:kInputCellReuseIdentifierPWD];
          // other initialization i.e. add an UIButton to cell's contentView
      }
      // custom cell. i.e. change cell's title
      cell.textLabel.text = @"It is awesome";
      return cell;
上面的代码中,你返回的`cell`会被`UITableView`添加到复用池中.第二次调用`tableView:cellForRowAtIndexPath:`,当前复用池中有一个`cell`.这时候因为`UITableView`上面还未填满,而且复用池中唯一的那一个已经在使用了.
所以取出来的Cell仍然是`nil`.于是继续新建一个cell并返回,复用池再添加一个`cell`,当前复用池中`cell`的个数为2.
假如当前`tableview`只能容纳5个`cell`.那么在滚动到第6个`cell`时,从`tableview`的复用池取出来的`cell`将会是第0行的那个`cell`.以此类推,当滚动到第7行时,会从复用池取出来第1行的那个`cell`. 另外,此时不再继续往复用池添加新的`cell`.
* 另一个用法:在`UITableView`初始化的时候,注册一个`UITableViewCell`的类.

      [tableView registerClass:[UITableViewCell class]
               forCellWithReuseIdentifier:@"AssetCell"];
使用此方法之后,就不用再判断取出来的`cell`是否为空,因为取出来的cell必定存在.调用`dequeueReusableCellWithIdentifier:`方法时,会先判断当前复用池时候有可用复用`cell`.
如果没有,`tableview`会在内部帮我们新建一个`cell`,其他的跟方法一一样.
这里有个动画可以很清晰地看到滑动时的复用过程.图片来自[Scroll Views Inside Scroll Views](http://oleb.net/blog/2014/05/scrollviews-inside-scrollviews/)  

![复用cell的过程](https://cl.ly/102m0R2f3m3t/table-view-cell-reuse-simulation.gif)

## StoryBoard中的复用机制
与[CSwater](http://www.jianshu.com/users/1c7530ba374e)讨论之后,觉得在StoryBoard中,复用机制如下:在对StoryBoard初始化UITableView时,会再内部调用`registerClass:forCellWithReuseIdentifier:`注册我们在storyboard上设置的cell.剩下的流程跟第二种方法一样.

## 爬过的坑
从复用池中获取cell的方法有两个:`dequeueReusableCellWithIdentifier:forIndexPath:`
和`dequeueReusableCellWithIdentifier:`.还记得我们在第二个方法对使用使用复用池之前的情况吗? 对,就是注册一个cell.注册之后我们调用`dequeueReusableCellWithIdentifier:`获取cell.
对于第二种情况,两种方法都是没问题的.但是,在调用`registerClass:forCellReuseIdentifier:`之前,你必须注册一个cell类.正如[官方文档](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/index.html#//apple_ref/occ/instm/UITableView/dequeueReusableCellWithIdentifier:forIndexPath:)所言:
> IMPORTANT
You must register a class or nib file using the `registerNib:forCellReuseIdentifier:` or `registerClass:forCellReuseIdentifier:` method before calling this method.

## 总结
`UITableViewCell`的复用机制是,在`tableview`中存在一个复用池.这个复用池是一个队列或一个链表.然后通过`dequeueReusableCellWithIdentifier:`获取一个`cell`,如果当前`cell`不存在,即新建一个`cell`,并将当前`cell`添加进复用池中.如果当前的cell数量已经到过`tableview`所能容纳的个数,则会在滚动到下一个`cell`时,自动取出之前的`cell`并设置内容.
## 扩展知识
其实,`UITableViewCell`的复用机制并非什么黑魔法,而是设计模式中的一种创造型设计模式--对象池设计模式
> The object pool pattern is a software creational design pattern that uses a set of initialized objects kept ready to use – a "pool" – rather than allocating and destroying them on demand. A client of the pool will request an object from the pool and perform operations on the returned object. When the client has finished, it returns the object to the pool rather than destroying it; this can be done manually or automatically.  ---- [wikipedia](https://en.wikipedia.org/wiki/Creational_pattern)

大意是,对象池模式是一种创造型设计模式,使用一个已初始化对象的集合,并能随时从”池”中拿出来使用.以此避免对象的创建销毁所带来的开销.一个客户端的池会请求池中的对象,然后在返回的对象上执行操作.当这个客户端结束时,代替原本销毁操作,取而代之的是将对象返回到池中.这个操作可以手动执行,也可以自动执行.有一个明显的有点就是面对数据量很大时,能很好的改善性能.更多的请看维基百科.
## 参考文章
[Object pool pattern](https://en.wikipedia.org/wiki/Object_pool_pattern)
[Scroll Views Inside Scroll Views](http://oleb.net/blog/2014/05/scrollviews-inside-scrollviews/)  
[UITableViewDataSource Protocol Reference](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDataSource_Protocol/index.html#//apple_ref/occ/intf/UITableViewDataSource)
[UITableView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/index.html)
