# HƯỚNG DẪN TÍCH HỢP SDK ANDROID (3.7.7)

👋 Xin chào! Đây là tài liệu hướng dẫn tích hợp SDK dành riêng cho Android được cung cấp bởi nhóm kỹ thuật. Chúng tôi sẽ cung cấp cho các bạn đầy đủ thông tin bao gồm:

- Điều kiện tích hợp.
- Hướng dẫn chi tiết tích hợp tài nguyên đầu vào của bộ SDK.
- Hướng dẫn chi tiết tích hợp các hàm chức năng có trong bộ SDK. *(Phần này sẽ cần phải kết hợp thêm các tài liệu kỹ thuật có liên quan khác từ chúng tôi để có thể hiểu rõ hơn và tích hợp chính xác.)*

💬 Nếu bạn có bất kỳ câu hỏi nào về nội dung liên quan, vui lòng liên hệ với nhóm kỹ thuật của chúng tôi để được hỗ trợ giải quyết vấn đề.

## 1. Điều kiện tích hợp

Yêu cầu về phiên bản Unity: từ phiên bản **2022.2.0a18** trở lên.

Yêu cầu về cấp độ SDK trong dự án:

- **Cấp độ SDK tối thiểu / Minimum SDK**: 24
- **Cấp độ SDK mục tiêu / Target SDK**: Từ 36 trở lên.

---

## 2. Hướng dẫn tích hợp tài nguyên đầu vào

📁 Chúng tôi đã đặt tên tệp của bộ SDK kèm theo tên gói/bundle ID **nằm ở bên trong cặp dấu ngoặc**. *(Tên gói của ảnh bên dưới chỉ là ví dụ minh họa, giá trị của tên gói có thể thay đổi từ nhóm kỹ thuật.)*

![](https://cdn-media.gzone.tech/media/tech/sdk/android/packagename.png)

> 👉 Đây sẽ là tên gói chính thức hoạt động xuyên suốt dự án. Vui lòng ghi nhớ thông tin này để có thể cấu hình chính xác thông tin dự án.

Bạn giải nén tệp này và mở ra. Bên trong sẽ chứa các tệp tài nguyên để tích hợp vào dự án:

| TÊN TỆP | TÊN GỌI TẮT | CHỨC NĂNG |
| --- | --- | --- |
| **SDKGame-v…-release.aar**<br> *(Giá trị ba chấm ... có thể bị thay đổi từ nhóm kỹ thuật, giá trị đó thường sẽ là phiên bản.)* | Tệp **SDK** | 📦 Tệp SDK chính của nhóm kỹ thuật, chứa các hàm chức năng đã được viết sẵn. |
| **google-services.json** | Tệp **google** hoặc **google-services** | ⚙️ Tệp cấu hình dịch vụ của Google ứng với tên gói mà chúng tôi yêu cầu. |
| **…client.xml**<br> *(Giá trị ba chấm ... có thể bị thay đổi từ nhóm kỹ thuật, thường sẽ là mã dự án.)* | Tệp **client** hoặc **cổng client** | 🧠 Tệp thông tin đầu vào của trò chơi giúp cho SDK có thể hoạt động chính xác. |
| **…key.jks**<br> *(Giá trị ba chấm ... có thể bị thay đổi từ nhóm kỹ thuật, thường sẽ là mã dự án.)* | Tệp **kho khóa** hoặc **keystore** | 🔑 Tệp kho khóa lưu trữ dùng để ký ứng dụng trước khi xuất bản, và xác minh ứng dụng khi gửi lên Google Play. |

### 2.1. Dán các tệp tài nguyên vào dự án Android

Trong Android Studio, mở dự án Android/Gradle đã được xuất từ Unity. Đổi sang chế độ hiển thị dạng **Project**. Sử dụng các tệp đã được cung cấp, đặt vào các vị trí như sau:

![](https://cdn-media.gzone.tech/media/tech/sdk/android/androidproject.png)

| TÊN TỆP | VỊ TRÍ THƯ MỤC/MODULE ĐƯỢC ĐẶT VÀO |
| --- | --- |
| **...client.xml** | src/main/res/values *(thư mục này được tự động tạo ra, bên trong chứa tệp `strings.xml`)* |
| **google-services.json** | launcher *(hoặc module có vai trò ứng dụng/application)* |
| **SDKGame-v...-release.aar** | unityLibrary/libs *(hoặc module có vai trò thư viện/library)* |

### 2.2. Điền thông tin các tài nguyên vào dự án

Đổi sang chế độ hiển thị dạng **Android**, chú ý các vùng khoanh vuông để thực hiện các công việc dưới đây.


![](https://cdn-media.gzone.tech/media/tech/sdk/android/androidproject2.png)


Nếu như không nhớ phiên bản Gradle đã cài đặt ở Unity Editor, có thể mở tệp `gradle-wrapper.properties` trong Android Studio để kiểm tra phiên bản Gradle đang sử dụng:

```properties
distributionUrl=https\://services.gradle.org/distributions/gradle-7.2-bin.zip
```

*(Giả sử ở ví dụ trên, phiên bản Gradle đang sử dụng là **7.2**)*

---

#### Bước 1: Tại tệp **build.gradle** (cấp độ project)

Thêm dòng khai báo plugin như sau:

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

> 👉 Giá trị `x.x.x` ở trên chỉ là ví dụ, cần lấy số phiên bản chính xác theo bảng dưới đây:

| Số phiên bản Gradle | Từ 8 trở lên | 7.5.1 | 7.2 |
| --- | --- | --- | --- |
| `com.android.application` | `8.x.x` | `7.4.2` | `7.1.2` |
| `com.android.library` | `8.x.x` | `7.4.2` | `7.1.2` |
| `com.google.gms.google-services` | `4.4.3` | `4.4.3` | `4.3.15` |
| `org.jetbrains.kotlin.android` | `2.2.0` | `2.0.21` | `1.9.25` |

#### Bước 2: Tại tệp **settings.gradle**

Trong khối `dependencyResolutionManagement{...}`, thêm dòng khai báo kho lưu trữ như sau:

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

#### Bước 3: Tại tệp **gradle.properties**

Thêm một số thông tin cấu hình sau:

```properties
...
org.gradle.jvmargs=...
android.useAndroidX=true
android.enableJetifier=true
android.nonTransitiveRClass=true
...
```

#### Bước 4: Tại tệp **build.gradle** (module **launcher** hoặc module có vai trò ứng dụng)

Thay dòng:

```gradle
apply plugin: 'com.android.application'
```

bằng cách khai báo mới và thêm một số plugin khác như sau:

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

> 👉  Giá trị `x.x.x` ở trên chỉ là ví dụ, cần lấy số phiên bản chính xác theo bảng dưới đây:

| Số phiên bản Gradle | Nhỏ hơn 8 | Từ 8 trở lên |
| --- | --- | --- |
| `com.google.firebase.crashlytics` | `2.9.9` | `3.0.6` |

#### Bước 5: Trong khối **defaultConfig{...}**

Thêm thuộc tính `multiDexEnabled` như sau:

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

#### Bước 6: Tại tệp **build.gradle** (module **unityLibrary** hoặc module có vai trò thư viện)

Thay dòng:

```gradle
apply plugin: 'com.android.library'
```

bằng cách khai báo mới như sau:

```gradle
plugins {
    id 'com.android.library' apply true
}

dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
}
```

Trong khối `dependencies{...}`, nhập thêm tệp SDK bằng lệnh `implementation`. Có **2 cách** như sau:

**Cách 1**: Chỉ định chính xác tên tệp SDK (ổn định nhưng phải đồng nhất với tên tệp SDK đã đặt vào dự án)

```gradle
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation(name: 'SDKGame-v...-release', ext: 'aar')
    ...
}
```

**Cách 2**: Nếu trong thư mục `libs` chỉ có duy nhất 1 tệp SDK có định dạng `.aar` thì cũng có thể sử dụng lệnh `implementation fileTree(...)` như sau:

```gradle
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.aar'])
    ...
}
```
Cách này sẽ không cần phải đồng nhất với tên tệp mỗi khi cập nhật SDK.

> ⚠️ Chỉ chọn **1 trong 2 cách**, không được sử dụng cả 2 cách cùng một lúc để nhập tệp SDK.

#### Bước 7: Bên cạnh tệp SDK, nhập thêm các thư viện khác

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

> 👉 Giá trị `x.x.x` ở thư viện cuối chỉ là ví dụ, cần lấy số phiên bản chính xác theo bảng dưới đây:

| Số phiên bản Gradle | Từ 8 trở lên | 7.5.1 | 7.2 |
| --- | --- | --- | --- |
| `org.jetbrains.kotlin:kotlin-stdlib` | `2.2.0` | `2.0.21` | `1.9.25` |

#### Bước 8: Tại tệp **AndroidManifest.xml** của module chứa Activity (Thường là module **unityLibrary** hoặc module có vai trò thư viện)

Ở thẻ `<activity/>` chính (ví dụ ở đây là lớp `UnityPlayerActivity`), thêm một số thẻ `<intent-filter/>` sau:

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

#### Bước 9: Cũng ở thẻ `<activity/>` chính đó

Thêm các thuộc tính sau ở phần đầu của thẻ (hoặc nếu đã có thì chỉnh sửa lại thành giá trị như sau):

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

### 2.3. Sửa tên gói / Bundle ID

Bạn có nhớ tên gói mà chúng tôi đã yêu cầu ở đầu tài liệu hướng dẫn không? Giá trị của tên gói trong dự án cần được thiết lập chính xác tương ứng với tên gói được định nghĩa trong tệp google-services thì ứng dụng mới có thể hoạt động. Kiểm tra dự án của bạn và thực hiện sửa đổi như sau:

> 👉 Nếu tên gói được khai báo trong tệp **AndroidManifest.xml**, hãy sửa lại thành tên gói mới như sau. Nếu không có thì có thể bỏ qua.

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    package="com.example.package.name"
    >
```

> 👉 Hoặc nếu tên gói được khai báo trong tệp **build.gradle**, hãy sửa lại thành tên gói mới như sau. Nếu không có thì có thể bỏ qua.

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

*(Lưu ý: Tên gói ở ví dụ trên chỉ là ví dụ minh họa. Giá trị tên gói có thể thay đổi từ nhóm kỹ thuật.)*

Cuối cùng, sau khi đã điền đủ nội dung, ấn **Sync Project with Gradle Files** 🐘 để đồng bộ dự án và nhập SDK cùng các thư viện mới.


![](https://cdn-media.gzone.tech/media/tech/sdk/android/gradle.png)


Nếu như bạn không gặp cảnh báo nghiêm trọng gì sau khi đồng bộ dự án thì xin chúc mừng! 👏👏 Bạn đã hoàn thành việc nhập tài nguyên SDK.

Giờ thì hãy bắt đầu sử dụng các đoạn mã nguồn có trong SDK của chúng tôi dưới đây.

### 2.4. Thêm mã nguồn cần thiết trong tệp Activity

**Bước 1:** Khai báo đối tượng SDK:

```java
public vn.expub.sdk.SDKGame sdkgame;
```
**Bước 2:** Trong hàm ghi đè `onCreate()`, bạn hãy khởi tạo SDK và gọi hàm sau:

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    sdkgame = new vn.expub.sdk.SDKGame(this);  // Khởi tạo SDK trước khi gọi các hàm chức năng.
    sdkgame.checkNotifyPermission(this, new vn.expub.sdk.OnCloseListener() {
        @Override
        public void onCompleted() {
            // sdkgame.login();  // Làm gì đó.
        }
    });
    // Hàm checkNotifyPermission() ở trên phải được gọi trước hàm super.onCreate() và hàm setContentView().
    super.onCreate(...);
    setContentView(...);
}
```
Trong hàm này có truyền 2 tham số:

| TÊN BIẾN | KIỂU DỮ LIỆU | VAI TRÒ |
| --- | --- | --- |
| **this** | Activity | Activity chứa hàm được gọi hiện tại. |
| **OnCloseListener()** | OnCloseListener | Lớp giao diện lắng nghe kết quả trả về sau khi kiểm tra xong quyền thông báo của ứng dụng. Mặc định, khi người dùng mở ứng dụng, một giao diện màn hình có nền màu trắng sẽ xuất hiện để kiểm tra quyền thông báo. Sau khi giao diện màn hình này đóng lại, hàm gọi lại `onCompleted()` sẽ được thực thi. Bạn có thể tiếp tục luồng xử lý hiển thị những phần tiếp theo của trò chơi trong hàm `onCompleted()`. Ví dụ như sau khi giao diện màn hình khởi động đóng lại, bạn có thể tiếp tục cho hiển thị giao diện tải tài nguyên của trò chơi. |

---

**Bước 3:** Tại hàm ghi đè `onRequestPermissionsResult()`, `onActivityResult()`, `onNewIntent()`, `onResume()`, `onPause()` và `onDestroy()`, thêm đoạn mã nguồn sau:

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
Đến đây là bạn đã hoàn thành việc tích hợp tài nguyên đầu vào để một ứng dụng Android có thể hoạt động bình thường rồi! 👏👏👏 Tuy nhiên, để có thể nghiệm thu tính năng nạp tiền, bạn sẽ cần xuất một tệp AAB đã tích hợp tài nguyên đầu vào đầy đủ, để chúng tôi có thể tải lên Google Play và cấu hình các tính năng thử nghiệm (ví dụ tính năng nạp tiền trong trò chơi).

> 👉 Sau khi chúng tôi cấu hình xong, bạn có thể nghiệm thu tính năng nạp tiền ngay trên tệp APK mà không cần phải tải về từ Google Play.

Nếu không gửi kịp thời, việc nghiệm thu tính năng nạp tiền trong trò chơi của bạn có thể bị kéo dài thời gian xử lý hơn, từ đó ảnh hưởng đến thời điểm ra mắt trò chơi.

---

## 3. Ký ứng dụng cho tệp AAB

🔑 Để đảm bảo không ảnh hưởng đến tài khoản nhà phát triển Google của chúng tôi, vui lòng sử dụng tệp keystore mà chúng tôi đã cung cấp cùng với các thông tin như sau:

- Key alias: **chính là tên của tệp keystore** *(ví dụ với tệp `xxxkey.jks` thì sẽ có key alias là `xxxkey`)*
- Key store password / Key password: **123456**

![](https://cdn-media.gzone.tech/media/tech/sdk/android/sign.png)

---

## 4. Hướng dẫn tích hợp các hàm chức năng

Đối chiếu với mã nguồn Unity và gọi các hàm tương ứng với các chức năng có trong trò chơi như sau:

### 4.1. Xử lý nguồn tài nguyên trò chơi

🤔❓ **Nguồn tài nguyên cần thiết để cấu thành trò chơi là gì?**  
Là nguồn tài nguyên gốc, đây là tài nguyên để xây dựng nên các thành phần cơ bản trong trò chơi. Một khi đã xử lý xong tài nguyên này thì sẽ không cần phải xử lý lại ở các lần mở trò chơi về sau nữa.

- Khi **bắt đầu** giải nén nguồn tài nguyên cần thiết để cấu thành trò chơi:

```java
sdkgame.trackStartExtractOriginResources();
```

- Khi **đã giải nén xong** nguồn tài nguyên cần thiết để cấu thành trò chơi:

```java
sdkgame.trackEndExtractOriginResources();
```

> **Lưu ý**: Nguồn tài nguyên cần thiết để cấu thành trò chơi **khác hoàn toàn** với nguồn tài nguyên qua CDN ở phía chúng tôi.

🤔❓ **Nguồn tài nguyên qua CDN/hotfix là gì?**  
Là nguồn tài nguyên được lấy từ máy chủ CDN từ xa, đây là tài nguyên bổ sung nhằm thay đổi một số thành phần nhỏ trong trò chơi.

- Khi **bắt đầu tải** tài nguyên qua CDN/hotfix:

```java
sdkgame.trackStartDownloadHotfixResources();
```

- Khi **đã tải xong** tài nguyên qua CDN/hotfix:

```java
sdkgame.trackEndDownloadHotfixResources();
```

- Khi **bắt đầu giải nén** tài nguyên qua CDN/hotfix:

```java
sdkgame.trackStartExtractHotfixResources();
```

- Khi **đã giải nén xong** tài nguyên qua CDN/hotfix:

```java
sdkgame.trackEndExtractHotfixResources();
```

---

### 4.2. Đăng nhập

```java
vn.expub.sdk.OnLoginListener loginListener = new vn.expub.sdk.OnLoginListener() {
    @Override
    public void onSuccess(String accountID, String accessToken) {
        // Làm gì đó với accountId, accessToken.
    }
};
sdkgame.login(loginListener);
```

🤔❓ **Khi nào gọi hàm này?**  
Mỗi khi mở trò chơi và tải xong tài nguyên, hoặc khi người chơi thực hiện hành động nào đó để mở giao diện đăng nhập của SDK.

Trong hàm này có truyền 1 tham số:

| TÊN BIẾN | VAI TRÒ |
| --- | --- |
| **loginListener**<br/> (OnLoginListener) | Lớp giao diện lắng nghe kết quả trả về của tính năng đăng nhập. Nếu đăng nhập thành công, hàm gọi lại `onSuccess()` sẽ được thực thi. Các tham số như `accountId` (định danh tài khoản) và `accessToken` (token đăng nhập) sẽ được trả về cho bạn. Vui lòng thực hiện các công việc tiếp theo của trò chơi để xử lý các tham số này cho phù hợp (ví dụ như xác thực phía máy chủ của trò chơi). |

---

### 4.3. Đăng xuất / Đổi tài khoản

```java
vn.expub.sdk.OnLogoutListener logoutListener = new vn.expub.sdk.OnLogoutListener() {
    @Override
    public void onSuccess() { /* Làm gì đó. */ }
};
sdkgame.logout(logoutListener);
```

🤔❓ **Khi nào gọi hàm này?**  
Khi người dùng thực hiện hành động nào đó để đăng xuất hoặc đổi tài khoản.

Trong hàm này có truyền 1 tham số:

| TÊN BIẾN | VAI TRÒ |
| --- | --- |
| **logoutListener**<br/> (OnLogoutListener) | Lớp giao diện lắng nghe kết quả trả về của tính năng đăng xuất. Nếu đăng xuất thành công, hàm gọi lại `onSuccess()` sẽ được thực thi. Vui lòng thực hiện các công việc tiếp theo của bạn sau khi đăng xuất hoặc đổi tài khoản thành công (ví dụ hiển thị giao diện tải tài nguyên, giao diện chọn máy chủ, hoặc giao diện cổng đăng nhập của trò chơi). |

---

### 4.4. Bắt đầu vào trò chơi

🤔❓ **Khi nào gọi hàm này?**  
Khi người chơi đã đăng nhập thành công, chọn một máy chủ nào đó, và bấm vào nút để bắt đầu vào trò chơi. Bạn cần kiểm tra điều kiện sau: nếu như đã có tài khoản nhân vật tồn tại trong máy chủ đã chọn, thì hãy gọi đến hàm này nếu đã lấy đầy đủ thông tin.

```java
String serverId;       // ID của máy chủ
String serverName;     // Tên của máy chủ
String characterId;    // ID của nhân vật
String characterName;  // Tên nhân vật
String characterLevel; // Cấp độ của nhân vật

sdkgame.trackEnterGame(serverId, serverName, characterId, characterName, characterLevel);
```
Trong hàm này có truyền 5 tham số:

| TÊN BIẾN | KIỂU DỮ LIỆU | VAI TRÒ |
| --- | --- | --- |
| **serverId** | String | ID của máy chủ. |
| **serverName** | String | Tên của máy chủ. |
| **characterId** | String | ID của nhân vật. |
| **characterName** | String | Tên nhân vật. |
| **characterLevel** | String | Cấp độ của nhân vật. |

**Vậy nếu như chưa có tài khoản nhân vật tồn tại trong máy chủ đã chọn thì sao?**  Tức có nghĩa là người chơi sẽ cần phải đặt tên nhân vật để lấy thông tin. Khi đó bạn sẽ cần phải gọi hàm bên dưới.

---

### 4.5. Tạo nhân vật mới

🤔❓ **Khi nào gọi hàm này?**  
Trái ngược lại với hàm bắt đầu vào trò chơi ở trên, hàm này sẽ được gọi ở nhánh điều kiện còn lại: Nếu chưa có tài khoản nhân vật tồn tại. Bạn sẽ cần để người dùng nhập tên nhân vật, và khi người dùng ấn vào nút nào đó để tạo nhân vật, thì hãy gọi đến hàm này nếu như đã tạo thành công và lấy đầy đủ thông tin.

```java
String serverId;      // ID của máy chủ
String serverName;    // Tên của máy chủ
String characterId;   // ID của nhân vật
String characterName; // Tên nhân vật
String characterLevel; // Cấp độ của nhân vật

sdkgame.trackCreateNewCharacter(serverId, serverName, characterId, characterName, characterLevel);
```
Trong hàm này có truyền 5 tham số:

| TÊN BIẾN | KIỂU DỮ LIỆU | VAI TRÒ |
| --- | --- | --- |
| **serverId** | String | ID của máy chủ. |
| **serverName** | String | Tên của máy chủ. |
| **characterId** | String | ID của nhân vật. |
| **characterName** | String | Tên nhân vật. |
| **characterLevel** | String | Cấp độ của nhân vật. |

---

### 4.6. Hướng dẫn tân thủ lần đầu tiên

🤔❓ **Khi nào gọi hàm này?**  
Khi người chơi hoàn thành hướng dẫn tân thủ lần đầu tiên trong trò chơi.

```java
String characterLevel; // Cấp độ của nhân vật

sdkgame.trackUserFinishTutorial(characterLevel);
```
Trong hàm này có truyền 1 tham số:

| TÊN BIẾN | KIỂU DỮ LIỆU | VAI TRÒ |
| --- | --- | --- |
| **characterLevel** | String | Cấp độ của nhân vật. |
---

### 4.7. Tăng cấp độ nhân vật, cấp độ VIP

🤔❓ **Khi nào gọi hàm này?**  
Khi người chơi đạt cấp độ nhân vật mới, cấp độ VIP mới. Tùy vào thời điểm đạt cấp độ mới mà bạn sẽ gọi một trong hai hàm sau:

```java
String characterLevel; // Cấp độ của nhân vật
String vipLevel;       // Cấp độ VIP của nhân vật

// Khi người chơi đạt cấp độ nhân vật mới. Hãy gọi hàm sau:
sdkgame.trackCharacterLevelUp(characterLevel);

// Khi người chơi đạt cấp độ VIP mới. Hãy gọi hàm sau:
sdkgame.trackVIPLevelUp(vipLevel);
```
Trong mỗi hàm trên có truyền một tham số riêng biệt:

| TÊN BIẾN | KIỂU DỮ LIỆU | VAI TRÒ |
| --- | --- | --- |
| **characterLevel** | String | Cấp độ của nhân vật. |
| **vipLevel** | String | Cấp độ VIP của nhân vật. |
---

### 4.8. Nạp tiền trong trò chơi

🤔❓ **Khi nào gọi hàm này?**  
Khi người chơi ấn vào một gói nạp nào đó trong trò chơi.

```java
String orderId = "your_game_order_id";          // Mã giao dịch.
String productPackageId = "demo_inapp_id_1";    // Mã gói nạp.

vn.expub.sdk.OnPayListener payListener = new vn.expub.sdk.OnPayListener() {
    @Override
    public void onSuccess(String resultOrderId) { /* Làm gì đó với resultOrderId. */ }

    @Override
    public void onFailure() { /* Làm gì đó. */ }

    @Override
    public void onDismiss() { /* Làm gì đó. */ }
};
sdkgame.pay(payListener, orderId, productPackageId);
```
Trong hàm này có truyền 3 tham số:

| TÊN BIẾN | KIỂU DỮ LIỆU | VAI TRÒ |
| --- | --- | --- |
| **payListener** | OnPayListener | Lớp giao diện lắng nghe kết quả trả về của tính năng nạp tiền. Nếu nạp tiền thành công, hàm gọi lại `onSuccessful()` sẽ được thực thi. Bạn sẽ cần tiếp tục xử lý mã giao dịch `resultOrderId` mà chúng tôi đã trả về để thực hiện cộng vật phẩm trong trò chơi. |
| **orderId** | String | Mã giao dịch/hóa đơn. Nếu trước đó bạn đã viết API tạo giao dịch, và khi hệ thống phía chúng tôi giao tiếp với API tạo giao dịch của bạn để xử lý và trả về cho bạn, bạn sẽ nhận được một dữ liệu trả về từ API đấy, đó chính là mã giao dịch. Bạn sử dụng mã giao dịch này để tiếp tục truyền vào tham số `orderId` của hàm nạp tiền ở trên. |
| **productPackageId** | String | Mã gói nạp.|

> 👉 Danh sách mã gói nạp chính là **cột đầu tiên trong bảng thông tin gói nạp** mà chúng tôi đã cung cấp như ảnh mình họa bên dưới.

![](https://cdn-media.gzone.tech/media/tech/sdk/android/payment.png)

Theo tài liệu chính thức của Google, để có thể kiểm tra tính năng **nạp tiền trong trò chơi** có thành công hay không, trước tiên bạn cần phải **xuất bản một tệp AAB thử nghiệm** và gửi cho chúng tôi để chúng tôi có thể gửi lên Google. Khi đó chúng tôi mới có thể cấu hình thông tin các gói nạp trên Google, thu thập khóa băm để xác thực thanh toán.

Vậy nên nếu bạn chỉ nhắm vào việc kiểm tra tính năng nạp tiền, hãy ưu tiên xuất bản tệp AAB đã tích hợp tài nguyên đầu vào trước đó. Chúng tôi sẽ hỗ trợ các bạn kiểm tra tính năng nhanh chóng sau khi bản dựng thử nghiệm được gửi lên Google thành công. Các tính năng khác có thể ưu tiên sau cho các bản cập nhật sau, miễn là các bạn đảm bảo các tính năng hoạt động ổn định trước khi ra mắt trò chơi.

---

### 4.9. Nạp tiền lần đầu tiên

```java
sdkgame.trackFirstRecharge();
```

🤔❓ **Khi nào gọi hàm này?**  
Khi người dùng nạp tiền lần đầu tiên thành công bên trong trò chơi.

---

### 4.10. Hiển thị quảng cáo

🤔❓ **Khi nào gọi hàm này?**  
Khi người dùng bấm vào nút xem quảng cáo.

```java
String adsType = "ad_type_1";        // Kiểu quảng cáo hiển thị.
String adsTransId = "ABC123456xyz";  // Mã giao dịch.

vn.expub.sdk.OnAdsListener adsListener = new vn.expub.sdk.OnAdsListener() {
    @Override
    public void onSuccess() { // Làm gì đó. }

    @Override
    public void onFailure() { // Làm gì đó. }
};

sdkgame.showAds(adsType, adsTransId, adsListener);
```
Trong hàm này có truyền 3 tham số:

| TÊN BIẾN | KIỂU DỮ LIỆU | VAI TRÒ |
| --- | --- | --- |
| **adsType** | String | Kiểu quảng cáo sẽ cần được hiển thị. |
| **adsTransId** | String | Mã giao dịch quảng cáo để xác thực. |
| **adsListener** | OnAdsListener | Lớp giao diện lắng nghe kết quả trả về của tính năng hiển thị quảng cáo. Nếu xem hết quảng cáo thành công, hàm gọi lại `onSuccess()` sẽ được thực thi. Nếu xem quảng cáo bị lỗi, hàm gọi lại `onFailure()` sẽ được thực thi. |

---

## 5. Thay đổi phiên bản và tối ưu tệp AAB

Trước khi xuất tệp APK hoặc AAB mới để tải lên Google Play, bạn phải thay đổi giá trị của mã phiên bản và tên phiên bản trong tệp **build.gradle** như sau.

```gradle
defaultConfig {
    ...
    versionCode 2       // Mã phiên bản mới phải luôn lớn hơn mã phiên bản cũ (ví dụ mã phiên bản cũ là 1).
    versionName "1.0.1" // Bạn có thể tăng, giảm hoặc giữ nguyên giá trị này giữa các phiên bản.
    ...
}
```

Theo tài liệu chính thức của Google Play, kích thước tải xuống tối đa được nén cho module cơ sở tạo qua tệp AAB là **200MB**. Vậy nên, nếu kích thước của ứng dụng vượt quá 200MB, bạn cần tích hợp thêm **Play Asset Delivery**. Dịch vụ này cho phép trò chơi có kích thước lớn hơn 200MB thay thế các tệp mở rộng cũ (OBB) bằng cách xuất bản một cấu phần phần mềm duy nhất lên Google Play.

**Đường dẫn tài liệu tham khảo chính thức:**

- [https://docs.unity3d.com/6000.0/Documentation/Manual/play-asset-delivery.html](https://docs.unity3d.com/6000.0/Documentation/Manual/play-asset-delivery.html)
- [https://developer.android.com/guide/playcore/asset-delivery](https://developer.android.com/guide/playcore/asset-delivery)
