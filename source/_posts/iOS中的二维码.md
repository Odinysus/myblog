### 前言
在iOS中,经常可以看到,有很多商业都需要APP,例如像BOSS直聘,微信都要用到扫描二维码.而且前几天面试的时候面试官问了这个为.结果自己也不会.虽然能在github下载例子拖进项目.但是
> 如果你不能将知识通过简洁的语言表达出来,那说明你还没掌握这个知识.

### 二维码
二维码是矩阵条形码类型中的一种商标类型.一个条形码能够储存字母,数字,二进制,和汉字四种标准数据类型.扫描设备通过二维码角落的三个正方形定位.在通过正方形里面的小正方形计算图片的大小,方向.中间的小黑点则包括二进制数字,其中有一部分数据用于纠错算法.  
纠错等级越高,储存的容量越少.总共有四个等级.

| 等级           | 容量|
| :------------- | :------------- |
| Level L (Low) | 7% of codewords can be restored   |
| Level M (Medium) | 15% of codewords can be restored      |
| Level Q (Quartile) | 25% of codewords can be restored       |
| Level H (High)|	30% of codewords can be restored      |

### 实现
AVFoundation是唯一一个你可以用来播放或者创造基于时间的影声媒介的框架.而二维码属于这个框架之中的一个小小模块.所以得按照AVFoundation流程创建一个扫描回话.如果仅仅需要简单得扫码功能而不需要详细地控制.可以直接在viewDidLoad中添加以下代码:

    // 创建一个会话
    self.session = [[AVCaptureSession alloc] init];
    // 创建一个默认的后摄像头视频设备
    self.device = [AVCaptureDevice defaultDeviceWithMediaType:AVMediaTypeVideo];
    // 从一个设备中创建一个设备输入源,可以用来获取摄像头捕捉到的数据
    self.input = [AVCaptureDeviceInput deviceInputWithDevice:self.device
                                                  error:nil];
    // 为回话添加一个输入源
    [self.session addInput:self.input];
    // 创建一个能扫描输出源,可以扫描二维码,条形码,等
    AVCaptureMetadataOutput *output = [[AVCaptureMetadataOutput alloc] init];
    [self.session addOutput:output];
    self.output = output;
    // 设置代理,当扫描取得数据时,会再阻塞主线程.
    [output setMetadataObjectsDelegate:self queue:dispatch_get_main_queue()];
    // 设备数据类型,当前只能扫描二维码
    output.metadataObjectTypes = @[AVMetadataObjectTypeQRCode];
    // 创建一个视频预览图层
    AVCaptureVideoPreviewLayer *previewLayer = [[AVCaptureVideoPreviewLayer alloc] initWithSession:self.session];
    previewLayer.frame = self.view.bounds;
    // 缩放方式
    previewLayer.videoGravity = AVLayerVideoGravityResizeAspectFill;
    [self.view.layer addSublayer:previewLayer];
    [self.session startRunning];

然后继承代理AVCaptureMetadataOutputObjectsDelegate  

    - (void)captureOutput:(AVCaptureOutput *)captureOutput didOutputMetadataObjects:(NSArray *)metadataObjects fromConnection:(AVCaptureConnection *)connection
    {
      AVMetadataMachineReadableCodeObject * metadataObject = [metadataObjects objectAtIndex : 0];
      NSLog(@"QR_code is %@",metadataObject.stringValue);
    }

### 细节控制  
上面的代码能够应用那些简单得需求了.当前,还有很多细节并没有控制.  
* **卡顿** 由于`[self.session startRunning]`可能会花费比较长的时间.可能会发费0.5s,会有一个卡顿的现象.session的初始化和配置都放在异步线程中.但是有关`previewLayer`的操作要放在主线程中更新.并在加载时显示`UIActivityIndicatorView`动画.
* **权限** 在`viewDidLoad`和`viewDidAppear`中添加权限的检测和权限的获取.在获取权限和`viewDidDisappear`之前,必须将当前的`session`暂停.
* **扫描区域** 上面的整个代码中,默认扫描整一个`layer`.像微信这些app都只扫描中间的部分区域.`AVCaptureVideoPreviewLayer`中有属性`rectOfInterest`,控制我们所扫描的区域.默认值为(0.0, 0.0, 1.0, 1.0).不过`AVCaptureVideoPreviewLayer`有一个简便的方法计算这个百分比`metadataOutputRectOfInterestForRect:`,通过坐标计算比例,利用这个方法可以快速计算百分比.
* **识别图片中的二维码** 利用`CIDetector`设别图片中的二维码.  

      -(void)imagePickerController:(UIImagePickerController *)picker didFinishPickingMediaWithInfo:(NSDictionary<NSString *,id> *)info
      {
        UIImage *image = [info objectForKey:UIImagePickerControllerOriginalImage];
        if (image) {
          CIDetector *detector = [CIDetector detectorOfType:CIDetectorTypeQRCode context:nil options:@{CIDetectorAccuracy:CIDetectorAccuracyHigh}];

          NSData *data = UIImagePNGRepresentation(image);
          CIImage *img = [CIImage imageWithData:data];
          NSArray *features = [detector featuresInImage:img options:nil];
          if (features.count > 0) {
            for (CIFeature *feature in features) {
            if (![feature  isKindOfClass:[CIQRCodeFeature class]]) continue;
              NSLog(@"msg:%@", [(CIQRCodeFeature *)feature messageString]);
            }
            } else {
              NSLog(@"No feature!!!");
            }
            } else {
              NSLog(@"No image!!!");
            }

            [picker dismissViewControllerAnimated:YES completion:NULL];
          }

我自己模仿微信的扫描界面,自己写了一个二维码界面并实现了以上的控制,直接考进项目就可以使用.
[AKQRCodeScan](https://github.com/johnMaster/AKQRCodeScan)
### 生成二维码
在网上找了一个博客 [使用CIFilter生成二维码并自定义 ](https://blog.yourtion.com/custom-cifilter-qrcode-generator.html)

### 总结

### 引用文章
[二维码](https://en.wikipedia.org/wiki/QR_code)
