👋 Hello! This is the SDK integration guide specifically for Android provided by our technical team. We will provide you with complete information including:

- Integration requirements.
- Detailed instructions for integrating the SDK input resources.
- Detailed instructions for integrating the functional methods available in the SDK. *(This section will need to be combined with other related technical documentation from us to better understand and integrate accurately.)*

💬 If you have any questions regarding the content, please contact our technical team for support in resolving the issue.

## 1. Integration Requirements

Unity version requirement: **2022.2.0a18** or higher.

SDK level requirements for the project:

- **Minimum SDK**: 24
- **Target SDK**: 36 or higher.

---

## 2. Input Resources Integration Guide

📁 We have named the SDK file with the package name/bundle ID **placed inside parentheses**. *(The package name in the image below is only an example; the package name value may vary as provided by our technical team.)*

![](https://cdn-media.gzone.tech/media/tech/sdk/android/packagename.png)

> 👉 This will be the official package name used throughout the project. Please remember this information to configure the project details accurately.

Extract this file and open it. Inside you will find the resource files to integrate into the project:

| FILE NAME | ABBREVIATED NAME | FUNCTION |
| --- | --- | --- |
| **SDKGame-v…-release.aar**<br> *(The ellipsis ... value may be changed by our technical team; that value is typically the version.)* | **SDK** file | 📦 The main SDK file from our technical team, containing pre-built functional methods. |
| **google-services.json** | **google** or **google-services** file | ⚙️ Google services configuration file corresponding to the package name we provide. |
| **…client.xml**<br> *(The ellipsis ... value may be changed by our technical team; it is typically the project code.)* | **client** or **client portal** file | 🧠 Input information file of the game that enables the SDK to function correctly. |
| **…key.jks**<br> *(The ellipsis ... value may be changed by our technical team; it is typically the project code.)* | **keystore** file | 🔑 Keystore file used to sign the application before publishing, and to verify the application when submitting to Google Play. |

### 2.1. Place the resource files into the Android project

In Android Studio, open the Android/Gradle project exported from Unity. Switch to **Project** view mode. Using the provided files, place them in the following locations:

![](https://cdn-media.gzone.tech/media/tech/sdk/android/androidproject.png)

| FILE NAME | DIRECTORY/MODULE LOCATION |
| --- | --- |
| **...client.xml** | src/main/res/values *(this directory is automatically created and contains the `strings.xml` file)* |
| **google-services.json** | launcher *(or the module serving as the application)* |
| **SDKGame-v...-release.aar** | unityLibrary/libs *(or the module serving as a library)* |

### 2.2. Fill in resource information into the project

Switch to **Android** view mode and pay attention to the boxed areas to perform the following tasks.


![](https://cdn-media.gzone.tech/media/tech/sdk/android/androidproject2.png)


If you don't remember the Gradle version installed in Unity Editor, you can open the `gradle-wrapper.properties` file in Android Studio to check the Gradle version being used:

```properties
distributionUrl=https\://services.gradle.org/distributions/gradle-7.2-bin.zip
```

*(Assuming in the example above, the Gradle version being used is **7.2**)*

---

#### Step 1: In the **build.gradle** file (project level)

Add the plugin declarations as follows:

```gradle
plugins {
    id 'com.android.application' version 'x.x.x' apply false
    id 'com.android.library' version 'x.x.x' apply false
    id 'com.google.gms.google-services' version 'x.x.x' apply false
    id 'org.jetbrains.kotlin.android' version 'x.x.x' apply false
}

task clean(type: Delete) {
    delete rootProject.buildDir
}
```

> 👉 The `x.x.x` values above are just examples; you need to use the exact version numbers according to the table below:

| Gradle Version | 8 and above | 7.5.1 | 7.2 |
| --- | --- | --- | --- |
| `com.android.application` | `8.x.x` | `7.4.2` | `7.1.2` |
| `com.android.library` | `8.x.x` | `7.4.2` | `7.1.2` |
| `com.google.gms.google-services` | `4.4.3` | `4.4.3` | `4.3.15` |
| `org.jetbrains.kotlin.android` | `2.2.0` | `2.0.21` | `1.9.25` |

#### Step 2: In the **settings.gradle** file

Inside the `dependencyResolutionManagement{...}` block, add the repository declarations as follows:

```gradle
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.PREFER_SETTINGS)
    repositories {
        google()
        mavenCentral()
        flatDir {
            dirs "${project(':unityLibrary').projectDir}/libs"
        }
        maven { url 'https://sdk-download.airbridge.io/maven' }
    }
}
```

#### Step 3: In the **gradle.properties** file

Add the following configuration properties:

```properties
...
org.gradle.jvmargs=...
android.useAndroidX=true
android.enableJetifier=true
android.nonTransitiveRClass=true
...
```

#### Step 4: In the **build.gradle** file (**launcher** module or the module serving as the application)

Replace the line:

```gradle
apply plugin: 'com.android.application'
```

with the new declaration and add some other plugins as follows:

```gradle
plugins {
    id 'com.android.application' apply true
    id 'com.google.gms.google-services' apply true
    id 'com.google.firebase.crashlytics' version 'x.x.x' apply true
}

dependencies {
    implementation project(':unityLibrary')
}
...
```

> 👉  The `x.x.x` values above are just examples; you need to use the exact version numbers according to the table below:

| Gradle Version | Below 8 | 8 and above |
| --- | --- | --- |
| `com.google.firebase.crashlytics` | `2.9.9` | `3.0.6` |

#### Step 5: Inside the **defaultConfig{...}** block

Add the `multiDexEnabled` property as follows:

```gradle
compileSdkVersion 36

defaultConfig {
    minSdkVersion 24
    targetSdkVersion 36
    ...
    versionCode 1
    versionName '1.0.0'
    multiDexEnabled true
}
```

#### Step 6: In the **build.gradle** file (**unityLibrary** module or the module serving as a library)

Replace the line:

```gradle
apply plugin: 'com.android.library'
```

with the new declaration as follows:

```gradle
plugins {
    id 'com.android.library' apply true
}

dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
}
```

Inside the `dependencies{...}` block, import the SDK file using the `implementation` command. There are **2 methods** as follows:

**Method 1**: Specify the exact SDK file name (stable but must match the SDK file name placed in the project)

```gradle
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation(name: 'SDKGame-v...-release', ext: 'aar')
    ...
}
```

**Method 2**: If the `libs` directory contains only one SDK file with the `.aar` format, you can also use the `implementation fileTree(...)` command as follows:

```gradle
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.aar'])
    ...
}
```
This method will not require matching the file name every time the SDK is updated.

> ⚠️ Only choose **1 of the 2 methods**; do not use both methods at the same time to import the SDK file.

#### Step 7: In addition to the SDK file, import other libraries

```gradle
implementation(name: 'SDKGame-v...-release', ext: 'aar')

implementation platform('com.google.firebase:firebase-bom:33.16.0')
implementation 'com.google.firebase:firebase-messaging'
implementation 'com.google.firebase:firebase-crashlytics'
implementation 'com.google.firebase:firebase-analytics'
implementation 'com.google.android.gms:play-services-auth:21.4.0'
implementation 'com.google.android.gms:play-services-base:18.7.2'
implementation 'com.google.android.gms:play-services-ads-identifier:18.2.0'
implementation 'com.android.billingclient:billing:8.1.0'
implementation 'com.facebook.android:facebook-android-sdk:18.1.3'
implementation 'com.appsflyer:af-android-sdk:6.17.0'
implementation 'com.android.installreferrer:installreferrer:2.2'
implementation 'io.airbridge:sdk-android:4.7.0'
implementation 'com.squareup.retrofit2:retrofit:2.11.0'
implementation 'com.squareup.retrofit2:converter-gson:2.11.0'
implementation 'com.squareup.picasso:picasso:2.8'
implementation 'androidx.datastore:datastore-preferences-rxjava3:1.1.7'
implementation 'androidx.appcompat:appcompat:1.6.1'
implementation 'androidx.cardview:cardview:1.0.0'
implementation 'androidx.lifecycle:lifecycle-viewmodel:2.7.0'
implementation 'com.squareup.okhttp3:okhttp:4.12.0'
implementation 'com.squareup.okhttp3:logging-interceptor:4.12.0'
implementation 'androidx.constraintlayout:constraintlayout:2.1.4'
implementation 'com.google.android.material:material:1.12.0'
implementation 'com.google.android.play:review:2.0.2'
implementation 'com.journeyapps:zxing-android-embedded:4.3.0'
implementation 'androidx.core:core-splashscreen:1.0.1'
debugImplementation 'com.squareup.leakcanary:leakcanary-android:2.14'
implementation 'com.google.android.gms:play-services-ads:25.1.0'
implementation 'com.google.ads.mediation:facebook:6.21.0.0'
implementation 'com.google.ads.mediation:unity:4.16.5.0'
implementation 'com.google.ads.mediation:applovin:13.5.1.0'
implementation 'org.jetbrains.kotlin:kotlin-stdlib:x.x.x'
```

> 👉 The `x.x.x` value for the last library above is just an example; you need to use the exact version number according to the table below:

| Gradle Version | 8 and above | 7.5.1 | 7.2 |
| --- | --- | --- | --- |
| `org.jetbrains.kotlin:kotlin-stdlib` | `2.2.0` | `2.0.21` | `1.9.25` |

#### Step 8: In the **AndroidManifest.xml** file of the module containing the Activity (Usually the **unityLibrary** module or the module serving as a library)

In the main `<activity/>` tag (in this example, the `UnityPlayerActivity` class), add the following `<intent-filter/>` tags:

```xml
<activity
    android:name="com.unity3d.player.UnityPlayerActivity"
    ...>

    <intent-filter android:autoVerify="true">
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="http"/>
        <data android:host="@string/sdk_airbridge_deeplink1"/>
        <data android:scheme="https"/>
        <data android:host="@string/sdk_airbridge_deeplink1"/>
    </intent-filter>

    <intent-filter android:autoVerify="true">
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="http"/>
        <data android:host="@string/sdk_airbridge_deeplink2"/>
        <data android:scheme="https"/>
        <data android:host="@string/sdk_airbridge_deeplink2"/>
    </intent-filter>

    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="@string/sdk_airbridge_scheme" />
    </intent-filter>

    <intent-filter android:autoVerify="true">
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data
            android:host="@string/sdk_appsflyer_deeplink"
            android:scheme="https" />
    </intent-filter>

    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>

    <meta-data
        android:name="unityplayer.UnityActivity"
        android:value="true" />
    ...
</activity>
```

#### Step 9: Also in that same main `<activity/>` tag

Add the following attributes at the beginning of the tag (or if they already exist, modify them to the following values):

```xml
<activity
    android:name="com.unity3d.player.UnityPlayerActivity"
    ...
    android:launchMode="singleTop"
    android:hardwareAccelerated="true"
    android:theme="@style/sdk_theme"
    ...
>
```
---

### 2.3. Change the package name / Bundle ID

Do you remember the package name we mentioned at the beginning of this guide? The package name value in the project must be set to exactly match the package name defined in the google-services file for the application to function properly. Check your project and make the following modifications:

> 👉 If the package name is declared in the **AndroidManifest.xml** file, change it to the new package name as follows. If not present, you can skip this.

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    package="com.example.package.name"
    >
```

> 👉 Or if the package name is declared in the **build.gradle** file, change it to the new package name as follows. If not present, you can skip this.

```gradle
android {
    namespace 'com.example.package.name'
    compileSdk 36
    ...
    defaultConfig {
        ...
    }
}
```

*(Note: The package name in the example above is just for illustration. The package name value may vary as provided by our technical team.)*

Finally, after filling in all the content, click **Sync Project with Gradle Files** 🐘 to synchronize the project and import the SDK along with the new libraries.


![](https://cdn-media.gzone.tech/media/tech/sdk/android/gradle.png)


If you don't encounter any serious warnings after syncing the project, congratulations! 👏👏 You have completed importing the SDK resources.

Now let's start using the code snippets in our SDK below.

### 2.4. Add required source code in the Activity file

**Step 1:** Declare the SDK object:

```java
public vn.expub.sdk.SDKGame sdkgame;
```
**Step 2:** In the `onCreate()` override method, initialize the SDK and call the following method:

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    sdkgame = new vn.expub.sdk.SDKGame(this);  // Initialize the SDK before calling functional methods.
    sdkgame.checkNotifyPermission(this, new vn.expub.sdk.OnCloseListener() {
        @Override
        public void onCompleted() {
            // sdkgame.login();  // Do something.
        }
    });
    // The checkNotifyPermission() method above must be called before the super.onCreate() method and the setContentView() method.
    super.onCreate(...);
    setContentView(...);
}
```
This method takes 2 parameters:

| VARIABLE NAME | DATA TYPE | ROLE |
| --- | --- | --- |
| **this** | Activity | The Activity containing the currently called method. |
| **OnCloseListener()** | OnCloseListener | The listener interface class for the result returned after checking the app's notification permission. By default, when the user opens the app, a white background screen will appear to check notification permission. After this screen closes, the `onCompleted()` callback method will be executed. You can continue the processing flow to display the next parts of the game inside the `onCompleted()` method. For example, after the splash screen closes, you can continue to display the game resource loading screen. |

---

**Step 3:** In the `onRequestPermissionsResult()`, `onActivityResult()`, `onNewIntent()`, `onResume()`, `onPause()`, and `onDestroy()` override methods, add the following source code:

```java
@Override
public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
    super.onRequestPermissionsResult(requestCode, permissions, grantResults);
    sdkgame.onRequestPermissionsResult(requestCode, permissions, grantResults);
}

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    sdkgame.onActivityResult(requestCode, resultCode, data);
}

@Override
protected void onNewIntent(Intent intent) {
    super.onNewIntent(intent);
    setIntent(intent);
    sdkgame.onNewIntent(intent);
}

@Override
protected void onResume() {
    super.onResume();
    if (co.ab180.airbridge.Airbridge.handleDeeplink(getIntent(), uri -> {})) return;
    sdkgame.onResume(getIntent());
}

@Override
protected void onPause() {
    super.onPause();
    sdkgame.onPause();
}

@Override
protected void onDestroy() {
    super.onDestroy();
    sdkgame.onDestroy();
    sdkgame = null;
}
```
At this point, you have completed integrating the input resources for an Android application to function normally! 👏👏👏 However, to be able to test the payment feature, you will need to build an AAB file with fully integrated input resources so that we can upload it to Google Play and configure testing features (such as the in-game payment feature).

> 👉 After we finish configuring, you can test the payment feature directly on the APK file without needing to download it from Google Play.

If not submitted in a timely manner, the testing of the in-game payment feature may take longer to process, which could affect your game's launch date.

---

## 3. Signing the application for the AAB file

🔑 To ensure no impact on our Google developer account, please use the keystore file we provided along with the following information:

- Key alias: **is the name of the keystore file** *(for example, with the file `xxxkey.jks`, the key alias would be `xxxkey`)*
- Key store password / Key password: **123456**

![](https://cdn-media.gzone.tech/media/tech/sdk/android/sign.png)

---

## 4. Functional Methods Integration Guide

Compare with the Unity source code and call the corresponding methods for the features in the game as follows:

### 4.1. Game resource handling

🤔❓ **What are the resources required to build the game?**  
They are the core resources, used to construct the basic components in the game. Once these resources have been processed, they do not need to be processed again in subsequent game launches.

- When **starting** to extract the resources required to build the game:

```java
sdkgame.trackStartExtractOriginResources();
```

- When the resources required to build the game **have been fully extracted**:

```java
sdkgame.trackEndExtractOriginResources();
```

> **Note**: The resources required to build the game are **completely different** from the CDN resources on our side.

🤔❓ **What are CDN/hotfix resources?**  
They are resources fetched from a remote CDN server, serving as supplementary resources to change certain small components in the game.

- When **starting to download** CDN/hotfix resources:

```java
sdkgame.trackStartDownloadHotfixResources();
```

- When CDN/hotfix resources **have been fully downloaded**:

```java
sdkgame.trackEndDownloadHotfixResources();
```

- When **starting to extract** CDN/hotfix resources:

```java
sdkgame.trackStartExtractHotfixResources();
```

- When CDN/hotfix resources **have been fully extracted**:

```java
sdkgame.trackEndExtractHotfixResources();
```

---

### 4.2. Login

```java
vn.expub.sdk.OnLoginListener loginListener = new vn.expub.sdk.OnLoginListener() {
    @Override
    public void onSuccess(String accountID, String accessToken) {
        // Do something with accountId, accessToken.
    }
};
sdkgame.login(loginListener);
```

🤔❓ **When should this method be called?**  
Each time the game is opened and resources finish loading, or when the player performs an action to open the SDK's login interface.

This method takes 1 parameter:

| VARIABLE NAME | ROLE |
| --- | --- |
| **loginListener**<br/> (OnLoginListener) | The listener interface class for the login feature result. If login is successful, the `onSuccess()` callback method will be executed. Parameters such as `accountId` (account identifier) and `accessToken` (login token) will be returned to you. Please proceed with the next steps of the game to process these parameters accordingly (e.g., server-side authentication for the game). |

---

### 4.3. Logout / Account switching

```java
vn.expub.sdk.OnLogoutListener logoutListener = new vn.expub.sdk.OnLogoutListener() {
    @Override
    public void onSuccess() { /* Do something. */ }
};
sdkgame.logout(logoutListener);
```

🤔❓ **When should this method be called?**  
When the user performs an action to log out or switch accounts.

This method takes 1 parameter:

| VARIABLE NAME | ROLE |
| --- | --- |
| **logoutListener**<br/> (OnLogoutListener) | The listener interface class for the logout feature result. If logout is successful, the `onSuccess()` callback method will be executed. Please proceed with your next steps after successfully logging out or switching accounts (e.g., displaying the resource loading screen, server selection screen, or the game's login portal screen). |

---

### 4.4. Starting to enter the game

🤔❓ **When should this method be called?**  
When the player has successfully logged in, selected a server, and pressed the button to start entering the game. You need to check the following condition: if a character account already exists on the selected server, call this method once all information has been retrieved.

```java
String serverId;       // ID of the server
String serverName;     // Name of the server
String characterId;    // ID of the character
String characterName;  // Character name
String characterLevel; // Level of the character

sdkgame.trackEnterGame(serverId, serverName, characterId, characterName, characterLevel);
```
This method takes 5 parameters:

| VARIABLE NAME | DATA TYPE | ROLE |
| --- | --- | --- |
| **serverId** | String | ID of the server. |
| **serverName** | String | Name of the server. |
| **characterId** | String | ID of the character. |
| **characterName** | String | Character name. |
| **characterLevel** | String | Level of the character. |

**So what if no character account exists on the selected server?**  This means the player will need to set a character name to retrieve the information. In that case, you will need to call the method below.

---

### 4.5. Creating a new character

🤔❓ **When should this method be called?**  
In contrast to the starting to enter the game method above, this method is called in the other condition branch: If no character account exists yet. You will need to let the user enter a character name, and when the user presses a button to create the character, call this method once the character has been successfully created and all information has been retrieved.

```java
String serverId;      // ID of the server
String serverName;    // Name of the server
String characterId;   // ID of the character
String characterName; // Character name
String characterLevel; // Level of the character

sdkgame.trackCreateNewCharacter(serverId, serverName, characterId, characterName, characterLevel);
```
This method takes 5 parameters:

| VARIABLE NAME | DATA TYPE | ROLE |
| --- | --- | --- |
| **serverId** | String | ID of the server. |
| **serverName** | String | Name of the server. |
| **characterId** | String | ID of the character. |
| **characterName** | String | Character name. |
| **characterLevel** | String | Level of the character. |

---

### 4.6. First-time tutorial

🤔❓ **When should this method be called?**  
When the player completes the first-time tutorial in the game.

```java
String characterLevel; // Level of the character

sdkgame.trackUserFinishTutorial(characterLevel);
```
This method takes 1 parameter:

| VARIABLE NAME | DATA TYPE | ROLE |
| --- | --- | --- |
| **characterLevel** | String | Level of the character. |
---

### 4.7. Character level up, VIP level up

🤔❓ **When should this method be called?**  
When the player reaches a new character level or new VIP level. Depending on the timing of reaching the new level, you will call one of the two methods below:

```java
String characterLevel; // Level of the character
String vipLevel;       // VIP level of the character

// When the player reaches a new character level. Call the following method:
sdkgame.trackCharacterLevelUp(characterLevel);

// When the player reaches a new VIP level. Call the following method:
sdkgame.trackVIPLevelUp(vipLevel);
```
Each of the above methods takes one separate parameter:

| VARIABLE NAME | DATA TYPE | ROLE |
| --- | --- | --- |
| **characterLevel** | String | Level of the character. |
| **vipLevel** | String | VIP level of the character. |
---

### 4.8. In-game payment

🤔❓ **When should this method be called?**  
When the player taps on a payment package in the game.

```java
String orderId = "your_game_order_id";          // Transaction ID.
String productPackageId = "demo_inapp_id_1";    // Payment package ID.

vn.expub.sdk.OnPayListener payListener = new vn.expub.sdk.OnPayListener() {
    @Override
    public void onSuccess(String resultOrderId) { /* Do something with resultOrderId. */ }

    @Override
    public void onFailure() { /* Do something. */ }

    @Override
    public void onDismiss() { /* Do something. */ }
};
sdkgame.pay(payListener, orderId, productPackageId);
```
This method takes 3 parameters:

| VARIABLE NAME | DATA TYPE | ROLE |
| --- | --- | --- |
| **payListener** | OnPayListener | The listener interface class for the payment feature result. If the payment is successful, the `onSuccessful()` callback method will be executed. You will need to continue processing the transaction ID `resultOrderId` that we return to you in order to grant in-game items. |
| **orderId** | String | Transaction/invoice ID. If you have previously written a transaction creation API, and when our system communicates with your transaction creation API to process and return data to you, you will receive a response from that API — that is the transaction ID. You use this transaction ID to pass into the `orderId` parameter of the payment method above. |
| **productPackageId** | String | Payment package ID.|

> 👉 The list of payment package IDs is **the first column in the payment package information table** that we provided as shown in the example image below.

![](https://cdn-media.gzone.tech/media/tech/sdk/android/payment.png)

According to Google's official documentation, to test whether the **in-game payment** feature works properly, you first need to **build a test AAB file** and send it to us so we can upload it to Google. Only then can we configure the payment package information on Google and collect hash keys for payment verification.

Therefore, if you are only focusing on testing the payment feature, prioritize building the AAB file with the previously integrated input resources. We will support you in quickly testing the feature after the test build is successfully uploaded to Google. Other features can be prioritized later for subsequent updates, as long as you ensure those features work stably before the game's launch.

---

### 4.9. First-time payment

```java
sdkgame.trackFirstRecharge();
```

🤔❓ **When should this method be called?**  
When the user makes their first successful payment in the game.

---

### 4.10. Displaying advertisements

🤔❓ **When should this method be called?**  
When the user taps the button to watch an advertisement.

```java
String adsType = "ad_type_1";        // Type of advertisement to display.
String adsTransId = "ABC123456xyz";  // Transaction ID.

vn.expub.sdk.OnAdsListener adsListener = new vn.expub.sdk.OnAdsListener() {
    @Override
    public void onSuccess() { // Do something. }

    @Override
    public void onFailure() { // Do something. }
};

sdkgame.showAds(adsType, adsTransId, adsListener);
```
This method takes 3 parameters:

| VARIABLE NAME | DATA TYPE | ROLE |
| --- | --- | --- |
| **adsType** | String | The type of advertisement to be displayed. |
| **adsTransId** | String | Advertisement transaction ID for verification. |
| **adsListener** | OnAdsListener | The listener interface class for the advertisement display feature result. If the advertisement is successfully watched to completion, the `onSuccess()` callback method will be executed. If watching the advertisement encounters an error, the `onFailure()` callback method will be executed. |

---

## 5. Version change and AAB optimization

Before building a new APK or AAB file to upload to Google Play, you must change the version code and version name values in the **build.gradle** file as follows.

```gradle
defaultConfig {
    ...
    versionCode 2       // The new version code must always be greater than the old version code (e.g., the old version code is 1).
    versionName "1.0.1" // You can increase, decrease, or keep this value the same between versions.
    ...
}
```

According to Google Play's official documentation, the maximum compressed download size for the base module created through an AAB file is **200MB**. Therefore, if the application size exceeds 200MB, you need to integrate **Play Asset Delivery**. This service allows games larger than 200MB to replace old expansion files (OBB) by publishing a single software artifact to Google Play.

**Official reference documentation links:**

- [https://docs.unity3d.com/6000.0/Documentation/Manual/play-asset-delivery.html](https://docs.unity3d.com/6000.0/Documentation/Manual/play-asset-delivery.html)
- [https://developer.android.com/guide/playcore/asset-delivery](https://developer.android.com/guide/playcore/asset-delivery)
