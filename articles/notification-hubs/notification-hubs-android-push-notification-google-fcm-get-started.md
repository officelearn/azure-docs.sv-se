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
ms.date: 02/05/2019
ms.author: jowargo
ms.openlocfilehash: 2fe448f3ed91f2c6dd242c24aa378c3541eceecc
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57857954"
---
# <a name="tutorial-push-notifications-to-android-devices-by-using-azure-notification-hubs-and-google-firebase-cloud-messaging"></a>Självstudier: Skicka push-meddelanden till Android-enheter med hjälp av Azure Notification Hubs och Google Firebase Cloud Messaging

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

I de här självstudierna får du lära dig hur du använder Azure Notification Hubs och Firebase Cloud Messaging (FCM) för att skicka meddelanden till ett Android-program. I den här självstudien skapar du en tom Android-app som tar emot push-meddelanden via Firebase Cloud Messaging (FCM).

Den slutförda koden för den här självstudiekursen kan hämtas från GitHub [här](https://github.com/Azure/azure-notificationhubs-android/tree/master/samples/FCMTutorialApp).

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa ett Android Studio-projekt.
> * Skapa ett Firebase-projekt som har stöd för Firebase Cloud Messaging.
> * Skapa en meddelandehubb.
> * Anslut appen till meddelandehubben.
> * Testa appen.

## <a name="prerequisites"></a>Förutsättningar

Du måste ha ett aktivt Azure-konto för att slutföra den här kursen. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/free/).

* Förutom det aktiva Azure-konto som nämns ovan, kräver de här självstudierna att du har den senaste versionen av [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797).
* Android 2.3 eller högre för Firebase Cloud Messaging.
* Google Repository revision 27 eller högre krävs för Firebase Cloud Messaging.
* Google Play Services 9.0.2 eller högre för Firebase Cloud Messaging.
* Du måste slutföra den här kursen innan du börjar någon annan kurs om Notification Hubs för Android-appar.

## <a name="create-an-android-studio-project"></a>Skapa ett Android Studio-projekt

1. Starta Android Studio.
2. Välj **Arkiv** på menyn, peka på **Nytt** och välj **Nytt projekt**. 
2. På sidan **Choose your project** (Välj projekt) väljer du **Empty Activity** (Tom aktivitet) och väljer **Nästa**. 
3. På sidan **Konfigurera projektet** går du igenom följande steg: 
    1. Ange ett **namn** för programmet.
    2. Ange en plats för att spara projektfiler. 
    3. Välj **Slutför**. 

        ![Konfigurera projektet)](./media/notification-hubs-android-push-notification-google-fcm-get-started/configure-project.png)

## <a name="create-a-firebase-project-that-supports-fcm"></a>Skapa ett Firebase-projekt som har stöd för FCM

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Konfigurera en meddelandehubb

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>Konfigurera Firebase Cloud Messaging-inställningar för hubben

1. Välj **Google (GCM/FCM)** under **Inställningar** på den vänstra menyn. 
2. Klistra in **servernyckeln** för FCM-projektet som du sparade tidigare. 
3. Välj **Spara** i verktygsfältet. 

    ![Azure Notification Hubs - Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. Du ser ett meddelande i aviseringar att meddelandehubbar har uppdaterats. Knappen **Spara** är inaktiverad. 

Din meddelandehubb har nu konfigurerats för att fungera med Firebase Cloud Messaging och du har anslutningssträngar för att registrera din app för att både ta emot och skicka push-meddelanden.

## <a id="connecting-app"></a>Anslut appen till meddelandehubben

### <a name="add-google-play-services-to-the-project"></a>Lägga till Google Play-tjänster till projektet

[!INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

### <a name="adding-azure-notification-hubs-libraries"></a>Lägga till bibliotek för Azure Notification Hubs

1. Lägg till följande rader i avsnittet för **beroenden** i filen `Build.Gradle` för **appen**.

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```

2. Lägg till följande lagringsplats efter avsnittet **beroenden**.

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

### <a name="add-google-firebase-support"></a>Lägg till stöd för Google Firebase

1. I `Build.Gradle`-filen för **appen** lägger du till följande rader i avsnittet **beroenden** om de inte redan finns. 

    ```gradle
    implementation 'com.google.firebase:firebase-core:16.0.7'
    ```

2. Lägg till följande plugin-program i slutet av filen om det inte redan finns. 

    ```gradle
    apply plugin: 'com.google.gms.google-services'
    ```

### <a name="updating-the-androidmanifestxml"></a>Uppdatera AndroidManifest.xml

1. För att kunna stödja FCM måste du implementera en lyssnartjänst för instans-ID i din kod. Denna används för att [hämta registreringstoken](https://firebase.google.com/docs/cloud-messaging/android/client#sample-register) med hjälp av [Googles FirebaseInstanceId API](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId). I den här självstudien är namnet på klassen `MyInstanceIDService`.

    Lägg till följande tjänstedefinition i filen AndroidManifest.xml inuti taggen `<application>`.

    ```xml
    <service android:name=".MyInstanceIDService">
        <intent-filter>
            <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
        </intent-filter>
    </service>
    ```

2. När du har tagit emot din registreringstoken för FCM från FirebaseInstanceId API kan du använda den för att [registrera dig med Azure Notification Hub](notification-hubs-push-notification-registration-management.md). Du stöder denna registrering i bakgrunden med en `IntentService` som kallas för `RegistrationIntentService`. Den här tjänsten är också ansvarig för att uppdatera din FCM-registreringstoken.

    Lägg till följande tjänstedefinition i filen AndroidManifest.xml inuti taggen `<application>`.

    ```xml
    <service
        android:name=".RegistrationIntentService"
        android:exported="false">
    </service>
    ```

3. Du kommer också att definiera en mottagare för att ta emot meddelanden. Lägg till följande mottagardefinition i filen AndroidManifest.xml inuti taggen `<application>`. 

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
    > Ersätt platshållaren `<your package NAME>` med det faktiska paketnamnet som visas överst i filen `AndroidManifest.xml`.
4. Välj **Synkronisera nu** i verktygsfältet.
5. Lägg till följande nödvändiga FCM-relaterade behörigheter under ****`</application>`-taggen.

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="adding-code"></a>Lägga till kod

1. Expandera  **app** > **src** > **main** > **java** i projektvyn. Högerklicka på paketmappen under **java**, klicka på **Ny** och sedan klickar du på **Java-klass**. Ange `NotificationSettings` för namnet och välj **OK**.

    Se till att uppdatera dessa tre platshållare i följande kod för klassen `NotificationSettings`:

   * **HubListenConnectionString**: Anslutningssträngen **DefaultListenAccessSignature** för din hubb. Du kan kopiera denna anslutningssträng genom att klicka på **Åtkomstprinciper** i din hubb på [Azure Portal].
   * **HubName**: Använd meddelandehubbens namn som visas på hubbsidan i [Azure Portal].

     `NotificationSettings` kod:

        ```java
        public class NotificationSettings {
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
        }
        ```

     > [!IMPORTANT]
     > Ange den **namn** och **DefaultListenSharedAccessSignature** meddelandehubbens innan du fortsätter. 
2. Lägg till ytterligare en klass med namnet `MyInstanceIDService`. Den här klassen är din implementering av lyssnartjänsten för instans-ID.

    Koden för den här klassen anropar din `IntentService` för att [uppdatera FCM-token](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) i bakgrunden.

    ```java
    import android.content.Intent;
    import android.util.Log;
    import com.google.firebase.iid.FirebaseInstanceIdService;

    public class MyInstanceIDService extends FirebaseInstanceIdService {

        private static final String TAG = "MyInstanceIDService";

        @Override
        public void onTokenRefresh() {

            Log.d(TAG, "Refreshing FCM Registration Token");

            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    };
    ```

3. Lägg till ytterligare en ny klass i projektet, med namnet `RegistrationIntentService`. Den här klassen implementerar `IntentService`-gränssnittet och hanterar [uppdateringen av FCM-token](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) och [registreringen hos meddelandehubben](notification-hubs-push-notification-registration-management.md).

    Använd följande kod för den här klassen.

    ```java
    import android.app.IntentService;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.preference.PreferenceManager;
    import android.util.Log;
    import com.google.firebase.iid.FirebaseInstanceId;
    import com.microsoft.windowsazure.messaging.NotificationHub;

    public class RegistrationIntentService extends IntentService {

        private static final String TAG = "RegIntentService";

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
                String FCM_token = FirebaseInstanceId.getInstance().getToken();
                Log.d(TAG, "FCM Registration Token: " + FCM_token);

                // Storing the registration ID that indicates whether the generated token has been
                // sent to your server. If it is not stored, send the token to your server,
                // otherwise your server should have already received the token.
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

                // Check if the token may have been compromised and needs refreshing.
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
                // If an exception happens while fetching the new token or updating our registration data
                // on a third-party server, this ensures that we'll attempt the update at a later time.
            }

            // Notify UI that registration has completed.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(resultString);
            }
        }
    }
    ```

4. Lägg till följande `import`-uttryck ovanför klassdeklarationen i din `MainActivity`-klass.

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

6. Lägg till följande metod för att kontrollera tillgängligheten för Google Play-tjänster i klassen `MainActivity`.

    ```java
    /**
    * Check the device to make sure it has the Google Play Services APK. If
    * it doesn't, display a dialog that allows users to download the APK from
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

7. Lägg till följande kod i din `MainActivity`-klass, som gör kontroller för Google Play Service innan din `IntentService` anropas för att hämta ditt registreringstoken för FCM och utföra registreringen på meddelandehubben.

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

8. Lägg till följande kod i metoden `OnCreate` för klassen `MainActivity` för att starta registreringsprocessen när aktivitet skapas.

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

9. Lägg till de ytterligare metoderna till `MainActivity` för att kontrollera appens status och rapportera statusen i din app.

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

10. I metoden `ToastNotify` används kontrollen *”Hello World”* `TextView` för att rapportera status och meddelanden på ett beständigt sätt i appen. Lägg till följande ID för den kontrollen i layouten **res** -> **layout** -> **activity_main.xml**.

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

13. Lägg till följande kod för klassen `MyHandler`. Detta gör den till en underklass för `com.microsoft.windowsazure.notifications.NotificationsHandler`.

    Den här koden åsidosätter metoden `OnReceive` vilket i sin tur gör att hanteraren rapporterar meddelanden som tas emot. Hanteraren skickar även push-meddelandena till Android Notification Manager genom att använda metoden `sendNotification()`. Metoden `sendNotification()` ska utföras när appen inte körs och ett meddelande har tagits emot.

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

14. På menyraden i Android Studio klickar du på **Skapa** > **Återskapa projekt** för att kontrollera att det inte finns några fel i din kod. Om du får ett felmeddelande om `ic_launcher`-ikonen tar du bort följande instruktion från filen AndroidManifest.xml. 

    ```
        android:icon="@mipmap/ic_launcher"
    ```
15. Kör appen på din enhet och verifiera att den registreras hos meddelandehubben.

    > [!NOTE]
    > Registreringen kan misslyckas vid första starten tills `onTokenRefresh()`-metoden för instans-ID-tjänst anropas. Uppdateringen bör initiera en lyckad registrering hos meddelandehubben.

    ![Enhetsregistreringen lyckades](./media/notification-hubs-android-push-notification-google-fcm-get-started/device-registration.png)

## <a name="test-send-notification-from-the-notification-hub"></a>Testa att skicka ett meddelande från meddelandehubben

Du kan skicka push-meddelanden från [Azure Portal] på följande sätt:

1. I Azure-portalen på **meddelandehubbsidan** för din meddelandehubb väljer du **Skicka test** i **felsökningsavsnittet**.
3. Välj **Android** under **Plattformar**.
4. Välj **Skicka**.  Du kan inte se något meddelande på Android-enheten ännu eftersom du inte har kört mobilappen på den. När du har kört mobilappen väljer du knappen **Skicka** igen, så visas meddelandet.
5. Visa åtgärdens **resultat** i listan längst ned.

    ![Azure Notification Hubs – Prova att skicka](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)
6. Du ser meddelandet på din enhet. 

    ![Aviseringsmeddelande på enhet](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-on-device.png)
    

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

### <a name="run-the-mobile-app-on-emulator"></a>Kör mobilappen i emulatorn
Om du vill testa push-meddelanden inne i en emulator, måste du se till att emulatorbilden stöder den Google-API-nivå som du har valt för din app. Om avbildningen inte stöder interna Google-API:er kommer processen att avslutas med undantaget **TJÄNSTEN\_ÄR INTE\_TILLGÄNGLIG**.

Du måste även se till att du har lagt till ditt Google-konto i den emulator som körs. Detta gör du under **Inställningar** > **Konton**. Annars kan försöken att registrera med GCM leda till undantaget **AUTENTISERINGEN\_MISSLYCKADES**.

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen har du använt Firebase Cloud Messaging för att skicka meddelanden till Android-enheter. Om du vill veta hur man skickar meddelanden med hjälp av Google Cloud Messaging går du vidare till nästa självstudiekurs:

> [!div class="nextstepaction"]
>[Skicka meddelanden till Android-enheter med hjälp avGoogle Cloud Messaging](notification-hubs-android-push-notification-google-gcm-get-started.md)

<!-- Images. -->

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: https://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: notification-hubs-push-notification-overview.md
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Azure Portal]: https://portal.azure.com
