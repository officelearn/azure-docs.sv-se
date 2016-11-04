---
title: Skicka push-meddelanden till Android med Azure Notification Hubs och Firebase Cloud Messaging | Microsoft Docs
description: I de här självstudierna får du lära dig hur du använder Azure Notification Hubs och Firebase Cloud Messaging för att skicka push-meddelanden till Android-enheter.
services: notification-hubs
documentationcenter: android
keywords: push-meddelanden, push-meddelande, push-meddelande för android, fcm, firebase cloud messaging
author: wesmc7777
manager: erikre
editor: ''

ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: hero-article
ms.date: 07/14/2016
ms.author: wesmc

---
# Skicka push-meddelanden till Android med Azure Notification Hubs
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## Översikt
> [!IMPORTANT]
> Det här ämnet visar push-meddelanden med Google Firebase Cloud Messaging (FCM). Se [Skicka push-meddelanden till Android med Azure Notification Hubs och GCM](notification-hubs-android-push-notification-google-gcm-get-started.md) om du fortfarande använder Googles Cloud Messaging (GCM).
> 
> 

I de här självstudierna får du lära dig hur du använder Azure Notification Hubs och Firebase Cloud Messaging för att skicka push-meddelanden till ett Android-program.
Du skapar en tom Android-app som tar emot push-meddelanden via Firebase Cloud Messaging (FCM).

[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Den slutförda koden för de här självstudierna kan hämtas från GitHub [här](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStartedFirebase).

## Krav
> [!IMPORTANT]
> Du måste ha ett aktivt Azure-konto för att slutföra den här kursen. Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started).
> 
> 

* Förutom det aktiva Azure-konto som nämns ovan, kräver de här självstudierna att du har den senaste versionen av [Android Studio](http://go.microsoft.com/fwlink/?LinkId=389797).
* Android 2.3 eller högre för Firebase Cloud Messaging.
* Google Repository revision 27 eller högre krävs för Firebase Cloud Messaging.
* Google Play Services 9.0.2 eller högre för Firebase Cloud Messaging.
* Du måste slutföra den här kursen innan du börjar någon annan kurs om Notification Hubs för Android-appar.

## Skapa ett nytt Android Studio-projekt
1. Starta ett nytt Android Studio-projekt i Android Studio.
   
    ![Android Studio – nytt projekt](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-new-project.png)
2. Välj formfaktorn för **Telefoner och surfplattor** samt det **Minimum SDK** som du vill stödja. Klicka sedan på **Nästa**.
   
    ![Android Studio – arbetsflöde för att skapa projekt](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-choose-form-factor.png)
3. Välj **Tom aktivitet** som huvudsaklig aktivitet, klicka på **Nästa** och sedan på **Slutför**.

## Skapa ett projekt som har stöd för Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## Konfigurera en ny meddelandehubb
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

&emsp;&emsp;6. På bladet **Inställningar** i din meddelandehubb väljer du **Notification Services** och sedan **Google (GCM)**. Ange den FCM-servernyckel som du kopierade tidigare från [Firebase-konsolen](https://firebase.google.com/console/) och klicka på **Spara**.

&emsp;&emsp;![Azure Notification Hubs – Google (GCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-gcm-api.png)

Din meddelandehubb har nu konfigurerats för att fungera med Firebase Cloud Messaging och du har anslutningssträngar för att registrera din app för att både ta emot och skicka push-meddelanden.

## <a id="connecting-app"></a>Anslut appen till meddelandehubben
### Lägga till Google Play-tjänster till projektet
[!INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

### Lägga till bibliotek för Azure Notification Hubs
1. Lägg till följande rader i avsnittet för **beroenden** i filen `Build.Gradle` för **appen**.
   
        compile 'com.microsoft.azure:notification-hubs-android-sdk:0.4@aar'
        compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
2. Lägg till följande lagringsplats efter avsnittet **beroenden**.
   
        repositories {
            maven {
                url "http://dl.bintray.com/microsoftazuremobile/SDK"
            }
        }

### Uppdatera AndroidManifest.xml.
1. För att kunna stödja FCM måste vi implementera en lyssnartjänst för instans-ID i vår kod. Denna används för att [hämta registreringstoken](https://firebase.google.com/docs/cloud-messaging/android/client#sample-register) med hjälp av [Googles FirebaseInstanceId API](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId). I den här självstudiekursen kommer vi att kalla klassen för `MyInstanceIDService`. 
   
    Lägg till följande tjänstedefinition i filen AndroidManifest.xml inuti taggen `<application>`. 
   
        <service android:name=".MyInstanceIDService">
            <intent-filter>
                <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
            </intent-filter>
        </service>
2. När vi har tagit emot vår registreringstoken för FCM från FirebaseInstanceId API kan den användas för [registrering med Azure Notification Hub](notification-hubs-push-notification-registration-management.md). Vi stöder denna registrering i bakgrunden med en `IntentService` som kallas för `RegistrationIntentService`. Den här tjänsten kommer också att ansvara för att uppdatera vårt FCM-registreringstoken.
   
    Lägg till följande tjänstedefinition i filen AndroidManifest.xml inuti taggen `<application>`. 
   
        <service
            android:name=".RegistrationIntentService"
            android:exported="false">
        </service>
3. Vi kommer också att definiera en mottagare för att ta emot meddelanden. Lägg till följande mottagardefinition i filen AndroidManifest.xml inuti taggen `<application>`. Ersätt platshållaren `<your package>` med det faktiska paketnamnet som visas överst i filen `AndroidManifest.xml`.
   
        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>
4. Lägg till följande nödvändiga FCM-relaterade behörigheter under `</application>`-taggen. Var noga med att ersätta `<your package>` med det paketnamn som visas högst upp i filen `AndroidManifest.xml`.
   
    Mer information om dessa behörigheter finns i [Konfigurera en GCM-klientapp för Android](https://developers.google.com/cloud-messaging/android/client#manifest) och [Migrera en GCM-klientapp för Android till Firebase Cloud Messaging](https://developers.google.com/cloud-messaging/android/android-migrate-fcm#remove_the_permissions_required_by_gcm).
   
        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

### Lägga till kod
1. Expandera  **app** > **src** > **main** > **java** i projektvyn. Högerklicka på paketmappen under **java**, klicka på **Ny** och sedan klickar du på **Java-klass**. Lägg till en ny klass med namnet `NotificationSettings`. 
   
    ![Android Studio – ny Java-klass](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hub-android-new-class.png)
   
    Se till att uppdatera dessa tre platshållare i följande kod för klassen `NotificationSettings`:
   
   * **SenderId**: Det avsändar-ID som du fick tidigare på fliken **Cloud Messaging** i dina projektinställningar i [Firebase-konsolen](https://firebase.google.com/console/).
   * **HubListenConnectionString**: Anslutningssträngen **DefaultListenAccessSignature** för din hubb. Du kan kopiera denna anslutningssträng genom att klicka på **Åtkomstprinciper** i  bladet **Inställningar** i din hubb på [Azure Portal].
   * **HubName**: Använd meddelandehubbens namn. Detta visas i hubbladet på [Azure Portal].
     
     `NotificationSettings` kod:
     
       public class NotificationSettings {
     
           public static String SenderId = "<Your project number>";
           public static String HubName = "<Your HubName>";
           public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
       }
2. Med hjälp av stegen ovan lägger du till ytterligare en ny klass med namnet `MyInstanceIDService`. Det här kommer att bli vår implementering av lyssnartjänsten för instans-ID.
   
    Koden för den här klassen anropar vår `IntentService` för att [uppdatera FCM-tokenen](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) i bakgrunden.
   
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


1. Lägg till ytterligare en ny klass i projektet, med namnet `RegistrationIntentService`. Detta blir implementeringen för vår `IntentService` som hanterar [uppdateringen av FCM-tokenen](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) och [registreringen hos meddelandehubben](notification-hubs-push-notification-registration-management.md).
   
    Använd följande kod för den här klassen.
   
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
   
                    // Storing the registration id that indicates whether the generated token has been
                    // sent to your server. If it is not stored, send the token to your server,
                    // otherwise your server should have already received the token.
                    if (((regID=sharedPreferences.getString("registrationID", null)) == null)){
   
                        NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                                NotificationSettings.HubListenConnectionString, this);
                        Log.d(TAG, "Attempting a new registration with NH using FCM token : " + FCM_token);
                        regID = hub.register(FCM_token).getRegistrationId();
   
                        // If you want to use tags...
                        // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
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
                        // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
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
2. Lägg till följande `import`-uttryck ovanför klassdeklarationen i din `MainActivity`-klass.
   
        import com.google.android.gms.common.ConnectionResult;
        import com.google.android.gms.common.GoogleApiAvailability;
        import com.microsoft.windowsazure.notifications.NotificationsManager;
        import android.content.Intent;
        import android.util.Log;
        import android.widget.TextView;
        import android.widget.Toast;
3. Lägg till följande privata medlemmar högst upp i klassen. Vi använder dessa för att [kontrollera tillgängligheten för Google Play-tjänster, enligt rekommendationer från Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).
   
        public static MainActivity mainActivity;
        public static Boolean isVisible = false;    
        private static final String TAG = "MainActivity";
        private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
4. Lägg till följande metod i tillgängligheten för Google Play-tjänster i klassen `MainActivity`. 
   
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
5. Lägg till följande kod i din `MainActivity`-klass, som gör kontroller för Google Play Service innan din `IntentService` anropas för att hämta ditt registreringstoken för FCM och utföra registreringen på meddelandehubben.
   
        public void registerWithNotificationHubs()
        {
            if (checkPlayServices()) {
                // Start IntentService to register this application with FCM.
                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        }
6. Lägg till följande kod i metoden `OnCreate` för klassen `MainActivity` för att starta registreringsprocessen när aktivitet skapas.
   
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
   
            mainActivity = this;
            NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
            registerWithNotificationHubs();
        }
7. Lägg till de ytterligare metoderna till `MainActivity` för att kontrollera appens status och rapportera statusen i din app.
   
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
8. I metoden `ToastNotify` används kontrollen *”Hello World”* `TextView` för att rapportera status och meddelanden på ett beständigt sätt i appen. Lägg till följande ID för den kontrollen i activity_main.xml.
   
       android:id="@+id/text_hello"
9. Därefter lägger vi till en underklass för den mottagare som vi har definierat i AndroidManifest.xml. Lägg till ytterligare en ny klass i projektet och ge den namnet `MyHandler`.
10. Lägg till följande importuttryck längst upp i `MyHandler.java`:
    
        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.media.RingtoneManager;
        import android.net.Uri;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
        import com.microsoft.windowsazure.notifications.NotificationsHandler;
11. Lägg till följande kod för klassen `MyHandler`. Detta gör den till en underklass för `com.microsoft.windowsazure.notifications.NotificationsHandler`.
    
    Den här koden åsidosätter metoden `OnReceive` vilket i sin tur gör att hanteraren rapporterar meddelanden som tas emot. Hanteraren skickar även push-meddelandena till Android Notification Manager genom att använda metoden `sendNotification()`. Metoden `sendNotification()` ska utföras när appen inte körs och ett meddelande har tagits emot.
    
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
12. På menyraden i Android Studio klickar du på **Skapa** > **Återskapa projekt** för att kontrollera att det inte finns några fel i din kod.
13. Kör appen på din enhet och verifiera att den registreras hos meddelandehubben. 
    
    > [!NOTE]
    > Registreringen kan misslyckas vid första starten tills `onTokenRefresh()`-metoden för instans-ID-tjänst anropas. Uppdateringen ska initiera en lyckad registrering hos meddelandehubben.
    > 
    > 

## Skicka push-meddelanden
Du kan testa att ta emot push-meddelanden i din app genom att skicka dem via [Azure Portal] – Leta upp avsnittet **Felsökning** under hubbladet, som visas nedan.

![Azure Notification Hubs – Prova att skicka](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## (Valfritt) Skicka push-meddelanden direkt från appen
> [!IMPORTANT]
> Det här exemplet för att skicka meddelanden från klientappen tillhandahålls endast i studiesyfte. Eftersom detta kräver att `DefaultFullSharedAccessSignature` ska finnas i klientappen, utsätter den din meddelandehubb för risken att en användare kan komma åt den och skicka obehöriga meddelanden från den till klienterna.
> 
> 

Normalt sett skickar du meddelanden med hjälp av en backend-server. I vissa fall kanske du vill kunna skicka push-meddelanden direkt från klientprogrammet. I det här avsnittet beskrivs hur du skickar meddelanden från klienten med hjälp av [Azure Notification Hub REST API](https://msdn.microsoft.com/library/azure/dn223264.aspx).

1. Expandera **App** > **src** > **main** > **res** > **layout** i projektvyn för Android Studio. Öppna layoutfilen för `activity_main.xml` och klicka på fliken **Text** för att uppdatera filens textinnehåll. Uppdatera den med koden nedan. Detta lägger till nya `Button`- och `EditText`-kontroller för att skicka push-meddelanden till meddelandehubben. Lägg till den här koden längst ned, precis före `</RelativeLayout>`.
   
        <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/send_button"
        android:id="@+id/sendbutton"
        android:layout_centerVertical="true"
        android:layout_centerHorizontal="true"
        android:onClick="sendNotificationButtonOnClick" />
   
        <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessage"
        android:layout_above="@+id/sendbutton"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="42dp"
        android:hint="@string/notification_message_hint" />
2. Expandera **App** > **src** > **main** > **res** > **värden** i projektvyn för Android Studio. Öppna filen `strings.xml` och lägg till strängvärden som refererar till de nya `Button`- och `EditText`-kontrollerna. Lägg till dessa längst ned i filen, precis före `</resources>`.
   
        <string name="send_button">Send Notification</string>
        <string name="notification_message_hint">Enter notification message text</string>
3. Lägg till följande inställning till klassen `NotificationSettings` i filen `NotificationSetting.java`.
   
    Uppdatera `HubFullAccess` med anslutningssträngen **DefaultFullSharedAccessSignature** för hubben. Den här anslutningssträngen kan kopieras från [Azure Portal] genom att klicka på **Åtkomstprinciper** i bladet **Inställningar** för meddelandehubben.
   
        public static String HubFullAccess = "<Enter Your DefaultFullSharedAccessSignature Connection string>";
4. Lägg till följande `import`-uttryck ovanför klassen `MainActivity` i filen `MainActivity.java`.
   
        import java.io.BufferedOutputStream;
        import java.io.BufferedReader;
        import java.io.InputStreamReader;
        import java.io.OutputStream;
        import java.net.HttpURLConnection;
        import java.net.URL;
        import java.net.URLEncoder;
        import javax.crypto.Mac;
        import javax.crypto.spec.SecretKeySpec;
        import android.util.Base64;
        import android.view.View;
        import android.widget.EditText;
5. Lägg till följande medlemmar högst upp i klassen `MainActivity` i filen `MainActivity.java`.  
   
        private String HubEndpoint = null;
        private String HubSasKeyName = null;
        private String HubSasKeyValue = null;
6. Du måste skapa en SaS-token (Software Access Signature) om du vill autentisera en POST-begäran för att skicka meddelanden till meddelandehubben. Detta görs genom att parsa nyckeluppgifterna från anslutningssträngen och sedan skapa ett SaS-token. Detta anges i REST-API-referensen [Vanliga koncept](http://msdn.microsoft.com/library/azure/dn495627.aspx). Följande kod är ett exempel på en implementering.
   
    I `MainActivity.java` lägger du till följande metod i klassen `MainActivity` för att parsa anslutningssträngen.
   
        /**
         * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx
         * to parse the connection string so a SaS authentication token can be
         * constructed.
         *
         * @param connectionString This must be the DefaultFullSharedAccess connection
         *                         string for this example.
         */
        private void ParseConnectionString(String connectionString)
        {
            String[] parts = connectionString.split(";");
            if (parts.length != 3)
                throw new RuntimeException("Error parsing connection string: "
                        + connectionString);
   
            for (int i = 0; i < parts.length; i++) {
                if (parts[i].startsWith("Endpoint")) {
                    this.HubEndpoint = "https" + parts[i].substring(11);
                } else if (parts[i].startsWith("SharedAccessKeyName")) {
                    this.HubSasKeyName = parts[i].substring(20);
                } else if (parts[i].startsWith("SharedAccessKey")) {
                    this.HubSasKeyValue = parts[i].substring(16);
                }
            }
        }
7. I `MainActivity.java` lägger du till följande metod i klassen `MainActivity` för att skapa ett SaS-token för autentisering.
   
        /**
         * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx to
         * construct a SaS token from the access key to authenticate a request.
         *
         * @param uri The unencoded resource URI string for this operation. The resource
         *            URI is the full URI of the Service Bus resource to which access is
         *            claimed. For example,
         *            "http://<namespace>.servicebus.windows.net/<hubName>"
         */
        private String generateSasToken(String uri) {
   
            String targetUri;
            String token = null;
            try {
                targetUri = URLEncoder
                        .encode(uri.toString().toLowerCase(), "UTF-8")
                        .toLowerCase();
   
                long expiresOnDate = System.currentTimeMillis();
                int expiresInMins = 60; // 1 hour
                expiresOnDate += expiresInMins * 60 * 1000;
                long expires = expiresOnDate / 1000;
                String toSign = targetUri + "\n" + expires;
   
                // Get an hmac_sha1 key from the raw key bytes
                byte[] keyBytes = HubSasKeyValue.getBytes("UTF-8");
                SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");
   
                // Get an hmac_sha1 Mac instance and initialize with the signing key
                Mac mac = Mac.getInstance("HmacSHA256");
                mac.init(signingKey);
   
                // Compute the hmac on input data bytes
                byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));
   
                // Using android.util.Base64 for Android Studio instead of
                // Apache commons codec
                String signature = URLEncoder.encode(
                        Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");
   
                // Construct authorization string
                token = "SharedAccessSignature sr=" + targetUri + "&sig="
                        + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
            } catch (Exception e) {
                if (isVisible) {
                    ToastNotify("Exception Generating SaS : " + e.getMessage().toString());
                }
            }
   
            return token;
        }
8. I `MainActivity.java` lägger du till följande metod för klassen `MainActivity` för att hantera knappen **Skicka meddelande**. Klicka på och skicka push-meddelandet till hubben med hjälp av det inbyggda REST-API:et.
   
        /**
         * Send Notification button click handler. This method parses the
         * DefaultFullSharedAccess connection string and generates a SaS token. The
         * token is added to the Authorization header on the POST request to the
         * notification hub. The text in the editTextNotificationMessage control
         * is added as the JSON body for the request to add a GCM message to the hub.
         *
         * @param v
         */
        public void sendNotificationButtonOnClick(View v) {
            EditText notificationText = (EditText) findViewById(R.id.editTextNotificationMessage);
            final String json = "{\"data\":{\"message\":\"" + notificationText.getText().toString() + "\"}}";
   
            new Thread()
            {
                public void run()
                {
                    try
                    {
                        // Based on reference documentation...
                        // http://msdn.microsoft.com/library/azure/dn223273.aspx
                        ParseConnectionString(NotificationSettings.HubFullAccess);
                        URL url = new URL(HubEndpoint + NotificationSettings.HubName +
                                "/messages/?api-version=2015-01");
   
                        HttpURLConnection urlConnection = (HttpURLConnection)url.openConnection();
   
                        try {
                            // POST request
                            urlConnection.setDoOutput(true);
   
                            // Authenticate the POST request with the SaS token
                            urlConnection.setRequestProperty("Authorization", 
                                generateSasToken(url.toString()));
   
                            // Notification format should be GCM
                            urlConnection.setRequestProperty("ServiceBusNotification-Format", "gcm");
   
                            // Include any tags
                            // Example below targets 3 specific tags
                            // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
                            // urlConnection.setRequestProperty("ServiceBusNotification-Tags", 
                            //      "tag1 || tag2 || tag3");
   
                            // Send notification message
                            urlConnection.setFixedLengthStreamingMode(json.length());
                            OutputStream bodyStream = new BufferedOutputStream(urlConnection.getOutputStream());
                            bodyStream.write(json.getBytes());
                            bodyStream.close();
   
                            // Get reponse
                            urlConnection.connect();
                            int responseCode = urlConnection.getResponseCode();
                            if ((responseCode != 200) && (responseCode != 201)) {
                                BufferedReader br = new BufferedReader(new InputStreamReader((urlConnection.getErrorStream())));
                                String line;
                                StringBuilder builder = new StringBuilder("Send Notification returned " +
                                        responseCode + " : ")  ;
                                while ((line = br.readLine()) != null) {
                                    builder.append(line);
                                }
   
                                ToastNotify(builder.toString());
                            }
                        } finally {
                            urlConnection.disconnect();
                        }
                    }
                    catch(Exception e)
                    {
                        if (isVisible) {
                            ToastNotify("Exception Sending Notification : " + e.getMessage().toString());
                        }
                    }
                }
            }.start();
        }

## Testa din app
#### Push-meddelanden i emulatorn
Om du vill testa push-meddelanden inne i en emulator, måste du se till att emulatorbilden stöder den Google-API-nivå som du har valt för din app. Om bilden inte stöder interna Google-API:er, kommer processen att avslutas med undantaget **TJÄNSTEN\_ÄR INTE\_TILLGÄNGLIG**.

Förutom det som nämns ovan måste du se till att du har lagt till ditt Google-konto i den emulator som körs. Detta görs under **Inställningar** > **Konton**. Annars kan försöken att registrera med GCM leda till undantaget **AUTENTISERINGEN\_MISSLYCKADES**.

#### Köra programmet
1. Kör appen och lägg märke till att ett registrerings-ID rapporteras vid en lyckad registrering.
   
    ![Tester på Android – Kanalregistrering](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-registered.png)
2. Ange ett meddelande som ska skickas till alla Android-enheter som har registrerats på hubben.
   
    ![Tester på Android – skicka ett meddelande](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-set-message.png)
3. Tryck på **Skicka meddelande**. Alla enheter där appen körs kommer att visa en `AlertDialog` instans med push-meddelandet. Enheter där appen inte körs men som tidigare hade registrerats för att ta emot push-meddelanden, kommer att få ett meddelande i Android Notification Manager. Dessa kan visas genom att svepa nedåt från det övre vänstra hörnet.
   
    ![Tester på Android – meddelanden](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-received-message.png)

## Nästa steg
Vi rekommenderar att du går vidare med självstudiekursen [Använda Notification Hubs för att skicka push-meddelanden till användare] som nästa steg. Den visar hur du skickar meddelanden från en ASP.NET-backend med hjälp av taggar för att rikta in dig på vissa specifika användare.

Om du vill dela in användarna efter intressegrupper, kan du gå till självstudiekursen [Använda Notification Hubs för att skicka de senaste nyheterna].

Om du vill få mer allmän information om Notification Hubs kan du läsa våra [Riktlinjer om Notification Hubs].

<!-- Images. -->



<!-- URLs. -->
[Komma igång med push-meddelanden i Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referera till ett biblioteksprojekt]: http://go.microsoft.com/fwlink/?LinkId=389800
[klassiska Azure-portalen]: https://manage.windowsazure.com/
[Riktlinjer om Notification Hubs]: notification-hubs-push-notification-overview.md
[Använda Notification Hubs för att skicka push-meddelanden till användare]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Använda Notification Hubs för att skicka de senaste nyheterna]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Azure Portal]: https://portal.azure.com



<!--HONumber=Sep16_HO3-->


