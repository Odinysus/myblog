* [Blacklight](https://github.com/limejelly/Backlight-for-XCode)
 当前代码高亮显示,可以自己设置高亮颜色
![BlackLight效果图](https://raw.githubusercontent.com/limejelly/Backlight-for-XCode/master/screenshot.png)

* [DBSmartPanels](https://github.com/chaingarden/DBSmartPanels/) 
自能面板显示,能够根据某些情况隐藏面板.在编写代码的时候自动隐藏调试窗口和右边的属性窗口.
![DBSmartPanels效果图](https://raw.githubusercontent.com/chaingarden/DBSmartPanels/master/Screenshots/DemoScreenshot.png)

* [FuzzyAutoComplete](https://github.com/FuzzyAutocomplete/FuzzyAutocompletePlugin) 
模糊代码补全,能够对代码补全进行模糊搜索
注意:xcode 7.2以上已经自带模糊代码补全,且该插件在我的xcode上已失效.
![自动补全效果图](http://upload-images.jianshu.io/upload_images/2631730-f34659ba21d2ad85.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**[VVDocumenter-Xcode](https://github.com/onevcat/VVDocumenter-Xcode)** 
能够像生成javadoc那样的代码注释,使用快捷键 `///` 

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2631730-5eeebd80d5754887.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**[XVim](https://github.com/XVimProject/XVim)** 
装逼神器!xcode上面的vim插件,让你的小伙伴拜倒在你的牛仔裤之下把.
最新版的XVim无法再xcode 8.0以下工作,所以我们需要安装旧版XVim
解决方法可以见github上的[issue 937](https://github.com/XVimProject/XVim/issues/973)

**[KSImageNamed-Xcode](https://github.com/ksuther/KSImageNamed-Xcode)**
 根据图片的名字进行补全,同时能够预览

![图片名字保存](http://upload-images.jianshu.io/upload_images/2631730-178445f81573c5b3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**[Peckham](https://github.com/markohlebar/Peckham)**
 快捷插入头文件,快捷键 `cmd+ctrl+p`

![插图头文件演示](http://upload-images.jianshu.io/upload_images/2631730-9821fcee62baab5e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Alcatraz 

插件管理,打开iterm或终端,输入下面的地址,回车

    curl -fsSL https://raw.githubusercontent.com/supermarin/Alcatraz/deploy/Scripts/install.sh | sh

**卸载**
删除Alcatraz插件

    rm -rf ~/Library/Application\ Support/Developer/Shared/Xcode/Plug-ins/Alcatraz.xcplugin

删除所有的缓存数据

    rm -rf ~/Library/Application\ Support/Alcatraz
