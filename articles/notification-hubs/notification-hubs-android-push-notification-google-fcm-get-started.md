---
title: Skicka meddelanden till Android-appar med Azure Notification Hubs och Firebase Cloud Messaging | Microsoft Docs
description: I de här självstudierna lär du dig att använda Azure Notification Hubs och Google Firebase Cloud Messaging för att skicka push-meddelanden till Android-enheter.
services: notification-hubs
documentationcenter: android
keywords: push-meddelanden, push-meddelande, push-meddelande för android, fcm, firebase cloud messaging
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 02298560-da61-4bbb-b07c-e79bd520e420
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 0ec429407324210fca824ebd0734ddb39bce56ac
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51256569"
---
# <a name="tutorial-push-notifications-to-android-devices-by-using-azure-notification-hubs-and-google-firebase-cloud-messaging"></a>Självstudier: Skicka meddelanden till Android-enheter med hjälp av Azure Notification Hubs och Google Firebase Cloud Messaging
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

> [!IMPORTANT]
> Den här artikeln visar push-meddelanden med Google Firebase Cloud Messaging (FCM). Se [Skicka meddelanden till Android-enheter med Azure Notification Hubs och GCM](notification-hubs-android-push-notification-google-gcm-get-started.md) om du fortfarande använder Googles Cloud Messaging (GCM).

I de här självstudierna får du lära dig hur du använder Azure Notification Hubs och Firebase Cloud Messaging för att skicka meddelanden till ett Android-program. I den här självstudien skapar du en tom Android-app som tar emot push-meddelanden via Firebase Cloud Messaging (FCM).

Den slutförda koden för den här självstudiekursen kan hämtas från GitHub [här](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStartedFirebase).

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa ett Android Studio-projekt.
> * Skapa ett Firebase-projekt som har stöd för Firebase Cloud Messaging.
> * Skapa en meddelandehubb.
> * Anslut appen till meddelandehubben.
> * Testa appen. 

## <a name="prerequisites"></a>Nödvändiga komponenter
Du måste ha ett aktivt Azure-konto för att slutföra den här kursen. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/free/).

* Förutom det aktiva Azure-konto som nämns ovan, kräver de här självstudierna att du har den senaste versionen av [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797).
* Android 2.3 eller högre för Firebase Cloud Messaging.
* Google Repository revision 27 eller högre krävs för Firebase Cloud Messaging.
* Google Play Services 9.0.2 eller högre för Firebase Cloud Messaging.
* Du måste slutföra den här kursen innan du börjar någon annan kurs om Notification Hubs för Android-appar.

## <a name="create-an-android-studio-project"></a>Skapa ett Android Studio-projekt
1. Starta ett nytt Android Studio-projekt i Android Studio.
   
    ![Android Studio – nytt projekt](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-new-project.png)
2. Välj formfaktorn för **Telefoner och surfplattor** samt det **Minimum SDK** som du vill stödja. Klicka sedan på **Nästa**.
   
    ![Android Studio – arbetsflöde för att skapa projekt](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-choose-form-factor.png)
3. Välj **Tom aktivitet** som huvudsaklig aktivitet, klicka på **Nästa** och sedan på **Slutför**.

## <a name="create-a-firebase-project-that-supports-fcm"></a>Skapa ett Firebase-projekt som har stöd för FCM
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Konfigurera en meddelandehubb
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>Konfigurera Firebase Cloud Messaging-inställningar för hubben
1. Välj **Google (GCM)** i kategorin **MEDDELANDEINSTÄLLNINGAR**. 
2. Ange den API-nyckel (FCM-servernyckel) som du kopierade tidigare från [Firebase-konsolen](https://firebase.google.com/console/).
3. Välj **Spara** i verktygsfältet.

    ![Azure Notification Hubs – Google (GCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-gcm-api.png)

Din meddelandehubb har nu konfigurerats för att fungera med Firebase Cloud Messaging och du har anslutningssträngar för att registrera din app för att både ta emot och skicka push-meddelanden.

## <a id="connecting-app"></a>Anslut appen till meddelandehubben
### <a name="add-google-play-services-to-the-project"></a>Lägga till Google Play-tjänster till projektet
[!INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

### <a name="adding-azure-notification-hubs-libraries"></a>Lägga till bibliotek för Azure Notification Hubs
1. Lägg till följande rader i avsnittet för **beroenden** i filen `Build.Gradle` för **appen**.
   
    ```java
        compile 'com.microsoft.azure:notification-hubs-android-sdk:0.4@aar'
        compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```

2. Lägg till följande lagringsplats efter avsnittet **beroenden**.
   
    ```java
        repositories {
            maven {
                url "http://dl.bintray.com/microsoftazuremobile/SDK"
            }
        }
    ```

### <a name="add-google-firebase-support"></a>Lägg till stöd för Google Firebase

1. Lägg till följande rader i avsnittet för **beroenden** i filen `Build.Gradle` för **appen**.
   
    ```java
        compile 'com.google.firebase:firebase-core:12.0.0'
    ```

2. Lägg till följande plugin i slutet av filen. 
   
    ```java
    apply plugin: 'com.google.gms.google-services'
    ```

### <a name="updating-the-androidmanifestxml"></a>Uppdatera AndroidManifest.xml.
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

3. Du kommer också att definiera en mottagare för att ta emot meddelanden. Lägg till följande mottagardefinition i filen AndroidManifest.xml inuti taggen `<application>`. Ersätt platshållaren `<your package>` med det faktiska paketnamnet som visas överst i filen `AndroidManifest.xml`.

    ```xml
        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>
    ```

4. Lägg till följande nödvändiga FCM-relaterade behörigheter under `</application>`-taggen. 
   
    Mer information om dessa behörigheter finns i [Konfigurera en GCM-klientapp för Android](https://developers.google.com/cloud-messaging/android/client#manifest) och [Migrera en GCM-klientapp för Android till Firebase Cloud Messaging](https://developers.google.com/cloud-messaging/android/android-migrate-fcm#remove_the_permissions_required_by_gcm).
   
    ```xml
        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="adding-code"></a>Lägga till kod
1. Expandera  **app** > **src** > **main** > **java** i projektvyn. Högerklicka på paketmappen under **java**, klicka på **Ny** och sedan klickar du på **Java-klass**. Lägg till en ny klass med namnet `NotificationSettings`. 
   
    ![Android Studio – ny Java-klass](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hub-android-new-class.png)
   
    Se till att uppdatera dessa tre platshållare i följande kod för klassen `NotificationSettings`:
   
   * **SenderId**: Det avsändar-ID som du fick tidigare på fliken **Cloud Messaging** i dina projektinställningar i [Firebase-konsolen](https://firebase.google.com/console/).
   * **HubListenConnectionString**: Anslutningssträngen **DefaultListenAccessSignature** för din hubb. Du kan kopiera denna anslutningssträng genom att klicka på **Åtkomstprinciper** i din hubb på [Azure Portal].
   * **HubName**: Använd meddelandehubbens namn som visas på hubbsidan i [Azure Portal].
     
     `NotificationSettings` kod:
     
    ```java
       public class NotificationSettings {
     
           public static String SenderId = "<Your project number>";
           public static String HubName = "<Your HubName>";
           public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
       }
    ```

2. Med hjälp av de föregående stegen lägger du till ytterligare en ny klass med namnet `MyInstanceIDService`. Den här klassen är din implementering av lyssnartjänsten för instans-ID.
   
    Koden för den här klassen anropar din `IntentService` för att [uppdatera FCM-token](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) i bakgrunden.
   
    ```java
        import android.content.Intent;
        import android.util.Log;
        import com.google.firebase.iid.FirebaseInstanceIdService;

        public class MyInstanceIDService extends FirebaseInstanceIdService {

            private static final String TAG = "MyInstanceIDService";

            @Override
            public void onTokenRefresh() {

                Log.d(TAG, "Refreshing GCM Registration Token");

                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        };
    ```

1. Lägg till ytterligare en ny klass i projektet, med namnet `RegistrationIntentService`. Den här klassen implementerar `IntentService`-gränssnittet och hanterar [uppdateringen av FCM-token](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) och [registreringen hos meddelandehubben](notification-hubs-push-notification-registration-management.md).
   
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

2. Lägg till följande `import`-uttryck ovanför klassdeklarationen i din `MainActivity`-klass.
   
    ```java
        import com.google.android.gms.common.ConnectionResult;
        import com.google.android.gms.common.GoogleApiAvailability;
        import com.microsoft.windowsazure.notifications.NotificationsManager;
        import android.content.Intent;
        import android.util.Log;
        import android.widget.TextView;
        import android.widget.Toast;
    ```
3. Lägg till följande privata medlemmar högst upp i klassen. Du använder de här fälten för att [kontrollera tillgängligheten för Google Play-tjänster, enligt rekommendationer från Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).
   
    ```java
        public static MainActivity mainActivity;
        public static Boolean isVisible = false;    
        private static final String TAG = "MainActivity";
        private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    ```

4. Lägg till följande metod i tillgängligheten för Google Play-tjänster i klassen `MainActivity`. 
   
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

5. Lägg till följande kod i din `MainActivity`-klass, som gör kontroller för Google Play Service innan din `IntentService` anropas för att hämta ditt registreringstoken för FCM och utföra registreringen på meddelandehubben.
   
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

6. Lägg till följande kod i metoden `OnCreate` för klassen `MainActivity` för att starta registreringsprocessen när aktivitet skapas.
   
    ```java
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
   
            mainActivity = this;
            NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
            registerWithNotificationHubs();
        }
    ```

7. Lägg till de ytterligare metoderna till `MainActivity` för att kontrollera appens status och rapportera statusen i din app.
   
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

8. I metoden `ToastNotify` används kontrollen *”Hello World”* `TextView` för att rapportera status och meddelanden på ett beständigt sätt i appen. Lägg till följande ID för den kontrollen i activity_main.xml.
   
    ```java
       android:id="@+id/text_hello"
    ```

9. Därefter lägger du till en underklass för den mottagare som du har definierat i AndroidManifest.xml. Lägg till ytterligare en ny klass i projektet och ge den namnet `MyHandler`.
10. Lägg till följande importuttryck längst upp i `MyHandler.java`:
    
    ```java
        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.media.RingtoneManager;
        import android.net.Uri;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
        import com.microsoft.windowsazure.notifications.NotificationsHandler;
    ```

11. Lägg till följande kod för klassen `MyHandler`. Detta gör den till en underklass för `com.microsoft.windowsazure.notifications.NotificationsHandler`.
    
    Den här koden åsidosätter metoden `OnReceive` vilket i sin tur gör att hanteraren rapporterar meddelanden som tas emot. Hanteraren skickar även push-meddelandena till Android Notification Manager genom att använda metoden `sendNotification()`. Metoden `sendNotification()` ska utföras när appen inte körs och ett meddelande har tagits emot.
    
    ```java
        public class MyHandler extends NotificationsHandler {
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
                NotificationCompat.Builder mBuilder =
                        new NotificationCompat.Builder(ctx)
                                .setSmallIcon(R.mipmap.ic_launcher)
                                .setContentTitle("Notification Hub Demo")
                                .setStyle(new NotificationCompat.BigTextStyle()
                                        .bigText(msg))
                                .setSound(defaultSoundUri)
                                .setContentText(msg);
    
                mBuilder.setContentIntent(contentIntent);
                mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
            }
        }
    ```

12. På menyraden i Android Studio klickar du på **Skapa** > **Återskapa projekt** för att kontrollera att det inte finns några fel i din kod.
13. Kör appen på din enhet och verifiera att den registreras hos meddelandehubben. 
    
    > [!NOTE]
    > Registreringen kan misslyckas vid första starten tills `onTokenRefresh()`-metoden för instans-ID-tjänst anropas. Uppdateringen bör initiera en lyckad registrering hos meddelandehubben.
    > 
    > 

## <a name="test-the-app"></a>Testa appen
### <a name="test-send-notification-from-the-notification-hub"></a>Testa att skicka ett meddelande från meddelandehubben
Du kan skicka push-meddelanden från [Azure Portal] på följande sätt: 

1. Välj **Testskicka** i avsnittet **Felsökning**.
2. Välj **Android** under **Plattformar**. 
3. Välj **Skicka**.  Du kan inte se något meddelande på Android-enheten ännu eftersom du inte har kört mobilappen på den. När du har kört mobilappen väljer du knappen **Skicka** igen, så visas meddelandet. 
4. Visa åtgärdens **resultat** i listan längst ned. 

    ![Azure Notification Hubs – Prova att skicka](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)


[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


### <a name="run-the-mobile-app"></a>Kör mobilappen
Om du vill testa push-meddelanden inne i en emulator, måste du se till att emulatorbilden stöder den Google-API-nivå som du har valt för din app. Om avbildningen inte stöder interna Google-API:er kommer processen att avslutas med undantaget **TJÄNSTEN\_ÄR INTE\_TILLGÄNGLIG**.

Du måste även se till att du har lagt till ditt Google-konto i den emulator som körs. Detta gör du under **Inställningar** > **Konton**. Annars kan försöken att registrera med GCM leda till undantaget **AUTENTISERINGEN\_MISSLYCKADES**.

1. Kör appen och lägg märke till att ett registrerings-ID rapporteras vid en lyckad registrering.
   
    ![Tester på Android – Kanalregistrering](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-registered.png)
2. Ange ett meddelande som ska skickas till alla Android-enheter som har registrerats på hubben.
   
    ![Tester på Android – skicka ett meddelande](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-set-message.png)
3. Tryck på **Skicka meddelande**. Alla enheter där appen körs visar en `AlertDialog`-instans med push-meddelandet. Enheter där appen inte körs men som tidigare hade registrerats för att ta emot push-meddelanden får ett meddelande i Android Notification Manager. Du kan visa meddelandena genom att svepa nedåt från det övre vänstra hörnet.
   
    ![Tester på Android – meddelanden](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-received-message.png)

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen har du använt Firebase Cloud Messaging för att skicka meddelanden till Android-enheter. Om du vill veta hur man skickar meddelanden med hjälp av Google Cloud Messaging går du vidare till nästa självstudiekurs: 

> [!div class="nextstepaction"]
>[Skicka meddelanden till Android-enheter med hjälp avGoogle Cloud Messaging](notification-hubs-android-push-notification-google-gcm-get-started.md)


<!-- Images. -->


<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: notification-hubs-push-notification-overview.md
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Azure Portal]: https://portal.azure.com
