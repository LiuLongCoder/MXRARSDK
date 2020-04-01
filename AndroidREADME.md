# 梦想人AR SDK

[TOC]
## 修改记录
| 修改内容                                       | 版本  |  修改时间  | 修改人 |
| ---------------------------------------------- | :---: | :--------: | :----: |
| 初始化                                         |  0.1  | 2019/6/26  | 马优峰 |
| 接口修改                                       |  0.3  |  2019/7/4  |  王峥  |
| 法务声明                                       |  1.0  |  2019/7/5  | 王思维 |
| 接口修改                                       |  1.1  | 2019/10/14 |  王峥  |
| 新增接口                                       |  1.2  | 2020/2/21  |  王峥  |
| 新增接口                                       |  1.3  | 2020/2/29  |  王峥  |
| 文档修改                                       | 1.3.1 |  2020/3/5  |  王峥  |
| 登录接口修改userID为String                     | 1.3.2 | 2020/3/10  |  王峥  |
| 正在下载图书数量添加change                     | 1.3.3 | 2020/3/10  |  王峥  |
| 更新埋点字段                                   | 1.3.4 | 2020/3/13  |  王峥  |
| 新增关闭扫描接口                               | 1.3.5 | 2020/3/16  |  王峥  |
| 新增本地图书总数，开始下载，打开5G扫一扫等接口 | 1.3.6 | 2020/3/20  |  王峥  |
| 新增init方法                                   | 1.4.0 | 2020/3/27  |  王峥  |
| 新增书架图书总数获取方法                       | 1.4.1 | 2020/3/28  |  王峥  |
| 修改登录接口                                   | 1.4.2 | 2020/3/30  |  王峥  |
| 修改电子书打开接口                             | 1.4.3 |  2020/4/1  |  王峥  |

## 声明

注：本文档"按原样"提供。本文档中表示的信息和视图（包括 URL 和其他 Internet 网站引用）可能会发生更改，且不进行通知。您需承担使用本文档的风险。

此处所述的某些示例仅为说明起见而提供，纯属虚构。不存在真实关联或连接，请勿妄加推测。

本文档未向您提供对任何梦想人产品的任意知识产权的任何法律权利。您可以复制和使用此文档，供内部参考之用。

©2019  梦想人科技 保留所有权利。




## 流程图

![flowchart](/images/oldflowchart.png)

![image](https://raw.githubusercontent.com/LiuLongCoder/MXRARSDK/master/images/oldflowchart.png)



## 环境及配置

1.开发工具：Android Studio

2.SDK版本：minSdkVersion：19



## 配置gradle以及权限

1. 在App的gradle中添加声明：

   ![](https://raw.githubusercontent.com/LiuLongCoder/MXRARSDK/master/images/androidconfig.png)

![1584581092228](/images/androidconfig.png)

2. 在项目gradle中添加声明：

   ![](https://raw.githubusercontent.com/LiuLongCoder/MXRARSDK/master/images/androidmaven.png)

![](/images/androidmaven.png)

3. 在App libs目录下添加 mxr_readbook.aar：

   ![](https://raw.githubusercontent.com/LiuLongCoder/MXRARSDK/master/images/androidaar.png)

![](/images/androidaar.png)

4. 在App的manifest.xml中声明权限：相机、相册、麦克风、定位

```Java 
<uses-permission android:name="android.permission.CAMERA" /> 
<uses-feature android:name="android.hardware.camera" /> 
<uses-feature android:name="android.hardware.camera.autofocus" /> <uses-permission android:name="android.permission.INTERNET" /> 
<uses-permission     android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS"     tools:ignore="ProtectedPermissions" /> <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" /> 
<uses-permission android:name="android.permission.READ_PHONE_STATE" /> 
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

5. 必要第三方依赖

   ![](https://raw.githubusercontent.com/LiuLongCoder/MXRARSDK/master/images/androiddependency.png)

![](/images/androiddependency.png)

## 接口说明 

### 一、 SDK初始化

描述：在Application的onCreat中注册 且需要在注册友盟之前

```java
/**
SDK初始化，需要在友盟之前
参数：context 上下文
host： 调用接口地址 可不传 默认“http://211.140.17.101:8122/bos-portal”
**/
MxrSdk.init(Context context, String host);
```

###  二、打开电子图书

​	描述：在APP中新增专区入口，展示图书列表，点击进入阅读图书。

1. 获取图书信息列表(暂未启用)（示例代码仅供参考）

   ```java
   /**
   参数：context 上下文
   page 页码 从0开始
   size 每页数据数 默认10条数据
   返回图书列表 List<Book>
   Book信息
   private String bookGUID; //图书guid
   private String bookName; //图书名称
   private String bookCoverURL //图书封面
   private long bookSize; //图书大小
   //其它信息省略，联系相关人员
   **/
   MxrSdk.getInstance(Context context).getMxrBooks(int page,int size);
   ```
   
2. 打开电子图书

   2.1 调用openBook方法前需获取权限：(示例代码仅供参考)

   ```java
/** 根据bookGUID打开对应图书页面
       bookGUID 图书唯一标识
       miguBookType 0 AR纸质书，1 AR电子书
    */ 
   public void openBook(String bookGUID,int miguBookType){
   if(ContextCompat.checkSelfPermission(MainActivity.this , Manifest.permission.CAMERA)!=PackageManager.PERMISSION_GRANTED){
           ActivityCompat.requestPermissions(MainActivity.this , new String[]{Manifest.permission.CAMERA} , 1);
       }else{
           if(ContextCompat.checkSelfPermission(MainActivity.this , Manifest.permission.WRITE_EXTERNAL_STORAGE)!=PackageManager.PERMISSION_GRANTED){
           ActivityCompat.requestPermissions(MainActivity.this , new String[]{Manifest.permission.WRITE_EXTERNAL_STORAGE} , 2);
           }else{
           MxrSdk.getInstance(MainActivity.this).openBook(bookGUID,miguBookType) ;
           }
       }
   }
   ```
   

### 三、打开模型(停用)

​	场景2描述：在APP原有图书中新增4D模型入口，点击直接打开观看模型。

1. 打开模型

   1.1 open4DModel方法调用前：获取权限(示例代码仅供参考)

   ```java
   public void openModel(String resId){
   if(ContextCompat.checkSelfPermission(MainActivity.this , Manifest.permission.WRITE_EXTERNAL_STORAGE)!=PackageManager.PERMISSION_GRANTED){
           ActivityCompat.requestPermissions(MainActivity.this , new String[]{Manifest.permission.WRITE_EXTERNAL_STORAGE} , 3);
       }else{
       //调用打开模型方法
       }
   }
   ```
   
   
   
   1.2 open4DModel方法说明：
   
   ```java
   /** 
    根据modelId打开对应模型页面
    context 上下文
    modelId 模型唯一标识
   */
   MxrSdk.getInstance(Context context).open4DModel(String modelId);
   ```
   
   

### 四、扫描接口返回

​	描述：扫描到图书二维码  (示例代码仅供参考)
​ 



```java
/**
注册广播oncreate方法中
Action: scan
change: migu
**/ 
LocalBroadcastManager localBroadcastManager = LocalBroadcastManager.getInstance(this);
IntentFilter intentFilter = new IntentFilter();
intentFilter.addAction("scan");
LocalBroadcastManager.registerReceiver(mAdownLoadReceiver,intentFilter);
private BroadcastReceiver mAdownLoadReceiver = new BroadcastReceiver(){
    @Override
    public void onReceive(Context context, Intent intent){
        String change = intent.getStringExtra("change");
        if(!TextUtils.isEmpty(change)){
            Log.e("onreceive:",change);
        }
    }
}
 @Override
protected void onDestroy(){
    super.onDestroy();
    localBroadcastManager.unregisterReceiver(mAdownLoadReceiver);
}

```



### 五、分享接口

​	描述：分享功能通知app,注册BroadcastReceiver(示例代码仅供参考)

```java
/**
 分享图片接口
 埋点 Action :  share  
 分享参数
 分享渠道 shareType  1微信好友，2微信朋友圈，3微博
 微信：shareType ：1
	imgPath ：sd卡存储路径
	wechatSessionTitle：分享标题
	weChatSessionContent：分享内容
	shareURL：分享链接（仅图书分享）
	bookGuid：图书id
	change：wechat
 微博：shareType ：3
	imgPath ：sd卡存储路径
	wechatSessionTitle：分享标题
	weChatSessionContent：分享内容
	shareURL：分享链接（仅图书分享）
	bookGuid：图书id
	change：micro-blog
 微信朋友圈：shareType ：3
	imgPath ：sd卡存储路径
	wechatSessionTitle：分享标题
	weChatSessionContent：分享内容
	shareURL：分享链接（仅图书分享）
	bookGuid：图书id
	change：moments
**/
Intentfilter intentfilter2= new Intentfilter();
intenteilter2.addiction(Mxrsdk.BUTTON_CLICK);
intentpilter2.addiction(Mxrsdk.VIEF_DIPIAY);
broadcastreceiver =new Broadcastreceiver(){
 @Override
 public void onreceive(Context context, Intent intent){
    if(intent. getaciion()equals(Mxrsdk BUTTON CLICK)){
   String change intent.getstringextra("change:);
    if (!Textutils isempty(change)) {
     Log.e(TAG,"migu点击事件:"+ change);
    }else if(intent.getaction()equals(Mxrsdk VIE DIPLAY)){
      String change=intent.getstringextra( name: "change");
     if(Textutils isempty(change)){
       Loge(TAG,"migu页面事件:"+ change);
     }
  }
  }
};
registerreceiver(broadcastreceiver, intentfilter2);



```



### 六、APP调用扫一扫页面

​	描述：从APP发起扫一扫 

```java
/**
mxrScanEntrance: 
1  点击精选右上方的扫一扫按钮
2  点击精选banner & AR专区右上角的扫一扫按钮
3  点击精选5G & AR专区右上角的扫一扫按钮
**/
mxrSdk.getInstance(Context context).mxrScan(int mxrScanEntrance);
/**
mxrScanEntrance:  
1  点击精选右上方的扫一扫按钮
2  点击精选banner & AR专区右上角的扫一扫按钮
3  点击精选5G & AR专区右上角的扫一扫按钮
scanType: 
1  普通扫一扫
2  5G扫一扫
**/
mxrSdk.getInstance(Context context).mxrScan(int mxrScanEntrance);
```



​    



 

### 七、埋点通知

​	描述：SDK通知APP进行埋点

```java
 /**
 Change: 字段参照《咪咕中信统计需求（咪咕方）-v2》
 埋点Action: mxr_view_display(页面展示)
         mxr_button_click（按钮点击）
         bookGuid：图书id
         bookName：图书名
**/
IntentFilter intentFilter = new IntentFilter();
        intentFilter.addAction(MxrSdk.BUTTON_CLICK);
        intentFilter.addAction(MxrSdk.VIEW_DIPLAY);
        intentFilter.addAction("scan");
        intentFilter.addAction(MxrSdk.BOOK_DOWNLOAD_NUM);
        intentFilter.addAction(MxrSdk.AR_MORE);
        intentFilter.addAction(MxrSdk.USER_NEED_LOGIN);
        broadcastReceiver = new BroadcastReceiver() {
            @Override
            public void onReceive(Context context, Intent intent) {
                if (intent.getAction().equals("scan")) {
                    String change = intent.getStringExtra("migu");
                    if (!TextUtils.isEmpty(change)) {
                        Log.e(TAG, change);
                    }
                } else if (intent.getAction().equals(MxrSdk.BOOK_DOWNLOAD_NUM)) {
                    int num = intent.getIntExtra("num", 0);
                    Log.e(TAG, "正在下载数量:" + num);
                } else if (intent.getAction().equals(MxrSdk.USER_NEED_LOGIN)) {
                    Log.e(TAG, "需要登录");
                    showLoginDialog();
                }else if(intent.getAction().equals(MxrSdk.BUTTON_CLICK)){
                    String change = intent.getStringExtra("change");
                    String bookGuid = intent.getStringExtra("bookGuid");
                    String bookName = intent.getStringExtra("bookName");
                    if(!TextUtils.isEmpty(bookGuid)){
                        Log.e(TAG, "migu点击事件:"+change +"--------"+bookGuid+"-------"+bookName);
                    }else {
                        Log.e(TAG, "migu点击事件:"+change);
                    }
                }else if(intent.getAction().equals(MxrSdk.VIEW_DIPLAY)){
                    String change = intent.getStringExtra("change");
                    String bookGuid = intent.getStringExtra("bookGuid");
                    String bookName = intent.getStringExtra("bookName");
                    if(!TextUtils.isEmpty(bookGuid)){
                        Log.e(TAG, "migu页面事件:"+change +"--------"+bookGuid+"-------"+bookName);
                    }else {
                        Log.e(TAG, "migu页面事件:"+change);
                    }
                }else if(intent.getAction().equals(MxrSdk.AR_MORE)){
                    Log.e(TAG, "查看更多ar图书");
                }
            }
        };
        registerReceiver(broadcastReceiver, intentFilter);

```

### 八、用户登录登出

描述：App用户登录登出状态同步到sdk

1.登录

``` java
/**
参数：context 上下文
userStr用户信息Json字符串
例：{userId:123456abcd,userName:张三}
**/
MxrSdk.getInstance(Context context). userLogin (String userStr);
```

2.登出

```java
/**
参数：context 上下文
**/
MxrSdk.getInstance(Context context). userLogout(); 
```

### 九、用户登录广播

描述：sdk检测需要用户登录 发送广播通知App（示例代码仅供参考）

```java
 /**
 埋点Action: mxrSdk_USER_NEED_LOGIN
**/
IntentFilter intentFilter = new IntentFilter();
        intentFilter.addAction(MxrSdk.BUTTON_CLICK);
        intentFilter.addAction(MxrSdk.VIEW_DIPLAY);
        intentFilter.addAction("scan");
        intentFilter.addAction(MxrSdk.BOOK_DOWNLOAD_NUM);
        intentFilter.addAction(MxrSdk.AR_MORE);
        intentFilter.addAction(MxrSdk.USER_NEED_LOGIN);
        broadcastReceiver = new BroadcastReceiver() {
            @Override
            public void onReceive(Context context, Intent intent) {
                if (intent.getAction().equals("scan")) {
                    String change = intent.getStringExtra("migu");
                    if (!TextUtils.isEmpty(change)) {
                        Log.e(TAG, change);
                    }
                } else if (intent.getAction().equals(MxrSdk.BOOK_DOWNLOAD_NUM)) {
                    int num = intent.getIntExtra("num", 0);
                    Log.e(TAG, "正在下载数量:" + num);
                } else if (intent.getAction().equals(MxrSdk.USER_NEED_LOGIN)) {
                    Log.e(TAG, "需要登录");
                    showLoginDialog();
                }else if(intent.getAction().equals(MxrSdk.BUTTON_CLICK)){
                    String change = intent.getStringExtra("change");
                    String bookGuid = intent.getStringExtra("bookGuid");
                    String bookName = intent.getStringExtra("bookName");
                    if(!TextUtils.isEmpty(bookGuid)){
                        Log.e(TAG, "migu点击事件:"+change +"--------"+bookGuid+"-------"+bookName);
                    }else {
                        Log.e(TAG, "migu点击事件:"+change);
                    }
                }else if(intent.getAction().equals(MxrSdk.VIEW_DIPLAY)){
                    String change = intent.getStringExtra("change");
                    String bookGuid = intent.getStringExtra("bookGuid");
                    String bookName = intent.getStringExtra("bookName");
                    if(!TextUtils.isEmpty(bookGuid)){
                        Log.e(TAG, "migu页面事件:"+change +"--------"+bookGuid+"-------"+bookName);
                    }else {
                        Log.e(TAG, "migu页面事件:"+change);
                    }
                }else if(intent.getAction().equals(MxrSdk.AR_MORE)){
                    Log.e(TAG, "查看更多ar图书");
                }
            }
        };
        registerReceiver(broadcastReceiver, intentFilter);
```

### 十、查看更多图书

描述：sdk发起查看更多ar图书 广播（示例代码仅供参考）

```java
 /**
 埋点Action: mxrSdk_armore
**/
IntentFilter intentFilter = new IntentFilter();
        intentFilter.addAction(MxrSdk.BUTTON_CLICK);
        intentFilter.addAction(MxrSdk.VIEW_DIPLAY);
        intentFilter.addAction("scan");
        intentFilter.addAction(MxrSdk.BOOK_DOWNLOAD_NUM);
        intentFilter.addAction(MxrSdk.AR_MORE);
        intentFilter.addAction(MxrSdk.USER_NEED_LOGIN);
        broadcastReceiver = new BroadcastReceiver() {
            @Override
            public void onReceive(Context context, Intent intent) {
                if (intent.getAction().equals("scan")) {
                    String change = intent.getStringExtra("migu");
                    if (!TextUtils.isEmpty(change)) {
                        Log.e(TAG, change);
                    }
                } else if (intent.getAction().equals(MxrSdk.BOOK_DOWNLOAD_NUM)) {
                    int num = intent.getIntExtra("num", 0);
                    Log.e(TAG, "正在下载数量:" + num);
                } else if (intent.getAction().equals(MxrSdk.USER_NEED_LOGIN)) {
                    Log.e(TAG, "需要登录");
                    showLoginDialog();
                }else if(intent.getAction().equals(MxrSdk.BUTTON_CLICK)){
                    String change = intent.getStringExtra("change");
                    String bookGuid = intent.getStringExtra("bookGuid");
                    String bookName = intent.getStringExtra("bookName");
                    if(!TextUtils.isEmpty(bookGuid)){
                        Log.e(TAG, "migu点击事件:"+change +"--------"+bookGuid+"-------"+bookName);
                    }else {
                        Log.e(TAG, "migu点击事件:"+change);
                    }
                }else if(intent.getAction().equals(MxrSdk.VIEW_DIPLAY)){
                    String change = intent.getStringExtra("change");
                    String bookGuid = intent.getStringExtra("bookGuid");
                    String bookName = intent.getStringExtra("bookName");
                    if(!TextUtils.isEmpty(bookGuid)){
                        Log.e(TAG, "migu页面事件:"+change +"--------"+bookGuid+"-------"+bookName);
                    }else {
                        Log.e(TAG, "migu页面事件:"+change);
                    }
                }else if(intent.getAction().equals(MxrSdk.AR_MORE)){
                    Log.e(TAG, "查看更多ar图书");
                }
            }
        };
        registerReceiver(broadcastReceiver, intentFilter);
```

### 十一、正在下载图书数量

描述：sdk反馈正在下载图书数量 广播（示例代码仅供参考）

```java
 /**埋点
 Action: mxrSdk_BOOK_DOWNLOAD_NUM
 change：num（正在下载数量）
         totalNum（本地图书总数量）
**/
IntentFilter intentFilter = new IntentFilter();
        intentFilter.addAction(MxrSdk.BUTTON_CLICK);
        intentFilter.addAction(MxrSdk.VIEW_DIPLAY);
        intentFilter.addAction("scan");
        intentFilter.addAction(MxrSdk.BOOK_DOWNLOAD_NUM);
        intentFilter.addAction(MxrSdk.AR_MORE);
        intentFilter.addAction(MxrSdk.USER_NEED_LOGIN);
        broadcastReceiver = new BroadcastReceiver() {
            @Override
            public void onReceive(Context context, Intent intent) {
                if (intent.getAction().equals("scan")) {
                    String change = intent.getStringExtra("migu");
                    if (!TextUtils.isEmpty(change)) {
                        Log.e(TAG, change);
                    }
                } else if (intent.getAction().equals(MxrSdk.BOOK_DOWNLOAD_NUM)) {
                    int num = intent.getIntExtra("num", 0);
                    Log.e(TAG, "正在下载数量:" + num);
                } else if (intent.getAction().equals(MxrSdk.USER_NEED_LOGIN)) {
                    Log.e(TAG, "需要登录");
                    showLoginDialog();
                }else if(intent.getAction().equals(MxrSdk.BUTTON_CLICK)){
                    String change = intent.getStringExtra("change");
                    String bookGuid = intent.getStringExtra("bookGuid");
                    String bookName = intent.getStringExtra("bookName");
                    if(!TextUtils.isEmpty(bookGuid)){
                        Log.e(TAG, "migu点击事件:"+change +"--------"+bookGuid+"-------"+bookName);
                    }else {
                        Log.e(TAG, "migu点击事件:"+change);
                    }
                }else if(intent.getAction().equals(MxrSdk.VIEW_DIPLAY)){
                    String change = intent.getStringExtra("change");
                    String bookGuid = intent.getStringExtra("bookGuid");
                    String bookName = intent.getStringExtra("bookName");
                    if(!TextUtils.isEmpty(bookGuid)){
                        Log.e(TAG, "migu页面事件:"+change +"--------"+bookGuid+"-------"+bookName);
                    }else {
                        Log.e(TAG, "migu页面事件:"+change);
                    }
                }else if(intent.getAction().equals(MxrSdk.AR_MORE)){
                    Log.e(TAG, "查看更多ar图书");
                }
            }
        };
        registerReceiver(broadcastReceiver, intentFilter);
```

### 十二、 关闭扫描

```java
/**
参数：context 上下文
**/
MxrSdk.getInstance(Context context).mxrCloseScan（）;
```

### 十三、 打开APP开始下载任务

描述：在打开APP时调用，用以开始图书下载等待队列的下载任务

```java
/**
参数：context 上下文
**/
MxrSdk.getInstance(Context context).startDownload();
```

### 十四、 获取本地图书总数

描述：在Application的onCreat中注册 且需要在注册友盟之前

```java
/**
参数：context 上下文
**/
MxrSdk.getInstance(Context context).getUserBookNum();
```

