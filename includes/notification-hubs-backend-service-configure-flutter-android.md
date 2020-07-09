---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 11bd05231aa3b5b336400d2434e63e57807a7e69
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147824"
---
### <a name="add-the-google-services-json-file"></a>Lägg till Google Services JSON-filen

1. **Kontroll**  +  **Klicka** på **Android** -mappen och välj sedan **Öppna i Android Studio**. Växla sedan till **projektvyn** (om den inte redan finns).

1. Leta upp *google-services.js* filen som du laddade ned tidigare när du konfigurerade **PushDemo** -projektet i [Firebase-konsolen](https://console.firebase.google.com). Dra den sedan till **app** -modulens rot Katalog (**Android**  >  **Android**  >  -**app**).

### <a name="configure-build-settings-and-permissions"></a>Konfigurera build-inställningar och-behörigheter

1. Växla **projektvyn** till **Android**.

1. Öppna **AndroidManifest.xml**och Lägg sedan till **Internet** och **READ_PHONE_STATE** behörigheter efter **program** element före stängnings **</manifest>** tag gen.

    ```xml
    <manifest>
        <application>...</application>
        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    </manifest>
    ```

### <a name="add-the-firebase-sdks"></a>Lägg till Firebase SDK: er

1. I **Android Studio**öppnar du filen **build. gradle** för projekt nivå (**gradle scripts**  >  **build. gradle (projekt: Android)**). kontrol lera att du har Klas Sök vägen "com. Google. GMS: Google-Services" i ``buildscript``  >  noden **beroenden** .

    ```json
    buildscript {

      repositories {
        // Check that you have the following line (if not, add it):
        google()  // Google's Maven repository
      }

      dependencies {
        // ...

        // Add the following line:
        classpath 'com.google.gms:google-services:4.3.3'  // Google Services plugin
      }
    }

    allprojects {
      // ...

      repositories {
        // Check that you have the following line (if not, add it):
        google()  // Google's Maven repository
        // ...
      }
    }
    ```

    > [!NOTE]
    > Se till att du refererar till den senaste versionen enligt anvisningarna i [Firebase-konsolen](https://console.firebase.google.com) när du skapade **Android-projektet**.

1. I filen **build. gradle** för app-nivå (**gradle scripts**  >  **build. gradle (modul: app)**) använder du **Google Services gradle-plugin-programmet**. Använd plugin-programmet precis ovanför **Android** -noden.

    ```json
    // ...

    // Add the following line:
    apply plugin: 'com.google.gms.google-services'  // Google Services plugin

    android {
      // ...
    }
    ```

1. I samma fil, i noden **beroenden** , lägger du till beroendet för **moln meddelandets** Android-bibliotek.

    ```json
    dependencies {
        // ...
        implementation 'com.google.firebase:firebase-messaging:20.2.0'
    }
    ```

    > [!NOTE]
    > Se till att du refererar till den senaste versionen enligt [dokumentationen om Cloud Messaging Android-klienten](https://firebase.google.com/docs/cloud-messaging/android/client).

1. Spara ändringarna och klicka sedan på knappen **Synkronisera nu** (från verktygsfälts tolken) eller **Synkronisera projekt med Gradle-filer**.

### <a name="handle-push-notifications-for-android"></a>Hantera push-meddelanden för Android

1. I **Android Studio**, **kontroll**  +  **klickar** du på mappen **com. <your_organization>. pushdemo** (**app**  >  **src**  >  **main**  >  **Kotlin**) och väljer sedan **paket** i den **nya** menyn. Ange **tjänster** som namn och tryck sedan på **RETUR**.

1. **Kontroll**  +  **Klicka** på mappen **tjänster** , Välj **Kotlin fil/klass** på menyn **ny** . Ange **DeviceInstallationService** som namn och tryck sedan på **RETUR**.

1. Implementera **DeviceInstallationService** med följande kod.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import android.annotation.SuppressLint
    import android.content.Context
    import android.provider.Settings.Secure
    import com.google.android.gms.common.ConnectionResult
    import com.google.android.gms.common.GoogleApiAvailability
    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodCall
    import io.flutter.plugin.common.MethodChannel

    @SuppressLint("HardwareIds")
    class DeviceInstallationService {

        companion object {
            const val DEVICE_INSTALLATION_CHANNEL = "com.<your_organization>.pushdemo/deviceinstallation"
            const val GET_DEVICE_ID = "getDeviceId"
            const val GET_DEVICE_TOKEN = "getDeviceToken"
            const val GET_DEVICE_PLATFORM = "getDevicePlatform"
        }

        private var context: Context
        private var deviceInstallationChannel : MethodChannel

        val playServicesAvailable
            get() = GoogleApiAvailability.getInstance().isGooglePlayServicesAvailable(context) == ConnectionResult.SUCCESS

        constructor(context: Context, flutterEngine: FlutterEngine) {
            this.context = context
            deviceInstallationChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, DEVICE_INSTALLATION_CHANNEL)
            deviceInstallationChannel.setMethodCallHandler { call, result -> handleDeviceInstallationCall(call, result) }
        }

        fun getDeviceId() : String
            = Secure.getString(context.applicationContext.contentResolver, Secure.ANDROID_ID)

        fun getDeviceToken() : String {
            if(!playServicesAvailable) {
                throw Exception(getPlayServicesError())
            }

            // TODO: Revisit once we have created the PushNotificationsFirebaseMessagingService
            val token = "Placeholder_Get_Value_From_FirebaseMessagingService_Implementation"

            if (token.isNullOrBlank()) {
                throw Exception("Unable to resolve token for FCM.")
            }

            return token
        }

        fun getDevicePlatform() : String = "fcm"

        private fun handleDeviceInstallationCall(call: MethodCall, result: MethodChannel.Result) {
            when (call.method) {
                GET_DEVICE_ID -> {
                    result.success(getDeviceId())
                }
                GET_DEVICE_TOKEN -> {
                    getDeviceToken(result)
                }
                GET_DEVICE_PLATFORM -> {
                    result.success(getDevicePlatform())
                }
                else -> {
                    result.notImplemented()
                }
            }
        }

        private fun getDeviceToken(result: MethodChannel.Result) {
            try {
                val token = getDeviceToken()
                result.success(token)
            }
            catch (e: Exception) {
                result.error("ERROR", e.message, e)
            }
        }

        private fun getPlayServicesError(): String {
            val resultCode = GoogleApiAvailability.getInstance().isGooglePlayServicesAvailable(context)

            if (resultCode != ConnectionResult.SUCCESS) {
                return if (GoogleApiAvailability.getInstance().isUserResolvableError(resultCode)){
                    GoogleApiAvailability.getInstance().getErrorString(resultCode)
                } else {
                    "This device is not supported"
                }
            }

            return "An error occurred preventing the use of push notifications"
        }
    }
    ```

    > [!NOTE]
    > Den här klassen implementerar den plattformsspecifik motparten för `com.<your_organization>.pushdemo/deviceinstallation` kanalen. Detta definierades i Flutter-delen av appen i **DeviceInstallationService. Dart**. I detta fall görs anropen från den gemensamma koden till den interna värden. Se till att ersätta **<your_organization>** med din egen organisation oavsett var den används.
    >
    > Den här klassen ger ett unikt ID (med hjälp av [Secure. AndroidId](https://developer.android.com/reference/android/provider/Settings.Secure#ANDROID_ID)) som en del av nytto lasten Notification Hub-registrering.

1. Lägg till en annan **Kotlin-fil/-klass** i mappen **tjänster** som heter *NotificationRegistrationService*och Lägg sedan till följande kod.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodChannel

    class NotificationRegistrationService {

        companion object {
            const val NOTIFICATION_REGISTRATION_CHANNEL = "com.<your_organization>.pushdemo/notificationregistration"
            const val REFRESH_REGISTRATION = "refreshRegistration"
        }

        private var notificationRegistrationChannel : MethodChannel

        constructor(flutterEngine: FlutterEngine) {
            notificationRegistrationChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, NotificationRegistrationService.NOTIFICATION_REGISTRATION_CHANNEL)
        }

        fun refreshRegistration() {
            notificationRegistrationChannel.invokeMethod(REFRESH_REGISTRATION, null)
        }
    }
    ```

    > [!NOTE]
    > Den här klassen implementerar den plattformsspecifik motparten för `com.<your_organization>.pushdemo/notificationregistration` kanalen. Detta definierades i Flutter-delen av appen i **NotificationRegistrationService. Dart**. I detta fall görs anropen från den interna värden till den gemensamma koden. Börja med att ersätta **<your_organization>** med din egen organisation oavsett var den används.

1. Lägg till en annan **Kotlin-fil/-klass** i mappen **tjänster** som heter *NotificationActionService*och Lägg sedan till följande kod.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodCall
    import io.flutter.plugin.common.MethodChannel

    class NotificationActionService {
        companion object {
            const val NOTIFICATION_ACTION_CHANNEL = "com.<your_organization>.pushdemo/notificationaction"
            const val TRIGGER_ACTION = "triggerAction"
            const val GET_LAUNCH_ACTION = "getLaunchAction"
        }

        private var notificationActionChannel : MethodChannel
        var launchAction : String? = null

        constructor(flutterEngine: FlutterEngine) {
            notificationActionChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, NotificationActionService.NOTIFICATION_ACTION_CHANNEL)
            notificationActionChannel.setMethodCallHandler { call, result -> handleNotificationActionCall(call, result) }
        }

        fun triggerAction(action: String) {
            notificationActionChannel.invokeMethod(NotificationActionService.TRIGGER_ACTION, action)
        }

        private fun handleNotificationActionCall(call: MethodCall, result: MethodChannel.Result) {
            when (call.method) {
                NotificationActionService.GET_LAUNCH_ACTION -> {
                    result.success(launchAction)
                }
                else -> {
                    result.notImplemented()
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Den här klassen implementerar den plattformsspecifik motparten för `com.<your_organization>.pushdemo/notificationaction` kanalen. Som definierades i Flutter-delen av appen i **NotificationActionService. Dart**. Anrop kan göras i båda riktningarna i det här fallet. Se till att ersätta **<your_organization>** med din egen organisation oavsett var den används.

1. Lägg till en ny **Kotlin-fil/-klass** i paketet **com. <your_organization>. Pushdemo** som heter *PushNotificationsFirebaseMessagingService*och implementera sedan med följande kod.

    ```kotlin
    package com.<your_organization>.pushdemo

    import android.os.Handler
    import android.os.Looper
    import com.google.firebase.messaging.FirebaseMessagingService
    import com.google.firebase.messaging.RemoteMessage
    import com.<your_organization>.pushdemo.services.NotificationActionService
    import com.<your_organization>.pushdemo.services.NotificationRegistrationService

    class PushNotificationsFirebaseMessagingService : FirebaseMessagingService() {

        companion object {
            var token : String? = null
            var notificationRegistrationService : NotificationRegistrationService? = null
            var notificationActionService : NotificationActionService? = null
        }

        override fun onNewToken(token: String) {
            PushNotificationsFirebaseMessagingService.token = token
            notificationRegistrationService?.refreshRegistration()
        }

        override fun onMessageReceived(message: RemoteMessage) {
            message.data.let {
                Handler(Looper.getMainLooper()).post {
                    notificationActionService?.triggerAction(it.getOrDefault("action", null))
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Den här klassen ansvarar för att hantera meddelanden när appen körs i förgrunden. Det kommer att villkorligt anropa **triggerAction** på **NotificationActionService** om en åtgärd ingår i meddelande nytto lasten som tas emot i **onMessageReceived**. Detta kommer också att anropa **refreshRegistration** på **NotificationRegistrationService** när **Firebase** -token återskapas genom att åsidosätta funktionen **onNewToken** .
    >
    > Innan du gör det kan du ersätta **<your_organization>** med din egen organisation var den används.

1. I **AndroidManifest.xml** (**appens**  >  **src**  >  **main**) lägger du till **PushNotificationsFirebaseMessagingService** längst ned i **program** elementet med `com.google.firebase.MESSAGING_EVENT` avsikts filtret.

    ```xml
    <manifest>
        <application>
            <!-- EXISTING MANIFEST CONTENT -->
             <service
                android:name="com.<your_organization>.pushdemo.PushNotificationsFirebaseMessagingService"
                android:exported="false">
                <intent-filter>
                    <action android:name="com.google.firebase.MESSAGING_EVENT" />
                </intent-filter>
            </service>
        </application>
    </manifest>
    ```

1. Gå tillbaka till **DeviceInstallationService**och se till att följande importer finns överst i filen.

    ```kotlin
    package com.<your_organization>.pushdemo
    import com.<your_organization>.pushdemo.services.PushNotificationsFirebaseMessagingService
    ```

    > [!NOTE]
    > Ersätt **<your_organization>** med ditt eget organisations värde.

1. Uppdatera platshållartexten *Placeholder_Get_Value_From_FirebaseMessagingService_Implementation* för att hämta värdet för token från **PushNotificationFirebaseMessagingService**.

    ```kotlin
    fun getDeviceToken() : String {
        if(!playServicesAvailable) {
            throw Exception(getPlayServicesError())
        }

        // Get token from the PushNotificationsFirebaseMessagingService.token field.
        val token = PushNotificationsFirebaseMessagingService.token

        if (token.isNullOrBlank()) {
            throw Exception("Unable to resolve token for FCM.")
        }

        return token
    }
    ```

1. I **MainActivity**kontrollerar du att följande importer finns överst i filen.

    ```kotlin
    package com.<your_organization>.pushdemo

    import android.content.Intent
    import android.os.Bundle
    import com.google.android.gms.tasks.OnCompleteListener
    import com.google.firebase.iid.FirebaseInstanceId
    import com.<your_organization>.pushdemo.services.DeviceInstallationService
    import com.<your_organization>.pushdemo.services.NotificationActionService
    import com.<your_organization>.pushdemo.services.NotificationRegistrationService
    import io.flutter.embedding.android.FlutterActivity
    ```

    > [!NOTE]
    > Ersätt **<your_organization>** med ditt eget organisations värde.

1. Lägg till en variabel för att lagra en referens till **DeviceInstallationService**.

    ```kotlin
    private lateinit var deviceInstallationService: DeviceInstallationService
    ```

1. Lägg till en funktion med namnet **processNotificationActions** för att kontrol lera om en **avsikt** har ett extra värde med namnet **Action**. Utlöser villkorligt åtgärden eller lagrar den för användning senare om åtgärden bearbetas när appen startas.

    ```kotlin
     private fun processNotificationActions(intent: Intent, launchAction: Boolean = false) {
        if (intent.hasExtra("action")) {
            var action = intent.getStringExtra("action");

            if (action.isNotEmpty()) {
                if (launchAction) {
                    PushNotificationsFirebaseMessagingService.notificationActionService?.launchAction = action
                }
                else {
                    PushNotificationsFirebaseMessagingService.notificationActionService?.triggerAction(action)
                }
            }
        }
    }
    ```

1. Åsidosätt **onNewIntent** -funktionen för att anropa **processNotificationActions**.

    ```kotlin
    override fun onNewIntent(intent: Intent) {
        super.onNewIntent(intent)
        processNotificationActions(intent)
    }
    ```

    > [!NOTE]
    > Eftersom **LaunchMode** för **MainActivity** är inställt på **SingleTop**skickas en **avsikt** till den befintliga **aktivitets** instansen via **onNewIntent** -funktionen i stället för **OnCreate** -funktionen, så du måste hantera en inkommande **avsikt** i både **onCreate** -och **onNewIntent** -funktionerna.

1. Åsidosätt funktionen **onCreate** , ange **deviceInstallationService** till en ny instans av **deviceInstallationService**.

    ```kotlin
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        flutterEngine?.let {
            deviceInstallationService = DeviceInstallationService(context, it)
        }
    }
    ```

1. Ange egenskaperna **notificationActionService** och **notificationRegistrationService** på **PushNotificationFirebaseMessagingServices**.

    ```kotlin
    flutterEngine?.let {
      deviceInstallationService = DeviceInstallationService(context, it)
      PushNotificationsFirebaseMessagingService.notificationActionService = NotificationActionService(it)
      PushNotificationsFirebaseMessagingService.notificationRegistrationService = NotificationRegistrationService(it)
    }
    ```

1. I samma funktion måste du villkorligt anropa **FirebaseInstanceId. GetInstance (). InstanceID**. Implementera **OnCompleteListener** för att ange det resulterande **token** -värdet på **PushNotificationFirebaseMessagingService** innan du anropar **refreshRegistration**.

    ```kotlin
    if(deviceInstallationService?.playServicesAvailable) {
        FirebaseInstanceId.getInstance().instanceId
            .addOnCompleteListener(OnCompleteListener { task ->
                if (!task.isSuccessful)
                    return@OnCompleteListener

                PushNotificationsFirebaseMessagingService.token = task.result?.token
                PushNotificationsFirebaseMessagingService.notificationRegistrationService?.refreshRegistration()
            })
    }
    ```

1. Anropa **processNotificationActions** i slutet av funktionen fortfarande i **onCreate**. Använd *True* för argumentet *launchAction* för att ange att den här åtgärden bearbetas när appen startas.

    ```kotlin
    processNotificationActions(this.intent, true)
    ```

> [!NOTE]
> Du måste registrera om appen varje gång du kör den och stoppa den från en felsökningssession för att fortsätta att ta emot push-meddelanden.
