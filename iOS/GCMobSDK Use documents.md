###GCMobSDK Use documents

##### 1.Configuration Project

```objective-c
a. Add 'AdSupport.framework' to 'TARGETS->Build Phases->Link Binary With Libraries'

b. Set '-ObjC' in 'Build Settings ->Other Linker Flags'

c. Add 'NSAppTransportSecurity',Set 'NSAllowsArbitraryLoads' is 'YES' in info.plist file
```

##### 2.About ViewAd

```objective-c
a. Create ViewAd and Request ViewAd
	GCMViewAd *viewad = [[GCMViewAd alloc] init]; 
  [viewad loadViewAdWithToken:@"1" adViewFrame:CGRectMake(10,30,320,50)];

b. set Delegate and callback
  viewad.delegate = self;
//when load ad success，please add ad.adview to superView
  - (void)GCMobViewAdLoadSuccess:(GCMViewAd *)ad {
      NSLog(@"ad load success, add ad.adView to parent view");
      [self.view addSubview:ad.adView];
  }
//when load ad failed
  - (void)GCMobViewAd:(GCMViewAd *)fullscreenVideoAd didFailWithError:(NSError *)error {
      NSLog(@"view ad load errr: %@",error);
  }
//when click ad
  - (void)GCMobViewAdClick:(GCMViewAd *)ad {
      NSLog(@"click ad");
  }
```

