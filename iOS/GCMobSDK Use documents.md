### GCMobSDK 使用文档

##### 1.配置工程

```objective-c
a. 在'TARGETS->Build Phases->Link Binary With Libraries'中添加'AdSupport.framework'

b. 设置'Build Settings'中'Other Linker Flags'为'-ObjC'

c. 设置info.plist,添加'NSAppTransportSecurity'，设置'NSAllowsArbitraryLoads'为'YES'
```


##### 2.关于展示广告

```objective-c
a. 创建广告对象并请求展示广告
  GCMFullScreenAd *ad = [[GCMFullScreenAd alloc] init]; 
  ad.delegate = self;
  self.ad = ad;
  [ad loadFullScreentAdWithToken:@"10007" delegate:self];

b. 设置代理监听回调
  
  @protocol GCMobFullScreenAdDelegate <NSObject>
  @optional
  /**
   This method is called when video ad material loaded successfully.
   */
  - (void)GCMobfullscreenVideoMaterialMetaAdDidLoad:(GCMFullScreenAd *)fullscreenVideoAd;

  /**
   This method is called when video ad materia failed to load.
   @param error : the reason of error
   */
  - (void)GCMobfullscreenVideoAd:(GCMFullScreenAd *)fullscreenVideoAd didFailWithError:(NSError *)error;

  /**
   This method is called when video ad slot will be showing.
   */
  - (void)GCMobfullscreenVideoAdWillVisible:(GCMFullScreenAd *)fullscreenVideoAd;

  /**
   This method is called when video ad slot has been shown.
   */
  - (void)GCMobfullscreenVideoAdDidVisible:(GCMFullScreenAd *)fullscreenVideoAd;

  /**
   This method is called when video ad is clicked.
   */
  - (void)GCMobfullscreenVideoAdDidClick:(GCMFullScreenAd *)fullscreenVideoAd;

  /**
   This method is called when video ad is about to close.
   */
  - (void)GCMobfullscreenVideoAdWillClose:(GCMFullScreenAd *)fullscreenVideoAd;

  /**
   This method is called when video ad is closed.
   */
  - (void)GCMobfullscreenVideoAdDidClose:(GCMFullScreenAd *)fullscreenVideoAd;

  /**
   This method is called when video ad playingFinish
   */
  - (void)GCMobfullscreenVideoAdPlayingFinish:(GCMFullScreenAd *)fullscreenVideoAd;

  @end
```





