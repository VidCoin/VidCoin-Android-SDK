# Vidcoin Android SDK - QuickStart Guide

![Vidcoin](https://documentation.vidcoin.com/images/Vidcoin-Logo.png)
SDK version: 1.4.0        
Manager: https://manager.vidcoin.com        
Contact: publishers@vidcoin.com        

## Overview						
Vidcoin Android SDK enables you to broadcast videos in your apps in order to give users access to restricted content or to obtain virtual currency for free.
This document and the archive contain all the information you need in order to integrate our solution and start broadcasting videos. You can also contact us directly at publishers@vidcoin.com.

## Account Settings						
An approved and valid publisher account is necessary in order to use the SDK. If you do not have an account, please sign up directly on [https://manager.vidcoin.com](https://manager.vidcoin.com).
This also grants you access to statistics and detailed reports in our Publishers Back-Office.

### App Creation
You can access App Creation in the “App” menu of your publisher area. The creation of an app generates an “App ID” which will be mandatory in order to use the Vidcoin Android SDK.

### Placement Creation			
A Placement represents the zone in your app where the videos will be available for broadcasting.

Basic configuration allows users to access a specific part of the app by watching a video. Once the advertisement's guaranteed duration is reached, you can unlock the content.		

If you want to reward your users with items or currency, you will have more settings available : 			
- Reward name: name of your virtual currency.		
- You can choose 2 types of rewards for the user:		
	- Conversion rate: rate defining how much 1€ equals of your virtual currency. Please note that we recommend that you use a low exchange rate such that 1€ = 1000 of your virtual currency. This will prevent any problems of crediting users with small amounts of virtual currency.
	- Fixed reward: amount of virtual currency the user will be rewarded for 1 view.
	- Callback URL (Optional): URL that will be used to validate the transaction on your servers.		

## Setting up the framework in your app
*Info: the sdk was built using Gradle 1.2.3, and requires a deployment target ≥ Android 4.0 (ICE_CREAM_SANDWICH)*		

### Step 1: Getting the sdk
Download and open the zip file from Github :  [https://github.com/VidCoin/VidCoin-Android-SDK](https://github.com/VidCoin/VidCoin-Android-SDK)

### Step 2: Adding and configuring the sdk in the project
- Add these lines to your app’s build.gradle:
```java
repositories {
	flatDir {
		dirs 'libs'
	}
}
```
- Add the sdkandroid-vidcoin-version.aar to the libs folder of your project
- Add a dependency to the aar and the other dependencies:
```java
dependencies {
	compile 'com.android.support:appcompat-v7:22.0.0'
	compile 'com.android.support:support-v4:22.0.0'
	compile 'com.mcxiaoke.volley:library:1.0.10'
	compile 'com.google.code.gson:gson:2.3'
	compile 'com.google.android.gms:play-services-ads:7.0.0'
	compile 'com.vidcoin.sdkandroid.core:sdkandroid:vidcoin:1.4.0@aar'
	compile 'com.vidcoin.sdkandroid:sdkandroidnative:vidcoin:1.4.0@aar'
}
```
- Sync your project with gradle files and it should build correctly

**Important note :** If you have already included the Play Services, you should remove the following line :		
`compile 'com.google.android.gms:play-services-ads:7.0.0'`

**Optional: Add the WAKE\_LOCK permission to your manifest**
Vidcoin recommends you to use the WAKE\_LOCK permission to your AndroidManifest.xml, using the following line :
```xml
<uses-permission android:name="android.permission.WAKE\_LOCK"/>
```
The WAKE\_LOCK permission helps the screen to stay awake during the duration of the video. It is recommended to add this to your manifest to increase user-experience, therefore more views.

### Step 3: Updating the SDK
Replace the current `sdkandroid-vidcoin-version.aar` and `sdkandroidnative-vidcoin-version.aar` by the new ones and resync your project.

### Step 4: Start the SDK
It’s now time to start the SDK. The only way to get an instance of the Android Vidcoin SDK is calling the method `VidCoin.getInstance()`. We advise you to initialize it this way:

```java
private final VidCoin mVidCoin = VidCoin.getInstance();
```

We recommended starting VidCoin by placing a call to onCreate() as soon as possible. For example, you can do the following:

```java
public class MainActivity extends Activity {
	public void onCreate(Bundle savedInstanceState) {
		// Your custom initialisation
		mVidCoin.init(this,  "yourGameId");
	}
}
```

For more details about the parameters, please refer to the javadoc. In order for our SDK to work well, you have to override the native Android methods `onStart()` and `onStop()` in your activity, as shown here:

```java
public class MainActivity extends Activity {
	@Override
	protected void onStart() {
		super.onStart();
		mVidCoin.onStart();
	}

	@Override
	protected void onStop() {
		super.onStop();
		mVidCoin.onStop();
	}
}
```

Vidcoin uses a callback system in order to notify your app of the SDK’s state. You have two ways to set your callback class:

- Start the sdk as described above, and assign the callback class later :
```java
mVidCoin.init(this, "yourGameId");
[...]
mVidCoin.setVidCoinCallBack("placementCode", yourCallBackInstance);
```
A callback instance is registered with a placement code.
You can also attach the callback when you request an ad for a given placement.

- Start the framework and assign the delegate when you request an ad later :
```java
public class MainActivity extends Activity implements VidCoinCallBack {
	public void onCreate(Bundle savedInstanceState) {
		// Your custom initialisation
		mVidCoin.requestAdForPlacement("placementCode", yourCallBackInstance);
	}
}
```

In any case, make sure the callback instance that you give as a parameter implements `VidCoinCallBack` as above. Assigning a callback instance to your Vidcoin instance is a simple way to get notified when there is a status updated, or when a video is presented / dismissed. For more information on `VidCoinCallBack` and the available methods, please refer to the associated sections of this document and the Javadoc.

### Step 4 (bis) : Start the SDK with GDPR info
Your application has to get the user consent before you init the SDK otherwise he will not be targeted. Publisher must controls the GDPR consent process.

There are 2 parameters for GDPR:

| Key  | Values | Description |
| :-------------: | :-------------: | :-------------: |
| hasUserConsent | YES/NO | Is the user has given his consent to be tracked. The default value is false|
| isGDPRApplicable | YES/NO | Is the user in country with GDPR. The default value is true |

To change the default value on the sdk initialization you can use two provided parameters :

```java
public class MainActivity extends Activity {
	public void onCreate(Bundle savedInstanceState) {
		// Your custom initialisation
		mVidCoin.init(this,  "yourGameId", true, true);
	}
}
```

Here is the method signature :

```java
public void init(Context parentContext, String appId, boolean hasUserConsent, boolean isGDPRApplicable)
```

### Step 5: Update GDPR status
You can update the status of the user consent.

To update the user consent:

```java
mVidCoin.setUserConsent(true);
```

To update the user GDRP applicable:

```java
mVidCoin.setGDPRApplicable(true);
```

These methods must be called after the `init` if you want to change the user's status consent.


### Optional step: Enable logging
In addition to implementing the VidCoinCallBack, you can enable extra logging by making a call to the following method:

```java
mVidCoin.setVerboseTag(true);
```

With this command, you will be informed of all the sdk actions and state.

**Notice:** it is recommended that you set the verbose tag to false before releasing your app, as it serves strictly to debug purposes.

### Step 6: Update the user information
Here is the way to send us information about the user.

```java
Map<VidCoinBase.VCUserInfos, String> userInfos = new HashMap<VidCoinBase.VCUserInfos, String>();  

userInfos.put(VidCoinBase.VCUserInfos.VC_USER_APP_ID, "UserName");

mVidCoin.setUserInfos(userInfos);
```

Or, with static import:

```java
import com.vidcoin.sdkandroid.core.VidCoinBase.VCUserInfos;
import static com.vidcoin.sdkandroid.core.VidCoinBase.VCUserInfos.VC_USER_APP_ID;

Map<VCUserInfos, String> userInfos = new HashMap<VCUserInfos, String>();
userInfos.put(VC_USER_APP_ID, "UserName");
mVidCoin.setUserInfos(userInfos);
```

You can pass any of these key-value pairs, all keys and values being String objects.

| Key | Values | Description |
| :-------------: | :-------------: | :-------------: |
| VC_USER_APP_ID | String | This is the string you use to identify the user in your app. This will be used in case you implement a server callback. |

### Step 7: Request for an ad

Once you want to request an ad, you can call :

```java
mVidcoin.requestAdForPlacement("placementCode", vidcoinCallback);
```

You should pass your placement code and the callback. You can use the same callback for multiple request and you will be able to dispatch notification events about campaigns updates thanks to the `placementCode`. See below.


### Step 8: Check if a video is available
You may want to know if a video is available for a given placement. For example, you shouldn’t display a button inviting the user to watch a video if there is none in cache. Calling the class method `videoIsAvailableForPlacement()` is the way to go:

```java
boolean videoIsAvailable = VidCoin.videoIsAvailableForPlacement("yourPlacementCode");
if (videoIsAvailable) {
	// Present a button inviting the user to play a video ad
}
```

This method will return a `boolean`, equal to `true` if a video is ready to be played for the placement you passed as parameter, and `false` otherwise.

### Step 9: Play a video
You can present a video to the user by calling the following method:

```java
mVidCoin.playAdForPlacement(parentActivity, "yourPlacementCode", animation);
```

Where `parentActivity` is an instance of the parent activity that will be the parent of the video player. You can pass an animation as an int, referring to an animation.xml file into the res folder of your app. If you want to use the default animation, you can pass -1. If a video is ready to be played for this placement, the player will appear and the video will start.

### Step 10: Using VidCoinCallBack (optional)
If you want to be notified of actions happening in the sdk, you can set an object to be Vidcoin’s callback (as seen in step 4). Your object can then implement some (or all) of  the following methods :

- This method will be called regularly, whenever the available video campaigns change:
```java
void vidCoinCampaignsUpdate(String placementCode) {
	// Campaigns have changed (e.g. a video has been downloaded, etc.)
}
```

- This method will be called just before the video player appears on screen. This is for example the perfect time to pause the audio of your game:
```java
void vidCoinViewWillAppear() {
	// The player will appear
}
```

- This method is called right after the video player left the screen. You may now resume the game audio if you paused it in the previous delegate method:
```java
void vidCoinViewDidDisappearWithViewInformation(SimpleArrayMap<VidCoin.VCData, String> data) {
		// Analyze the result of the video viewed
 }
 ```

You can access basic information about the video that was just watched in the viewInfo dictionary passed as parameter. Here are the values you can access:
```java
VC_DATA_STATUS_CODE statusCode = data.get(VidCoin.VCData.VC_DATA_STATUS_CODE);
VC_DATA_REWARD reward = data.get(VidCoin.VCData.VC_DATA_REWARD);
```

The `statusCode` var can be compared to the integers `VC_STATUS_CODE_SUCCESS` (the video was watched correctly), `VC_STATUS_CODE_CANCEL` (the user canceled the video), and `VC_STATUS_CODE_ERROR` (something went wrong, and the user couldn’t watch the video).

The `reward` var informs you on the amount of virtual currency that the user will be rewarded once the view is validated on the server-side.

- This method is called when the server answers after the sdk tried to validate the view:
```java
void vidCoinDidValidateView(SimpleArrayMap<VidCoin.VCData, String> data) {
	// Analyze the result of the video viewed
}
```

You can access the same information as the previous method, but in this case, the `status` value is either `VC_STATUS_CODE_SUCCESS` or `VC_STATUS_CODE_ERROR`, depending on whether the server did validate the view or not.

The value associated to the `reward` key contains the amount of your in-game currency that was *actually* credited to the user on the server side. Note that this method will always be called after the previous callback method `vidcoinViewDidDisappearWithViewInformation`.

### Step 11: Proguard Rules (optional)
If you use Proguard in your release process,you should add the following rules in your  *proguard-rules.pro* file:  
```
-keepattributes Signature
-keepattributes *Annotation*

-keep class com.google.gson.** { *; }
-keep class com.android.volley.** { *; }
-keep public class com.google.android.gms.* { public *; }

-keep class com.vidcoin.** { public *; }
-keepclassmembers enum com.vidcoin.** { *; }

-dontwarn com.android.volley.**
-dontwarn com.google.android.gms.**
```

## Advice for integration
- The integration of Vidcoin's solution must not deteriorate the user experience.
- Because of certain parameters and restrictions (country, number of videos already viewed...) there may not always be available videos.
- A non-blocking integration consists in offering Vidcoin to the user as an alternate solution to the classical running of the game. Here is an example :
	- The user is presented a given view controller of the game
	- A button “Please sign-in to access your content” is presented
	- Make the activity and implement the vidcoinCampaignsUpdate method of VidCoinCallBack
	- In this method, if a call to Vidcoin’s class method videoIsAvailableForPlacement returns true for the given placement code, a second button can be presented: “Watch a video to access your content”
	- If the call returns false, the second button should not be made visible.

	This type of integration avoids proposing a user to watch a video if none is available, and does not damage the user experience. **You should not expect videos to be always available.** The sample app gives a good example of a recommended integration.


## Server-side callback (optional)
If you want to use a server-side callback to credit your users, please refer to the following documentation: [http://documentation.vidcoin.com/Vidcoin-Server-Callback.html](http://documentation.vidcoin.com/Vidcoin-Server-Callback.html)
