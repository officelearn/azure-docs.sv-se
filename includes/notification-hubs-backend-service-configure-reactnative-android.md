---
author: alexeystrakh
ms.author: alstrakh
ms.date: 06/11/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 9330488debb0b7611fdd63656e4c555566c749dd
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060517"
---
### <a name="configure-required-android-packages"></a>Konfigurera nödvändiga Android-paket

Paketet [länkas automatiskt](https://github.com/react-native-community/cli/blob/master/docs/autolinking.md) när appen skapas. Du har några ytterligare steg för att slutföra konfigurations processen.

### <a name="configure-android-manifest"></a>Konfigurera Android-manifest

I din "Android/app/src/AndroidManifest.xml" kontrollerar du paket namn, behörigheter och nödvändiga tjänster. Kontrol lera att du `RNPushNotificationPublisher` har registrerat och `RNPushNotificationBootEventReceiver` tagit emot och registrerat `RNPushNotificationListenerService` tjänsten. Metadata för aviseringar kan användas för att anpassa utseendet på dina push-meddelanden.

  ```xml
  <manifest xmlns:android="http://schemas.android.com/apk/res/android"
      package="YOUR_PACKAGE_NAME">

        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>

        <application
          android:name=".MainApplication"
          android:label="@string/app_name"
          android:usesCleartextTraffic="true"
          android:icon="@mipmap/ic_launcher"
          android:roundIcon="@mipmap/ic_launcher_round"
          android:allowBackup="false"
          android:theme="@style/AppTheme">

          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_channel_name"
                      android:value="PushDemo Channel"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_channel_description"
                      android:value="PushDemo Channel Description"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_foreground"
                      android:value="true"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_color"
                      android:resource="@android:color/white"/>

          <receiver android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationPublisher" />
          <receiver android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationBootEventReceiver">
              <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
              </intent-filter>
          </receiver>

          <service
              android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationListenerService"
              android:exported="false" >
              <intent-filter>
                  <action android:name="com.google.firebase.MESSAGING_EVENT" />
              </intent-filter>
          </service>

          <activity
            android:name=".MainActivity"
            android:label="@string/app_name"
            android:configChanges="keyboard|keyboardHidden|orientation|screenSize|uiMode"
            android:launchMode="singleTask"
            android:windowSoftInputMode="adjustResize">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
          </activity>
          <activity android:name="com.facebook.react.devsupport.DevSettingsActivity" />
        </application>

  </manifest>
  ```

### <a name="configure-google-services"></a>Konfigurera Google-tjänster

I "Android/app/build. gradle" registrerar du Google-tjänster:

```gradle
dependencies {
  ...
  implementation 'com.google.firebase:firebase-analytics:17.3.0'
  ...
}

apply plugin: 'com.google.gms.google-services'
```

Kopiera filen "google-services.jspå" som du laddade ned under installationen av FCM till projektmappen "Android/app/".

### <a name="handle-push-notifications-for-android"></a>Hantera push-meddelanden för Android

Du konfigurerade den befintliga `RNPushNotificationListenerService` tjänsten för att hantera inkommande Android-push-meddelanden. Den här tjänsten registrerades tidigare i applikations manifestet. Den bearbetar inkommande meddelanden och proxyservrar till den plattforms oberoende reagera på den ursprungliga delen. Inga ytterligare steg krävs.
