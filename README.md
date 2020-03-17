# 梦想人AR SDK



- ## 声明

注：本文档"按原样"提供。本文档中表示的信息和视图（包括 URL 和其他 Internet 网站引用）可能会发生更改，且不进行通知。您需承担使用本文档的风险。

此处所述的某些示例仅为说明起见而提供，纯属虚构。不存在真实关联或连接，请勿妄加推测。

本文档未向您提供对任何梦想人产品的任意知识产权的任何法律权利。您可以复制和使用此文档，供内部参考之用。

©2019  梦想人科技 保留所有权利。




- ## 流程图

![](https://github.com/LiuLongCoder/MXRARSDK/master/images/flowchart.png)

## 环境及配置

1. 开发工具：Xcode11

2. SDK版本：iOS 8.0

3. 支持架构： armv7s arm64 arm64e



## 配置Pod以及权限

1. 在Podfile中添加依赖：

   pod 'MXRSDK', :path => '../PodResource

![](https://github.com/LiuLongCoder/MXRARSDK/master/images/podresource.png)

2. Enable Bitcode设置为NO，在Header Search Path里需要添加MXRSDK.framewrok的头文件路径

   $(SRCROOT)/../MXRSDK/PodResource/MXRSDK.framework/Headers

3. 把PodResource文件夹下的Data以 Reference的方式拖进（添加）到工程项目中（展示模型相关文件）。

4. 需要设置对域名 mxrcorp.cn 的信任

   vide.mxrcorp.cn 、 

   img.mxrcorp.cn 、

   qfile.mxrcorp.cn 、

   booksr.mxrcorp.cn、

   books.mxrcorp.cn、

   page.mxrcorp.cn

5. 增加权限：相机、相册、麦克风、定位

   

## 接口说明 

###  一、打开图书(停用)

​	描述：在APP中新增专区入口，展示图书列表，点击进入阅读图书。

1. 获取图书信息列表

   ```objective-c
   // 请求图书列表block
   typedef void (^MXR4DGetBookArray) (NSError *err, NSArray<MXR4DBook *> *bookArray);
   
   /** 获取图书列表请求的模型
    */
   @interface MXR4DBookRequestM : NSObject
   
   @property (nonatomic, assign) NSInteger page;   // 页码
   @property (nonatomic, assign) NSInteger size;   // 每页图书数量
   @end
   
   //返回图书列表 NSArray<MXR4DBook *> *
   //MXR4DBook信息
   @interface MXR4DBook : NSObject
   @property (nonatomic, strong) NSString * bookGUID; //图书guid
   @property (nonatomic, strong) NSString * bookName; //图书名称
   @property (nonatomic, strong) NSString * bookCoverURL //图书封面
   @property (nonatomic, strong) CGFloat     bookSize; //图书大小
   /*
   '''
   其它信息省略，联系相关人员
   */
   @end
   ```

2. 打开图书

   2.1 调用openBook方法打开相应图书。

   2.2  openBook方法说明：

   ```objective-c
   /** 根据bookGUID打开对应图书页面
       @param bookGUID 图书唯一标识
    */
   + (void)openBook:(NSString *)bookGUID;
   ```

   

### 二、打开模型(停用)

​	场景2描述：在APP原有图书中新增4D模型入口，点击直接打开观看模型。

1. 打开模型

   1.1 open4DModel方法说明：

   ```objective-c
   /** 根据modelId打开对应模型页面
       @param modelId 模型唯一标识
   */
   + (void)openModel:(NSString *)modelId;
   ```

   

### 三、扫描接口返回

​	描述：原扫描能力功过代理返回

```objective-c
/// 扫描到普通二维码回调
/// @param scanVC 扫面页面
/// @param metadataObjects AVCaptureMetadataOutputObjectsDelegate返回的摄像头获取的原数据
/// @param value 解析出普通二维码数据
- (void)scanVC:(MXRSDKScanVC *)scanVC didOutputMetadataObjects:(NSArray *)metadataObjects value:(NSString *)value;

```



/// 扫描到普通二维码回调

/// **@param** scanVC 扫面页面

/// **@param** metadataObjects AVCaptureMetadataOutputObjectsDelegate返回的摄像头获取的原数据

/// **@param** value 解析出普通二维码数据

\- (**void**)scanVC:(MXRSDKScanVC *)scanVC didOutputMetadataObjects:(NSArray *)metadataObjects value:(NSString *)value;

 

### 四、分享接口

​	描述：分享功能

​	

```objective-c
/// 提供分享能力，委托SDK进行分享
/// @param shareEntrance 分享的入口
/// @param sharePlatfrom 分享到的平台
/// @param bookId 分享需要的bookId
/// @param image 分享需要的图片。 从离线阅读入口过来的是nil
@property (nonatomic, copy) void (^shareBlock)(MXRShareEntrance shareEntrance, MXRSharePlatform sharePlatfrom, NSString *bookId, UIImage *image);

```



### 五、APP调用扫一扫页面

​	描述：从APP发起扫一扫 

​	1    点击精选右上方的扫一扫按钮

​	2     点击精选banner & AR专区右上角的扫一扫按钮

​	3     点击精选5G & AR专区右上角的扫一扫按钮

 

### 六、埋点通知

​	描述：SDK通知APP进行埋点

```objective-c
/// 提供数据统计的能力，委托SDK进行统计
@property (nonatomic, copy) void (^dataStatistics)(MXRSDKDataStatisticsType type, NSString *label);
```