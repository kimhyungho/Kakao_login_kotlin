Kakao login Android v2
============
How to login/sign up to Kakao Account

Official Homepage
-----------
https://developers.kakao.com/

https://developers.kakao.com/docs/latest/ko/kakaologin/android

Version
-----------
 Kakao SDK for Android v2

사용법
-----------
* 프로젝트 수준의 build.gradle
```
allprojects {
    repositories {
        google()
        jcenter()
        maven { url 'https://devrepo.kakao.com/nexus/content/groups/public/' }
    }
}
```
* 앱 수준의 build.gradle
```
android {
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }

    kotlinOptions {
        jvmTarget = "1.8"
    }
}

dependencies {
    implementation "com.kakao.sdk:v2-user:2.0.0"
}
```
* GlobalApplication   (class)
```
import android.app.Application
import com.kakao.sdk.common.KakaoSdk

class GlobalApplication : Application() {
    override fun onCreate() {
        super.onCreate()
        // 다른 초기화 코드들

        // Kakao SDK 초기화
        KakaoSdk.init(this, "네이티브앱키")
    }
}
```
* AndroidManifest.xml
```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="">

    <uses-permission android:name="android.permission.INTERNET" />

    <application
        android:name=".GlobalApplication"
        android:allowBackup="true"
        .
        .
        .       
        <activity android:name="com.kakao.sdk.auth.AuthCodeHandlerActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />

            <!-- Redirect URI: "kakao{NATIVE_APP_KEY}://oauth“ -->
            <data android:host="oauth"
                android:scheme="kakao네이티브앱키" />
        </intent-filter>
    </activity>
    </application>

</manifest>
```
* LoginActivity
```
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.util.Log
import com.kakao.sdk.auth.LoginClient
import com.kakao.sdk.auth.model.OAuthToken
import kotlinx.android.synthetic.main.activity_main.*

class LoginActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_login)

        button.setOnClickListener {
            // 로그인 조합 예제

// 로그인 공통 callback 구성
            val callback: (OAuthToken?, Throwable?) -> Unit = { token, error ->
                if (error != null) {
                    Log.e("TAG", "로그인 실패", error)
                }
                else if (token != null) {
                    Log.i("TAG", "로그인 성공 ${token.accessToken}")
                }
            }

// 카카오톡이 설치되어 있으면 카카오톡으로 로그인, 아니면 카카오계정으로 로그인
            if (LoginClient.instance.isKakaoTalkLoginAvailable(this)) {
                LoginClient.instance.loginWithKakaoTalk(this, callback = callback)
            } else {
                LoginClient.instance.loginWithKakaoAccount(this, callback = callback)
            }
        }
    }
}
```
주의사항
-----------
공식 홈페이지에 나와있는 {NATIVE_APP_KEY}(중괄호 포함)을 native app key로 바꾸셔야합니다.

ex) 네이티브 앱키가 n1a2t3iv4e5a6p7p8ke9y라면

manifest
```
        <data android:host="oauth"
                android:scheme="kakaon1a2t3iv4e5a6p7p8ke9y" />
```
GlobalApplication
```
         KakaoSdk.init(this, "n1a2t3iv4e5a6p7p8ke9y")
```
로 입력해 주셔야 합니다.

ScreenShots
-----------
<div>
<img whdth = "50" src = "https://github.com/kimhyungho/kakaologin/blob/master/image/1.JPG">
<img whdth = "50" src = "https://github.com/kimhyungho/kakaologin/blob/master/image/2.JPG">
<div>
