---
title: Skicka meddelanden till Android-appar med Azure Notification Hubs och Firebase Cloud Messaging | Microsoft Docs
description: I de här självstudierna lär du dig att använda Azure Notification Hubs och Google Firebase Cloud Messaging för att skicka push-meddelanden till Android-enheter.
services: notification-hubs
documentationcenter: android
keywords: push-meddelanden, push-meddelande, push-meddelande för android, fcm, firebase cloud messaging
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 02298560-da61-4bbb-b07c-e79bd520e420
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/30/2019
ms.author: jowargo
ms.openlocfilehash: c21b1e38077575fc49221150a61693a23aa408a3
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509108"
---
# <a name="tutorial-push-notifications-to-android-devices-by-using-azure-notification-hubs-and-google-firebase-cloud-messaging"></a>Självstudier: Skicka push-meddelanden till Android-enheter med hjälp av Azure Notification Hubs och Google Firebase Cloud Messaging

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

I de här självstudierna får du lära dig hur du använder Azure Notification Hubs och Firebase Cloud Messaging (FCM) för att skicka meddelanden till ett Android-program. I den här självstudien skapar du en tom Android-app som tar emot push-meddelanden via Firebase Cloud Messaging (FCM).

Den färdiga koden för den här självstudiekursen kan hämtas [från GitHub](https://github.com/Azure/azure-notificationhubs-android/tree/master/samples/FCMTutorialApp).

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa ett Android Studio-projekt.
> * Skapa ett Firebase-projekt som har stöd för Firebase Cloud Messaging.
> * Skapa en hubb.
> * Anslut din app till hubben.
> * Testa appen.

## <a name="prerequisites"></a>Förutsättningar

Du måste ha ett aktivt Azure-konto för att slutföra den här kursen. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/free/). 

Du måste också följande objekt: 

* Den senaste versionen av [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797)
* Android 2.3 eller högre för Firebase Cloud Messaging
* Google Repository revision 27 eller högre för Firebase Cloud Messaging
* Google Play Services 9.0.2 eller högre för Firebase Cloud Messaging

Den här kursen är en förutsättning för att göra Meddelandehubbar kurs för Android-appar.

## <a name="create-an-android-studio-project"></a>Skapa ett Android Studio-projekt

1. Starta Android Studio.
2. Välj **filen**, peka på **New**, och välj sedan **nytt projekt**. 
2. På den **väljer du projektet** väljer **tom aktivitet**, och välj sedan **nästa**. 
3. På den **konfigurera ditt projekt** utför följande steg: 
    1. Ange ett namn på programmet.
    2. Ange en plats där du kan spara projektfiler. 
    3. Välj **Slutför**. 

        ![Konfigurera projektet)](./media/notification-hubs-android-push-notification-google-fcm-get-started/configure-project.png)

## <a name="create-a-firebase-project-that-supports-fcm"></a>Skapa ett Firebase-projekt som har stöd för FCM

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-hub"></a>Konfigurera ett nav

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>Konfigurera Firebase Cloud Messaging-inställningar för hubben

1. I den vänstra rutan under **inställningar,** Välj **Google (GCM/FCM)** . 
2. Ange den **servernyckel** för FCM-projektet som du sparade tidigare. 
3. I verktygsfältet väljer **spara**. 

    ![Azure Notification Hub - Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. Azure-portalen visas ett meddelande i aviseringar att hubben har uppdaterats. Knappen **Spara** är inaktiverad. 

Hubben har nu konfigurerats för att fungera med Firebase Cloud Messaging. Du kan också ha anslutningssträngar som är nödvändiga för att skicka meddelanden till en enhet och registrera en app för att ta emot meddelanden.

## <a id="connecting-app"></a>Anslut appen till meddelandehubben

### <a name="add-google-play-services-to-the-project"></a>Lägga till Google Play-tjänster till projektet

[!INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

### <a name="add-azure-notification-hubs-libraries"></a>Lägga till Azure Notification Hubs-bibliotek

1. Lägg till följande rader i avsnittet beroenden i Build.Gradle-filen för appen.

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```

2. Lägg till följande lagringsplats efter avsnittet beroenden.

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

### <a name="add-google-firebase-support"></a>Lägg till stöd för Google Firebase

1. I Build.Gradle-filen för appen lägger du till följande rader i den **beroenden** avsnittet om de inte redan finns. 

    ```gradle
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:17.3.4'
    ```

2. Lägg till följande plugin-programmet i slutet av filen om den inte redan finns där. 

    ```gradle
    apply plugin: 'com.google.gms.google-services'
    ```
3. Välj **Synkronisera nu** i verktygsfältet.

### <a name="update-the-androidmanifestxml-file"></a>Uppdatera AndroidManifest.xml-filen

1. När du tagit emot din Registreringstoken för fcm kan du använda den för att [registrering med Azure Notification Hubs](notification-hubs-push-notification-registration-management.md). Du stöder denna registrering i bakgrunden med hjälp av en `IntentService` med namnet `RegistrationIntentService`. Den här tjänsten uppdaterar även din FCM-registreringstoken.

    Lägg till följande tjänstedefinition i filen AndroidManifest.xml inuti taggen `<application>`.

    ```xml
    <service
        android:name=".RegistrationIntentService"
        android:exported="false">
    </service>
    ```

2. Du måste också definiera en mottagare för att ta emot meddelanden. Lägg till följande mottagardefinition i filen AndroidManifest.xml inuti taggen `<application>`. 

    ```xml
    <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
        android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <category android:name="<your package name>" />
        </intent-filter>
    </receiver>
    ```

    > [!IMPORTANT]
    > Ersätt den `<your package NAME>` med det faktiska paketnamnet som visas överst i filen AndroidManifest.xml.
3. Lägg till följande nödvändiga FCM-relaterade behörigheter nedan i `</application>` tagg.

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="add-code"></a>Lägg till kod

1. Expandera  **app** > **src** > **main** > **java** i projektvyn. Högerklicka på paketmappen under **java**väljer **New**, och välj sedan **Java-klass**. Ange **NotificationSettings** för namn och välj sedan **OK**.

    Se till att uppdatera dessa tre platshållare i följande kod för klassen `NotificationSettings`:

   * **HubListenConnectionString**: Anslutningssträngen **DefaultListenAccessSignature** för din hubb. Du kan kopiera denna anslutningssträng genom att klicka på **åtkomstprinciper** i hubben i den [Azure Portal].
   * **HubName**: Använd namnet på din hubb som visas på hubbsidan i den [Azure Portal].

     `NotificationSettings` kod:

        ```java
        public class NotificationSettings {
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
        }
        ```

     > [!IMPORTANT]
     > Ange den **namn** och **DefaultListenSharedAccessSignature** på hubben innan du fortsätter. 

3. Lägg till ytterligare en ny klass i projektet och ge den namnet `RegistrationIntentService`. Den här klassen implementerar den `IntentService` gränssnitt. Den hanterar också [uppdateringen av FCM-token](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) och [registrering hos meddelandehubben](notification-hubs-push-notification-registration-management.md).

    Använd följande kod för den här klassen.

    ```java
    import android.app.IntentService;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.preference.PreferenceManager;
    import android.util.Log;
    import com.google.android.gms.tasks.OnSuccessListener;
    import com.google.firebase.iid.FirebaseInstanceId;
    import com.google.firebase.iid.InstanceIdResult;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    import java.util.concurrent.TimeUnit;

    public class RegistrationIntentService extends IntentService {

        private static final String TAG = "RegIntentService";
        String FCM_token = null;

        private NotificationHub hub;

        public RegistrationIntentService() {
            super(TAG);
        }

        @Override
        protected void onHandleIntent(Intent intent) {

            SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
            String resultString = null;
            String regID = null;
            String storedToken = null;

            try {
                FirebaseInstanceId.getInstance().getInstanceId().addOnSuccessListener(new OnSuccessListener<InstanceIdResult>() { 
                    @Override 
                    public void onSuccess(InstanceIdResult instanceIdResult) { 
                        FCM_token = instanceIdResult.getToken(); 
                        Log.d(TAG, "FCM Registration Token: " + FCM_token); 
                    } 
                }); 
                TimeUnit.SECONDS.sleep(1);

                // Storing the registration ID that indicates whether the generated token has been
                // sent to your server. If it is not stored, send the token to your server.
                // Otherwise, your server should have already received the token.
                if (((regID=sharedPreferences.getString("registrationID", null)) == null)){

                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.d(TAG, "Attempting a new registration with NH using FCM token : " + FCM_token);
                    regID = hub.register(FCM_token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1,tag2").getRegistrationId();

                    resultString = "New NH Registration Successfully - RegId : " + regID;
                    Log.d(TAG, resultString);

                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                    sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                }

                // Check to see if the token has been compromised and needs refreshing.
                else if ((storedToken=sharedPreferences.getString("FCMtoken", "")) != FCM_token) {

                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.d(TAG, "NH Registration refreshing with token : " + FCM_token);
                    regID = hub.register(FCM_token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1,tag2").getRegistrationId();

                    resultString = "New NH Registration Successfully - RegId : " + regID;
                    Log.d(TAG, resultString);

                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                    sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                }

                else {
                    resultString = "Previously Registered Successfully - RegId : " + regID;
                }
            } catch (Exception e) {
                Log.e(TAG, resultString="Failed to complete registration", e);
                // If an exception happens while fetching the new token or updating registration data
                // on a third-party server, this ensures that we'll attempt the update at a later time.
            }

            // Notify UI that registration has completed.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(resultString);
            }
        }
    }
    ```

4. I den `MainActivity` klass, lägger du till följande `import` uttryck ovanför klassdeklarationen.

    ```java
    import com.google.android.gms.common.ConnectionResult;
    import com.google.android.gms.common.GoogleApiAvailability;
    import com.microsoft.windowsazure.notifications.NotificationsManager;
    import android.content.Intent;
    import android.util.Log;
    import android.widget.TextView;
    import android.widget.Toast;
    ```

5. Lägg till följande medlemmar högst upp i klassen. Du använder de här fälten för att [kontrollera tillgängligheten för Google Play-tjänster, enligt rekommendationer från Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

    ```java
    public static MainActivity mainActivity;
    public static Boolean isVisible = false;
    private static final String TAG = "MainActivity";
    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    ```

6. I den `MainActivity` klass, lägger du till följande metod för att kontrollera tillgängligheten för Google Play-tjänster.

    ```java
    /**
    * Check the device to make sure it has the Google Play Services APK. If
    * it doesn't, display a dialog box that enables  users to download the APK from
    * the Google Play Store or enable it in the device's system settings.
    */

    private boolean checkPlayServices() {
        GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
        int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.SUCCESS) {
            if (apiAvailability.isUserResolvableError(resultCode)) {
                apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
                        .show();
            } else {
                Log.i(TAG, "This device is not supported by Google Play Services.");
                ToastNotify("This device is not supported by Google Play Services.");
                finish();
            }
            return false;
        }
        return true;
    }
    ```

7. I den `MainActivity` klass, Lägg till följande kod som söker efter Google Play-tjänster innan du anropar den `IntentService` hämta ditt registreringstoken token och registrera med din hubb:

    ```java
    public void registerWithNotificationHubs()
    {
        if (checkPlayServices()) {
            // Start IntentService to register this application with FCM.
            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    }
    ```

8. I den `OnCreate` -metoden för den `MainActivity` klass, lägger du till följande kod för att starta registreringsprocessen när aktiviteten har skapats:

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mainActivity = this;
        registerWithNotificationHubs();
        MyHandler.createChannelAndHandleNotifications(getApplicationContext());
    }
    ```

9. Kontrollera appens status och rapportera statusen i din app genom att lägga till de ytterligare metoderna till `MainActivity`:

    ```java
    @Override
    protected void onStart() {
        super.onStart();
        isVisible = true;
    }

    @Override
    protected void onPause() {
        super.onPause();
        isVisible = false;
    }

    @Override
    protected void onResume() {
        super.onResume();
        isVisible = true;
    }

    @Override
    protected void onStop() {
        super.onStop();
        isVisible = false;
    }

    public void ToastNotify(final String notificationMessage) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                TextView helloText = (TextView) findViewById(R.id.text_hello);
                helloText.setText(notificationMessage);
            }
        });
    }
    ```

10. I metoden `ToastNotify` används kontrollen *”Hello World”* `TextView` för att rapportera status och meddelanden på ett beständigt sätt i appen. Lägg till följande ID för den kontrollen i layouten **res** > **layout** > **activity_main.xml**.

    ```java
    android:id="@+id/text_hello"
    ```

11. Därefter lägger du till en underklass för den mottagare som du har definierat i AndroidManifest.xml. Lägg till ytterligare en ny klass i projektet och ge den namnet `MyHandler`.

12. Lägg till följande importuttryck längst upp i `MyHandler.java`:

    ```java
    import android.app.NotificationChannel;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.media.RingtoneManager;
    import android.net.Uri;
    import android.os.Build;
    import android.os.Bundle;
    import android.support.v4.app.NotificationCompat;
    import com.microsoft.windowsazure.notifications.NotificationsHandler;    
    import com.microsoft.windowsazure.notifications.NotificationsManager;
    ```

13. Lägg till följande kod för den `MyHandler` klass, vilket gör det till en underklass av `com.microsoft.windowsazure.notifications.NotificationsHandler`.

    Den här koden åsidosätter metoden `OnReceive` vilket i sin tur gör att hanteraren rapporterar meddelanden som tas emot. Hanteraren skickar även push-meddelandena till Android Notification Manager genom att använda metoden `sendNotification()`. Anropa den `sendNotification()` metoden när appen körs inte och ett meddelande tas emot.

    ```java
    public class MyHandler extends NotificationsHandler {
        public static final String NOTIFICATION_CHANNEL_ID = "nh-demo-channel-id";
        public static final String NOTIFICATION_CHANNEL_NAME = "Notification Hubs Demo Channel";
        public static final String NOTIFICATION_CHANNEL_DESCRIPTION = "Notification Hubs Demo Channel";
    
        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;
    
        @Override
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String nhMessage = bundle.getString("message");
            sendNotification(nhMessage);
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(nhMessage);
            }
        }
    
        private void sendNotification(String msg) {
    
            Intent intent = new Intent(ctx, MainActivity.class);
            intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
    
            mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);
    
            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                    intent, PendingIntent.FLAG_ONE_SHOT);
    
            Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
            NotificationCompat.Builder notificationBuilder = new NotificationCompat.Builder(
                    ctx,
                    NOTIFICATION_CHANNEL_ID)
                    .setContentText(msg)
                    .setPriority(NotificationCompat.PRIORITY_HIGH)
                    .setSmallIcon(android.R.drawable.ic_popup_reminder)
                    .setBadgeIconType(NotificationCompat.BADGE_ICON_SMALL);
    
            notificationBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, notificationBuilder.build());
        }
    
        public static void createChannelAndHandleNotifications(Context context) {
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
                NotificationChannel channel = new NotificationChannel(
                        NOTIFICATION_CHANNEL_ID,
                        NOTIFICATION_CHANNEL_NAME,
                        NotificationManager.IMPORTANCE_HIGH);
                channel.setDescription(NOTIFICATION_CHANNEL_DESCRIPTION);
                channel.setShowBadge(true);
    
                NotificationManager notificationManager = context.getSystemService(NotificationManager.class);
                notificationManager.createNotificationChannel(channel);
                NotificationsManager.handleNotifications(context, "", MyHandler.class);
            }
        }
    }
    ```

14. I Android Studio på menyraden väljer **skapa** > **återskapa projekt** att se till att det inte finns några fel i din kod. Om du får ett felmeddelande om de `ic_launcher` ikonen Ta bort följande instruktion från AndroidManifest.xml-filen: 

    ```
        android:icon="@mipmap/ic_launcher"
    ```
15. Kör appen på din enhet och verifiera att den registreras hos hubben.

    > [!NOTE]
    > Registreringen kan misslyckas vid första starten tills den `onTokenRefresh()` metoden för instans-ID-tjänst anropas. Uppdateringen bör initiera en lyckad registrering hos meddelandehubben.

    ![Enhetsregistreringen lyckades](./media/notification-hubs-android-push-notification-google-fcm-get-started/device-registration.png)

## <a name="test-send-notification-from-the-notification-hub"></a>Testa att skicka ett meddelande från meddelandehubben

Du kan skicka push-meddelanden från den [Azure Portal] genom att utföra följande steg:

1. Azure-portalen på Notification Hub-sidan för din hubb, Välj **testsändning** i den **felsökning** avsnittet.
3. Välj **Android** under **Plattformar**.
4. Välj **Skicka**.  Du kommer inte se ett meddelande på Android-enhet ännu eftersom du inte köra mobilappen på den. När du har kört den mobila appen, Välj den **skicka** knappen igen för att se meddelandet.
5. Se resultatet av åtgärden i listan längst ned på sidan.

    ![Azure Notification Hubs – Prova att skicka](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)
6. Du ser meddelandet på din enhet. 

    ![Aviseringsmeddelande på enhet](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-on-device.png)
    

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

### <a name="run-the-mobile-app-on-emulator"></a>Kör mobilappen i emulatorn
Innan du testar push-meddelanden inne i en emulator, se till att emulatorbilden stöder den Google API-nivå som du har valt för din app. Om avbildningen inte stöder interna Google APIs kan du få den **SERVICE\_inte\_tillgänglig** undantag.

Kontrollera dessutom att du har lagt till ditt Google-konto till din körs emulator under **inställningar** > **konton**. Annars kan försöken att registrera med FCM kan resultera i den **AUTENTISERING\_misslyckades** undantag.

## <a name="next-steps"></a>Nästa steg
I den här självstudien använde du Firebase Cloud Messaging för att sända meddelanden till alla Android-enheter som registrerats med tjänsten. Information om hur du skickar meddelanden till specifika enheter finns i följande självstudie:

> [!div class="nextstepaction"]
>[Självstudie: Push-meddelanden till specifika Android-enheter](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- Images. -->

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: https://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: notification-hubs-push-notification-overview.md
[Azure Portal]: https://portal.azure.com
