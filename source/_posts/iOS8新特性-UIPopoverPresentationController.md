在IOS 8 之后,iPhone终于可以像iPad一样弹出一个小窗口拉.这是个特大喜讯.你可以直接使用系统框架而不需要使用其他第三方框架.可以避免一些不必要的麻烦.

####简单配置 

    //Present the view controller using the popover style.
      myPopoverViewController.modalPresentationStyle = UIModalPresentationPopover;
      [self presentViewController:myPopoverViewController animated: YES completion: nil];
 
    // Get the popover presentation controller and configure it.
      UIPopoverPresentationController *presentationController =
      [myPopoverViewController popoverPresentationController];
      presentationController.permittedArrowDirections = UIPopoverArrowDirectionLeft | UIPopoverArrowDirectionRight;
      presentationController.sourceView = myView;
      presentationController.sourceRect = sourceRect;
以presentViewController的方式集成在模态视图控制器中,并能够对其统一管理.与之前UIPopoverViewController不同的是,UIPopoverPresentationController不需要新建它的实例,它的实例变量值需要在你的view controller设置模态样式:modalPresentationStyle = UIModalPresentationPopover之后,通过viewcontroller的 popoverPresentationController获取即可.然后你只需要对popoverPresentationController的样式属性进行设置即可.
效果图:
![弹出视图控制器演示](https://cl.ly/0k182Z1t170d/IMG_0187.PNG)

###属性
`popverLayoutMargins`  
一个edge值,能推测与屏幕在旋转之后的弹出窗口的相对位置.目前在iOS 9 上测试无效果.

`backgroundColor`
 背景颜色.在没有自定义背景视图的情况下.设置background值只能在popover view 内部改变颜色.

`barButtonItem `
将当前锚点设置为`barButtonItem`所在的位置.在弹出窗口时,系统会自动在`barButtonItem`的位置弹出.如果需要修改弹出视图和位置,可以使用`sourceView`和`sourceRect`来替代这个属性
注:巨坑 `barButtonItem`在设置之后,`sourceView` 和`sourceRect`将会失效

`sourceView`
 箭头所指的对应的视图,`sourceRect`会以这个视图的左上角为原点.使用这个属性和`sourceRect`计算箭头所处的锚点.如果不设置,`sourceRect`就会不起作用 

`sourceRect`
 箭头所指对应的区域.锚点的计算是这样的.首先根据`sourceView`.在`sourceView`描绘出一块区域(`CGRect`),然后箭头指向这块区域的中心点.

`delegate` 代理
可以控制窗口谈出和消失的行为
*注意: 在默认的情况下,`UIPopoverPresentationController`会根据是否是iphone和ipad来选择弹出的样式,如果当前的设备是iphone,那么系统会选择modal样式,并弹出到全屏.如果我们需要改变这个默认的行为,则需要实现代理,在代理`- adaptivePresentationStyleForPresentationController:`这个方法中返回一个`UIModalPresentationNone`样式*
    
    -(UIModalPresentationStyle)adaptivePresentationStyleForPresentationController:(UIPresentationController *)controller
    {
        return UIModalPresentationNone;
    }

`permittedArrowDirections`
允许弹出窗口时的箭头方向,设置`barButtonItem`这个属性无效

`arrowDirection readonly`
当前的箭头方向在弹出之前.消失之后,这个值为 `UIPopoverArrowDirectionUnknown`

`popoverBackgroundViewClass`
 自定义的的背景类.用于替换弹出视图之后的背景.这个类必须继承`UIPopoverBackgroundView`,且必须实现`UIPopoverBackgroundViewMethods`接口的方法.

`UIPopoverBackgroundView`
这个类必须继承和子类化才能使用.你必须实现箭头和调整border的风格.popover controller 将会放置在这个自定义的backgroung的左上方.
这个背景内容应该基于一个可伸缩的图片. [Apple官方文档](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIPopoverBackgroundView_class/index.html#//apple_ref/occ/cl/UIPopoverBackgroundView)解释
>Because the popover is animated into place (and may require animated transitions), using images is the only way to ensure that the animations are smooth and not jittery. 

大致意思为:因为弹窗执行动画效果弹到特定的地方,要确保动画平滑且没卡顿,就只有一个方法,那就是使用可伸缩的图.

需要重载的属性和方法,同时实现setter 和getter方法
####property  
`arrowOffset `
`arrowDirection`

###method 
`+ (CGFloat)arrowBase`
`+ (UIEdgeInsets)contentViewInsets`
`+ (CGFloat)arrowHeight`

然后你需要自定背景图,先看看上面的属性和方法代表的意义.

![各个属性的意义](http://upload-images.jianshu.io/upload_images/2631730-d72681d9054c38cf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

详细的自定义背景,国外有一片文章写得不错.
[Customizing UIPopover with UIPopoverBackgroundView](http://www.scianski.com/customizing-uipopover-with-uipopoverbackgroundview/)

***
引用:
https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIPopoverBackgroundView_class/index.html#//apple_ref/occ/cl/UIPopoverBackgroundView

http://www.scianski.com/customizing-uipopover-with-uipopoverbackgroundview/
