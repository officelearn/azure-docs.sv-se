---
title: "Kom igång med Azure Notification Hubs för Kindle-appar | Microsoft Docs"
description: "I den här självstudiekursen kommer du att få lära dig hur du använder Azure Notification Hubs för att skicka push-meddelanden till en Kindle-app."
services: notification-hubs
documentationcenter: 
author: ysxu
manager: erikre
editor: 
ms.assetid: 346fc8e5-294b-4e4f-9f27-7a82d9626e93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-kindle
ms.devlang: Java
ms.topic: hero-article
ms.date: 06/29/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7206f152ed7270abc62536a9ee164f7227833bcc


---
# <a name="get-started-with-notification-hubs-for-kindle-apps"></a>Kom igång med Notification Hubs för Kindle-appar
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Översikt
I den här självstudiekursen beskrivs hur du använder Azure Notification Hubs för att skicka push-meddelanden till en Kindle-app.
Du skapar en tom Kindle-app som tar emot push-meddelanden genom att använda Amazon Device Messaging (ADM).

## <a name="prerequisites"></a>Krav
För den här kursen behöver du följande:

* Hämta Android SDK (vi antar att du kommer att använda Eclipse) från <a href="http://go.microsoft.com/fwlink/?LinkId=389797">webbplatsen för Android</a>.
* Följ stegen i <a href="https://developer.amazon.com/appsandservices/resources/development-tools/ide-tools/tech-docs/01-setting-up-your-development-environment">Konfigurera din utvecklingsmiljö</a> för att ställa in din utvecklingsmiljö för Kindle.

## <a name="add-a-new-app-to-the-developer-portal"></a>Lägga till en ny app i utvecklarportalen
1. Börja med att skapa en app i [Amazon Developer-portalen].
   
    ![][0]
2. Kopiera **programnyckeln**.
   
    ![][1]
3. Klicka på namnet på din app i portalen och klicka sedan på fliken **Device Messaging**.
   
    ![][2]
4. Klicka på **Skapa en ny säkerhetsprofil** och skapa sedan en ny säkerhetsprofil (till exempel **säkerhetsprofilen TestAdm**). Klicka sedan på **Spara**.
   
    ![][3]
5. Klicka på **Säkerhetsprofiler** för att visa den säkerhetsprofil som du nyss har skapat. Kopiera värdena för **klient-ID** och **klienthemlighet** för användning längre fram.
   
    ![][4]

## <a name="create-an-api-key"></a>Skapa en API-nyckel.
1. Öppna en kommandotolk med administratörsbehörighet.
2. Navigera till mappen Android SDK.
3. Ange följande kommando:
   
        keytool -list -v -alias androiddebugkey -keystore ./debug.keystore
   
    ![][5]
4. Ange **android** som lösenord för **keystore**.
5. Kopiera **MD5**-fingeravtrycket.
6. När du har återgått till utvecklarportalen klickar du på **Android/Kindle** på fliken **Messaging** och anger paketnamnet för din app (till exempel **com.sample.notificationhubtest**) och **MD5**-värdet. Sedan klickar du på **Generera API-nyckel**.

## <a name="add-credentials-to-the-hub"></a>Lägga till autentiseringsuppgifter i hubben
Lägg till klienthemligheten och klient-ID:t i portalen på fliken **Konfigurera** i din meddelandehubb.

## <a name="set-up-your-application"></a>Konfigurera din app
> [!NOTE]
> När du skapar en app bör du använda minst API-nivå 17.
> 
> 

Lägg till ADM-biblioteket i Eclipse-projektet:

1. [Ladda ned SDK] för att hämta ADM-biblioteket. Packa upp zip-filen för SDK.
2. Högerklicka på projektet i Eclipse och klicka sedan på **Egenskaper**. Välj **Java Build Path** till vänster och sedan fliken **Bibliotek** högst upp. Klicka på **Lägg till extern Jar** och markera filen `\SDK\Android\DeviceMessaging\lib\amazon-device-messaging-*.jar` i katalogen där du extraherade Amazon SDK.
3. Hämta NotificationHubs Android SDK (länk).
4. Packa upp paketet och dra sedan filen `notification-hubs-sdk.jar` till `libs`-mappen i Eclipse.

Redigera ditt app-manifest för att stödja ADM:

1. Lägg till namnområdet för Amazon i rotelementets manifest:

        xmlns:amazon="http://schemas.amazon.com/apk/res/android"

1. Lägg till behörigheter som det första elementet under manifestelementet. Ersätt **[YOUR PACKAGE NAME]** med det paket som du använde för att skapa appen.
   
        <permission
         android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
         android:protectionLevel="signature" />
   
        <uses-permission android:name="android.permission.INTERNET"/>
   
        <uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />
   
        <!-- This permission allows your app access to receive push notifications
        from ADM. -->
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />
   
        <!-- ADM uses WAKE_LOCK to keep the processor from sleeping when a message is received. -->
        <uses-permission android:name="android.permission.WAKE_LOCK" />
2. Infoga följande element som app-elementets första underordnade. Kom ihåg att ersätta **[YOUR SERVICE NAME]** med namnet på din  meddelandehanterare för ADM som du skapar i nästa avsnitt (inklusive paketet). Ersätt även **[YOUR PACKAGE NAME]** med det paketnamn som du skapade din app med.
   
        <amazon:enable-feature
              android:name="com.amazon.device.messaging"
                     android:required="true"/>
        <service
            android:name="[YOUR SERVICE NAME]"
            android:exported="false" />
   
        <receiver
            android:name="[YOUR SERVICE NAME]$Receiver" />
   
            <!-- This permission ensures that only ADM can send your app registration broadcasts. -->
            android:permission="com.amazon.device.messaging.permission.SEND" >
   
            <!-- To interact with ADM, your app must listen for the following intents. -->
            <intent-filter>
          <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
          <action android:name="com.amazon.device.messaging.intent.RECEIVE" />
   
          <!-- Replace the name in the category tag with your app's package name. -->
          <category android:name="[YOUR PACKAGE NAME]" />
            </intent-filter>
        </receiver>

## <a name="create-your-adm-message-handler"></a>Skapa en meddelandehanterare för ADM
1. Skapa en ny klass som ärver från `com.amazon.device.messaging.ADMMessageHandlerBase` och ge den namnet `MyADMMessageHandler`, som visas på följande bild:
   
    ![][6]
2. Lägg till följande `import`-uttryck:
   
        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.support.v4.app.NotificationCompat;
        import com.amazon.device.messaging.ADMMessageReceiver;
        import com.microsoft.windowsazure.messaging.NotificationHub
3. Lägg till följande kod i den klass som du har skapat. Kom ihåg att ersätta hubbnamnet och anslutningssträngen (lyssna):
   
        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
          private static NotificationHub hub;
        public static NotificationHub getNotificationHub(Context context) {
            Log.v("com.wa.hellokindlefire", "getNotificationHub");
            if (hub == null) {
                hub = new NotificationHub("[hub name]", "[listen connection string]", context);
            }
            return hub;
        }
   
        public MyADMMessageHandler() {
                super("MyADMMessageHandler");
            }
   
            public static class Receiver extends ADMMessageReceiver
            {
                public Receiver()
                {
                    super(MyADMMessageHandler.class);
                }
            }
   
            private void sendNotification(String msg) {
                Context ctx = getApplicationContext();
   
                mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);
   
            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                  new Intent(ctx, MainActivity.class), 0);
   
            NotificationCompat.Builder mBuilder =
                  new NotificationCompat.Builder(ctx)
                  .setSmallIcon(R.mipmap.ic_launcher)
                  .setContentTitle("Notification Hub Demo")
                  .setStyle(new NotificationCompat.BigTextStyle()
                         .bigText(msg))
                  .setContentText(msg);
   
             mBuilder.setContentIntent(contentIntent);
             mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
        }
4. Lägg till följande kod i `OnMessage()`-metoden:
   
        String nhMessage = intent.getExtras().getString("msg");
        sendNotification(nhMessage);
5. Lägg till följande kod i `OnRegistered`-metoden:
   
            try {
        getNotificationHub(getApplicationContext()).register(registrationId);
            } catch (Exception e) {
        Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
            }
6. Lägg till följande kod i `OnUnregistered`-metoden:
   
         try {
             getNotificationHub(getApplicationContext()).unregister();
         } catch (Exception e) {
             Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
         }
7. Lägg till följande importuttryck i `MainActivity`-metoden:
   
        import com.amazon.device.messaging.ADM;
8. Lägg till följande kod i slutet av `OnCreate`-metoden:
   
        final ADM adm = new ADM(this);
        if (adm.getRegistrationId() == null)
        {
           adm.startRegister();
        } else {
            new AsyncTask() {
                  @Override
                  protected Object doInBackground(Object... params) {
                     try {                         MyADMMessageHandler.getNotificationHub(getApplicationContext()).register(adm.getRegistrationId());
                     } catch (Exception e) {
                         Log.e("com.wa.hellokindlefire", "Failed registration with hub", e);
                         return e;
                     }
                     return null;
                 }
               }.execute(null, null, null);
        }

## <a name="add-your-api-key-to-your-app"></a>Lägg till API-nyckeln i din app
1. I Eclipse skapar du en ny fil med namnet **api_key.txt** i ditt projekts katalogtillgångar.
2. Öppna filen och kopiera den API-nyckel som du genererade på Amazon Developer-portalen.

## <a name="run-the-app"></a>Kör appen
1. Starta emulatorn.
2. I emulatorn sveper du uppifrån och klickar på **Inställningar**. Sedan klickar du på **Mitt konto** och registrerar dig med ett giltigt Amazon-konto.
3. Kör appen i Eclipse.

> [!NOTE]
> Om problem uppstår, kontrollerar du tiden för emulatorn (eller enheten). Tidsvärdet måste vara korrekt. Om du vill ändra Kindle-emulatorns tid kan du köra följande kommando från katalogen för plattformsverktygen för Android SDK:
> 
> 

        adb shell  date -s "yyyymmdd.hhmmss"

## <a name="send-a-message"></a>Skicka ett meddelande
Att skicka ett meddelande med hjälp av .NET:

        static void Main(string[] args)
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

            hub.SendAdmNativeNotificationAsync("{\"data\":{\"msg\" : \"Hello from .NET!\"}}").Wait();
        }

![][7]

<!-- URLs. -->
[Amazon Developer-portalen]: https://developer.amazon.com/home.html
[Ladda ned SDK]: https://developer.amazon.com/public/resources/development-tools/sdk

[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png



<!--HONumber=Nov16_HO2-->


