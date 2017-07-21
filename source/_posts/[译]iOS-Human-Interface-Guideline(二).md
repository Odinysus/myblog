> 翻译官方文档: [iOS Human Interface Guidelines-3D Touch]( https://developer.apple.com/ios/human-interface-guidelines/interaction/3d-touch/)

## 3D Touch
3D Touch添加一个额外的维度来基于触摸的交互,在已支持的设备上,人们可以通过对触摸屏施加不同的压力来使用不同的功能.APP可以通过展示一个菜单,显示额外的内容,执行一个动画.用户不需要学习新的手势就能使用3D Touch进行交互.当他们轻轻按压屏幕和得到响应是,能快速发现额外的交互维度.  

### Home屏幕交互
在Home屏幕,按压支持3D Touch的APP,展示一个动作视图.这个视图让你快速地执行指定APP中的常用任务和看见一些有趣的信息.日历能提供一个简短的事件.它还显示你的时间表上的下一次事件.想查看设计指导,查看[Home屏幕动作](https://developer.apple.com/ios/human-interface-guidelines/extensions/home-screen-actions/)和[小部件](https://developer.apple.com/ios/human-interface-guidelines/extensions/widgets/)

### Peek和Pop(看一眼和弹出)
peek功能让用户使用3D Touch来预览一些内容,如页面,链接,文件,一个临时出现在当前上下文的视图.对于支持这个功能的内容,为了看一眼(peek),用手指对其施加一点压力.简单地从挪开你的手指退出peek状态.为了查看更详细的信息,需要按压比之前重一点,直到弹出并充满真个屏幕.在一些peek视图中,你可以向上划来显示相关的动作按钮.在Safari看一眼(peek)链接时,你可以向上划显示打开链接并在后台运行,添加链接到阅读列表,复制链接的按钮.  

**使用peek提供鲜明的,内容丰富的预览** 在理想的情况下,peek提供足够APP相关信息来增强当前任务,或帮助用户决定是否全面参与项目.(打磨)例如,在决定将一个链接分享给用朋友或在Safari打开之前，先在Mail信息中的预览。Peek功能常用于在预览TableViewCell跳转到view的信息。  

**设计一个足够大的peek视图**  设计一个足够大的peek视图,让手指不会遮挡到它的内容。让用户清楚他们按压更深一点并完全弹出的内容是什么。  

**采用一致的Peek和Pop** 如果你在一些地方支持Peek和Pop,而其他地方没有.用户将不清楚可以在些地方哪使用这些特性。甚至这会怀疑APP出错或他们的设备出错。  

**让每个peek都可以弹出** 尽管peek已经向用户提供足够的信息。但如果用户决定离开当前任务并专注于peek具体内容时，让这些内容弹射出来。  

**避免在peek视图中展示按钮状的元素** 如果挪开手指去点击类似按钮的元素，peek就消失。  

**同一内容不要同时出现peek和编辑菜单** 当这两种特性同时生效时，可能让用户觉得困惑，同时系统也难分辨是哪个特性。需要额外的指导，查看[编辑菜单](https://developer.apple.com/ios/human-interface-guidelines/ui-controls/edit-menus/).  

**在适当的时候提供动作按钮** 不是所有的peek都需要按钮，但是一个提供常用任务快捷方式的好方法。如果你的APP已经提供自定义的长按动作，在peek时包含相同的动作是佷好的实践。  

**避免提供打开peek详情内容的动作按钮** 用户经常通过用力按压打开peek的详情内容.所以,不需要特别地提供一个明显的打开按钮.  

**不要通过peek这个方法来这行这些动作** 不是每个设备都支持peek和pop,有一些用户可能会关闭3D Touch.你的app应该在不支持的设备上提供其他方式来触发这些动作.例如,你的app可以在用户进行长按时出现的视图中,镜像peek中的快捷动作.  

###Live Photos  
APP可以通过支持Live Photos,把压力感应合并到照片视图体验.在你按压Live Photos时,Live Photos通过使用移动和声音来显示拍照前后片刻的内容,能表现得很生动.其设计指导,查看[Live Photos](https://developer.apple.com/ios/human-interface-guidelines/technologies/live-photos/)
