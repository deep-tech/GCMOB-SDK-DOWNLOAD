# OpenAds SDK Integration Guideline

* [1. Import the SDK Package](#step1)
* [2. Android Manifest Configuration](#step2)
* [3. Android code obfuscation](#step3)
* [4. Targeting Android P](#step4)
* [5. Building for AndroidX](#step5)
* [6. Initialize the SDK](#step6)
* [7. Prepare for load ad](#step7)
* [8. Integrating Full-screen Video ad](#Full-screen)
  

## <a name="step1">1. Import the SDK Package</a>  

* Apply for App SlotID

    Please make sure you have at least one SlotID in GCMob Platform. 
    
* Add the following to your app’s project level build.gradle file inside the repositories section:

```
allprojects {
    repositories {
        maven { 
        		url 'https://google.bintray.com/exoplayer/' 
        }
    }
    ...
}
```

* Import SDK to your module 
  
    Copy GCMob_adsdk_xxx.jar to your Application Module/libs folder (Create one manually if there isn’t one), and add the following code to your app’s build.gradle:        

```
android {
    compileOptions {
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

depedencies {
    implementation files('libs/GCMob_adsdk_xxx.jar')
    implementation 'com.google.android.exoplayer:exoplayer:2.9.6'
}
```

## <a name="step2">2. Android Manifest Configuration</a>  

* Add Permissions

```
<!--Necessary-->
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

<!--Optional -->
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
<uses-permission android:name="android.permission.REQUEST_INSTALL_PACKAGES" />
```

* Operating Environment Configuration

    This SDK runs on Android 4.4 (API Level 19) and above.

```
<uses-sdk android:minSdkVersion="19"    android:targetSdkVersion="28" />
```

## <a name="step3">3. Android code obfuscation</a> 

If you need to use proguard to obfuscate your code, be sure not to obfuscate the SDK code. Please add the following configuration at the end of the proguard.cfg file (or other obfuscated files):

```
   -keep public class com.jumpraw.**{*;}
```

## <a name="step4">4. Targeting Android P</a> 

When targeting Android P and using video ads, you must add an exception rule to 'localhost' found in your apps Network Security Configuration.

* Make the Network Security Configuration xml with the code below:

```
    <?xml version="1.0" encoding="utf-8"?>
    <network-security-config>
       <domain-config cleartextTrafficPermitted="true">
           <domain includeSubdomains="true">127.0.0.1</domain>
       </domain-config>
    </network-security-config>
```
* Modify your manifest of your app to point to this file.

```
    <?xml version="1.0" encoding="utf-8"?>
    <manifest ... >
        <application android:networkSecurityConfig="@xml/network_security_config"
                        ... >
            ...
        </application>
    </manifest>
```

## <a name="step5">5. Building for AndroidX</a>  

If you use AndroidX libraries, you can update your build to ensure compatibility. To use androidx-namespaced libraries in a new project, you must set the compile SDK to Android 9.0 (API level 28) or higher.

If you have a native integration, add the following parameters to your gradle.properties file:

```
    android.useAndroidX=true
    android.enableJetifier=true
```


## <a name="step6">6. Initialize the SDK</a>  

We suggest the developer initialize the GCMob SDK in the Application#onCreate() method. You can initialize the ad SDK for specific processes only.

```
   JRAdSDK.init(context, "your slotID");
```


## <a name="step7">7. Prepare for load ad</a>

* Build JRAdManager Object
  
    JRAdManager object is the interface to Integrate the entire SDK and can be used for ad acquisition. 
    
```
    //Must be called after initialization.
    JRAdManager jrAdManager = JRAdSDK.getAdManager();
```

* Build JRAdNative Object

    JRAdNative is the interface to load ads, such as full-screen video ads etc. It also provides a load callback listener. It is recommended to be the member variable of the Activity.

```
    JRAdNative jrAdNative = jrAdManager.createAdNative(context);
```

* Build AdSlot Object
  
    The AdSlot object is the ad information that needs to be set when JRAdNative loads the ad. 
    
```
    AdSlot adSlot = new AdSlot.Builder()
            .setSlotId("your slotID")
            .build();
```


### <a name="Full-screen">8. Integrating Full-screen Video ad</a>

The SDK provides full-screen video ad for the developers, the ad plays in full screen and can be skipped after a certain time, watching the whole video is not required. 

* update Manifest for Full-screen Video Ads

 ```
    <activity
        android:name="com.jumpraw.ads.openad.core.JRVideoActivity"
        android:configChanges="keyboardHidden|orientation|screenSize" />   
 ```

* Load Full-screen Video Ads

    The developers can call loadFullScreenVideoAd(AdSlot adSlot, FullScreenVideoAdListener Listener) to asynchronously load a full-screen video ad.

```j
    //set the ad parameters
    AdSlot adSlot = new AdSlot.Builder()
                .setSlotId("your full-screen slotID")
                .build();
                 
    /**
     * Load Full-screen Video Ads
     *
     * AdSlot is the information requested by the user 
     * FullScreenVideoAdListener is the callback listener of a successful or failed loading
     */         
    jrAdNative.loadFullScreenVideoAd(adSlot, new JRAdNative.FullScreenVideoAdListener() {
            @Override
            public void onError(int code, String message) {
                Log.i(TAG, "onError: " + message);
            }

            @Override
            public void onFullScreenVideoAdLoad(JRFullScreenVideoAd ad) {
                Log.i(TAG, "onFullScreenVideoAdLoad");
                
                if (ad != null) {
                    jrFullScreenVideoAd = ad;
                    
                    jrFullScreenVideoAd.setFullScreenVideoAdInteractionListener(new JRFullScreenVideoAd.FullScreenVideoAdInteractionListener() {
                        @Override
                        public void onAdShow() {
                            Log.i(TAG, "onAdShow");
                        }

                        @Override
                        public void onAdVideoBarClick() {
                            Log.i(TAG, "onAdVideoBarClick");
                        }

                        @Override
                        public void onAdClose() {
                            Log.i(TAG, "onAdClose");
                        }

                        @Override
                        public void onVideoComplete() {
                            Log.i(TAG, "onVideoComplete");
                        }

                        @Override
                        public void onVideoError() {
                            Log.i(TAG, "onVideoError: ");
                            Utils.showToast(context, "onVideoError");
                        }

                        @Override
                        public void onSkippedVideo() {
                            Log.i(TAG, "onSkippedVideo");
                        }
                    });
                }
            }

            @Override
            public void onFullScreenVideoCached() {
                Log.i(TAG, "onFullScreenVideoCached");
            }
        });               
```

* Display Video Ad

```
    if (jrFullScreenVideoAd != null) {
        jrFullScreenVideoAd.showFullScreenVideoAd(activity);
    }
```





