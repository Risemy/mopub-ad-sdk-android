# AD SDK使用教程
[TOC]

## 系统要求
android 4.1 或更高版本
Android Studio 3.4

## 集成
 1. 在 build.gradle 的 allprojects > repositories里面添加
 ```java
 maven { url "https://s3.amazonaws.com/moat-sdk-builds" }
 maven { url 'https://maven.google.com' }
 maven {url "https://dl.bintray.com/ironsource-mobile/android-sdk"}
 maven { url "https://raw.githubusercontent.com/Risemy/mopub-ad-sdk-android/master" }
 ```
 2. 在 build.gradle 的  dependencies 里面添加：
```java
 implementation 'com.wnl:mopub_ad_sdk_android:1.0.4'
```
 3. build项目

## 缓存策略
* 初始化成功SDK后，将会自动缓存一个插屏，全部奖励视频广告
* 默认每隔2分钟缓存一次之前未加载成功或被消耗的插屏、视频广告
* 当已经加载过的广告被消耗且已关闭，就会重新加载该广告

## 使用案例

#### 一.初始化SDK
``` java
		//初始化配置
        AdConfig.Builder builder = new AdConfig.Builder()
                .setIsAutoLoadAd(true) //默认为true 自动缓存视频广告
                .setRepeatTime(120000)//配置自动加载广告，每两分钟自动检查是否有缓存视频广告，如果有视频广告缓存则不做任何操作，没有视频广告缓存则请求
                .setOverTime(30000)//主动加载广告  设置请求超时时间  默认30秒
                .setAppFlyerKey("")//默认值为发行商所配置的key
                .setBannerAdUnits(bannerList)//banner广告单元 List<String>数组
                .setInterstitialAdUnits(interstitialList)//插屏广告单元 List<String>数组
                .setRewardedVideoAdUnits(videoList);//视频广告单元 List<String>数组
				
		AdManager.initAd(this, builder.build(), new SdkInitListener() {
            @Override
            public void onInitializationFinished() {
                //SDK初始化完成回调监听
            }
        });

```

##### 1.1 加载一个Banner
直接加载，需要传入banner的位置、大小信息和父类视图
``` java
  /**
     * 加载banner广告
     *
     * @param context          上下文
     * @param viewGroup        父控件
     * @param bannerAdListener 回调监听
     */
  AdManager.getInstance().loadAndShowBannerAd(Context context, ViewGroup viewGroup, final BannerAdListener
  bannerAdListener)
```
##### 1.2 隐藏一个Banner
隐藏当前显示的一个Banner
``` java
  /**
     * 隐藏banner广告
     */
  AdManager.getInstance().hideBannerAd()
```

#### 1.2 开启自动缓存模式
##### 1.2.1 显示Interstitial
``` java
  /**
     * 展示插屏广告
     *
     * @param activity               activity
     * @param interstitialAdListener 显示回调
     */
  AdManager.getInstance().showInterstitialAd(Activity activity, InterstitialAdListener interstitialAdListener)
```
##### 1.2.2 展示RewardedVideo
建议先判断` AdManager.getInstance()hasRewardedVideo`是否为YES，然后调用
``` java
  /**
     * 显示广告
     *
     * @param activity           上下文
     * @param rewardedAdListener 显示监听
     */
  AdManager.getInstance().showRewardedVideoAd(Activity activity, RewardedAdListener rewardedAdListener)
```

#### 1.3 关闭自动缓存模式
插屏、激励视频广告可在关闭自动缓存时去请求，但不建议这么做
##### 1.3.1 加载Interstitial
``` java
  //在需要加载的地方调用load方法,在协议回调中调用Show方法即可展示
  /**
     * 加载插屏广告
     *
     * @param activity               activity
     * @param interstitialAdListener 加载回调
     */
  AdManager.getInstance().loadInterstitialAd(Activity activity, InterstitialAdListener interstitialAdListener)

```
##### 1.3.2 加载RewardedVideo
``` java
//在需要加载的地方调用load方法,在协议回调中调用Show方法即可展示
 /**
     * 缓存视频广告
     *
     * @param activity           上下文
     * @param rewardedAdListener 缓存监听
     */
  AdManager.getInstance().loadRewardedVideoAd(Activity activity, RewardedAdListener rewardedAdListener)
```

#### 二.统计分析

**2.1 Appsflyer**

* 须在初始化SDK时 AdConfig.Builder 里面 setAppFlyerKey(你的appsflyer ID) 默认为发行商的ID

**2.2 FaceBook**
***注意：必填项，如不填写，将无法初始化FaceBook 数据统计。***

在项目里面 AndroidManifest 的 Application 里面配置 facebook_app_id 是facebook开发者后台申请的 app_id
https://developers.facebook.com/docs/android/getting-started/
```java
   <meta-data android:name="com.facebook.sdk.ApplicationId" android:value="你的facebook_app_id"/>
```

#### 三.协议回调一览
**3.1 banner广告相关回调**
```java
  public interface BannerAdListener {
    void onBannerLoaded();  //加载成功

    void onBannerFailed(String errorMsg);  //加载失败

    void onBannerClicked();  //点击了广告
}
```

**3.2 插屏广告相关回调**
```java
public interface InterstitialAdListener {
    void onInterstitialLoaded();  //加载成功

    void onInterstitialFailed(String errorMsg);  //加载失败

    void onInterstitialShown();  //已经显示

    void onInterstitialClicked(); //点击插屏广告

    void onInterstitialDismissed();  //已经关闭
}
```

**3.3 激励视频相关回调**
```java
public interface RewardedAdListener {
    void onRewardedVideoLoadSuccess();  //激励视频加载成功

    void onRewardedVideoLoadFailure(String errorMsg);  //激励视频加载失败

    void onRewardedVideoClicked();  //点击激励视频
 
    void onRewardedVideoClosed();   //关闭激励视频

    void onRewardedVideoCompleted();  //激励视频播放完毕

    void onRewardedVideoStarted();  // //激励视频开始播放

}
```
