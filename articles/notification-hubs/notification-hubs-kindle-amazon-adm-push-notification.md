---
title: Skicka meddelanden till Kindle-appar med hjälp av Azure Notification Hubs | Microsoft Docs
description: I den här självstudiekursen kommer du att få lära dig hur du använder Azure Notification Hubs för att skicka push-meddelanden till en Kindle-app.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 346fc8e5-294b-4e4f-9f27-7a82d9626e93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-kindle
ms.devlang: Java
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/29/2019
ms.author: jowargo
ms.openlocfilehash: edd0e12460e07cfd2990cc43a9056ed06b84fb1d
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927020"
---
# <a name="get-started-with-notification-hubs-for-kindle-apps"></a>Kom igång med Notification Hubs för Kindle-appar

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

I den här självstudiekursen beskrivs hur du använder Azure Notification Hubs för att skicka push-meddelanden till en Kindle-app. Du skapar en tom Kindle-app som tar emot push-meddelanden genom att använda Amazon Device Messaging (ADM).

I den här självstudiekursen får du skapa/uppdatera kod för att utföra följande uppgifter:

> [!div class="checklist"]
> * Lägga till en ny app i utvecklarportalen
> * Skapa en API-nyckel.
> * Skapa och konfigurera en notification hub
> * Konfigurera din app
> * Skapa en meddelandehanterare för ADM
> * Lägg till API-nyckeln i din app
> * Kör appen
> * Skicka ett testmeddelande

## <a name="prerequisites"></a>Nödvändiga komponenter

- [Android Studio](https://developer.android.com/studio/?gclid=CjwKCAjwwZrmBRA7EiwA4iMzBPZ9YYmR0pbb5LtjnWhWCxe8PWrmjmeaR6ad5ksCr_j2mmkVj_-o6hoCAqwQAvD_BwE).
- Följ stegen i [Konfigurera din utvecklingsmiljö](https://developer.amazon.com/docs/fire-tablets/ft-set-up-your-development-environment.html) för att ställa in din utvecklingsmiljö för Kindle.

## <a name="add-a-new-app-to-the-developer-portal"></a>Lägga till en ny app i utvecklarportalen

1. Logga in på den [Amazon Developer-portalen](https://developer.amazon.com/apps-and-games/console/apps/list.html).
2. Välj **Lägg till ny App**, och välj sedan **Android**.  

    ![Lägg till nya app-knapp](./media/notification-hubs-kindle-get-started/add-new-app-button.png)
1. På den **skicka in nya program** utför dessa steg för att få den **programnyckel**:
    1. Ange ett namn för den **apptiteln**.
    2. Välj valfri **kategori** (till exempel: education)
    4. Ange en e-postadress för den **Customer support e-postadress** fält. 
    5. Välj **Spara**.

        ![Den nya appen Skicka sida](./media/notification-hubs-kindle-get-started/new-app-submission-page.png) 
2.  Högst upp, växla till den **Mobile annonser** fliken och gör följande: 
    1. Ange om din app dirigeras främst i barn under 13. Den här självstudien väljer **nr**.
    2. Välj **Skicka**. 

        ![Mobile Ads-sidan](./media/notification-hubs-kindle-get-started/mobile-ads-page.png)
    3. Kopiera den **programnyckel** från den **Mobile annonser** sidan. 

        ![Programnyckel](./media/notification-hubs-kindle-get-started/application-key.png)
3.  Välj **appar och tjänster** på överst menyn och välj ditt program i listan. 

    ![Välj din app från listan](./media/notification-hubs-kindle-get-started/all-apps-select.png)
4. Växla till den **enhetsmeddelanden** fliken och följ de här stegen: 
    1. Välj **skapa en ny säkerhetsprofil**.
    2. Ange en **namn** för din säkerhetsprofil. 
    3. Ange **beskrivning** för din säkerhetsprofil. 
    4. Välj **Spara**. 
    5. Välj **visa säkerhetsprofil** på resultatsidan. 
5. Nu på den **säkerhetsprofil** gör du följande steg: 
    1. Växla till den **webbinställningar** fliken och kopiera den **klient-ID** och **Klienthemlighet** värdet för senare användning. 

        ![Hämta klient-ID och hemlighet](./media/notification-hubs-kindle-get-started/client-id-secret.png) 
    2. Växla till den **inställningar för Android/Kindle** sidan och håll sidan öppen. Du måste ange dessa värden i nästa avsnitt. 

## <a name="create-an-api-key"></a>Skapa en API-nyckel.
1. Öppna en kommandotolk med administratörsbehörighet.
2. Navigera till mappen Android SDK.
3. Ange följande kommando:

    ```shell
    keytool -list -v -alias androiddebugkey -keystore ./debug.keystore
    ```
4. Ange **android** som lösenord för **keystore**.
5. Kopiera den **MD5** och **SHA256** fingeravtryck. 
6. Tillbaka i developer-portalen på den **inställningar för Android/Kindle** gör du följande steg: 
    1. Ange en **för API-nyckeln**. 
    2. Ange den **namn på paketet** för din app (till exempel **com.fabrikam.mykindleapp**) och **MD5** värde.
        
        >[!IMPORTANT]
        > När du skapar en app i Android Studio kan du använda samma paketnamn som du angav här. 
    1. Klistra in den **MD5 signatur** du kopierade tidigare. 
    2. Klistra in den **SHA256 signatur** du kopierade tidigare.  
    3. Välj **Skapa ny nyckel**.

        ![Inställningar för Android/Kindle – Generera nyckel](./media/notification-hubs-kindle-get-started/android-kindle-settings.png)
    4. Nu väljer **visa** i listan för att visa API-nyckel. 

        ![Inställningar för Android/Kindle – visa API-nyckel](./media/notification-hubs-kindle-get-started/show-api-key-button.png) 
    5. I den **information om API-nyckel** fönstret Kopiera API-nyckeln och spara den någonstans. Välj **X** i det övre högra hörnet att Stäng fönstret. 


## <a name="create-and-configure-a-notification-hub"></a>Skapa och konfigurera en notification hub

1. Följ stegen i den [skapar en Azure notification hub i Azure-portalen](create-notification-hub-portal.md) artikeln om du vill skapa en meddelandehubb. 
2. Välj **Amazon (ADM)** under **inställningar** menyn.
3. Klistra in den **klient-ID** och **Klienthemlighet** du sparade tidigare. 
4. Välj **Spara** i verktygsfältet. 

    ![Konfigurera ADM-inställningar för en meddelandehubb](./media/notification-hubs-kindle-get-started/configure-notification-hub.png)
5. Välj **åtkomstprinciper** på den vänstra menyn och välj den **kopia** knappen för anslutningssträngen för den **DefaultListenSharedAccessSignature** princip. Spara den någonstans. Du ska använda den senare i källkoden. 

    ![Meddelandehub – lyssna anslutningssträng](./media/notification-hubs-kindle-get-started/event-hub-listen-connection-string.png)    

## <a name="set-up-your-application"></a>Konfigurera din app

1. Starta Android Studio. 
2. Välj **filen**, peka på **New**, och välj sedan **nytt projekt**. 
3. I den **väljer du projektet** fönstret på den **Telefoner och surfplattor** fliken **tom aktivitet**, och välj **nästa**. 
4. I den **konfigurera ditt projekt** och gör följande:
    1. Ange en **namn för ditt program**. Du kanske vill matcha med namnet på det program du skapade i Amazon Developer-portalen. 
    2. Ange en **namn för paketet**. 
        
        >[!IMPORTANT]
        >Paketnamnet måste matcha paketnamn som du angav i Amazon Developer-portalen.
    3. Granska och uppdatera det återstående värdena efter behov. 
    4. Välj **Slutför**. 

        ![Konfigurera Android-projekt](./media/notification-hubs-kindle-get-started/new-android-studio-project.png)
5. Ladda ned [Amazon Developer-SDK för Android](https://developer.amazon.com/sdk-download) biblioteket på din hårddisk. Packa upp zip-filen för SDK.
6. I Android Studio ändrar mappstrukturen från **Android** till **projekt** om den inte har angetts till **projekt**. 

    ![Android Studio – växla till projektstruktur](./media/notification-hubs-kindle-get-started/android-studio-project-view.png)
7. Expandera **app** att se den **libs** mapp i trädvyn.     
8. I ett fönster med Utforskaren, går du till den mapp där du hämtade Amazon Android SDK: er.
9. Tryck på **CTRL** och dra och släpp den **amazon-enhet-meddelanden-1.0.1.jar** filen till den **lib** noden i trädvyn. 

    ![Android Studio - Add Amazon Device Messaging JAR](./media/notification-hubs-kindle-get-started/drag-drop-amazon-device-messaging-jar.png)
9. I den **kopia** väljer **OK**. Om du ser den **flytta** fönster i stället för **kopia** fönstret stänger du det och försök med dra och släpp **CTRL** tryckt. 

    ![Android Studio – kopiera JAR](./media/notification-hubs-kindle-get-started/copy-jar-window.png)
10. Lägg till följande instruktion för att den **appens build.gradle** fil i den **beroenden** avsnittet: `implementation files('libs/amazon-device-messaging-1.0.1.jar')`. 

    ![Android Studio – Lägg till ADM i appens build.gradle](./media/notification-hubs-kindle-get-started/adm-build-gradle.png)
11. I den `Build.Gradle` för den **app**, Lägg till följande rader i den **beroenden** avsnittet: 

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```
12. Lägg till följande lagringsplats **när** den **beroenden** avsnittet:

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```
13. I Redigeraren för den **build.gradle** för den **app**väljer **synkronisera nu** i verktygsfältet. 

    ![Android Studio – synkronisera build.gradle-appen](./media/notification-hubs-kindle-get-started/gradle-sync-now.png)
14. Gå tillbaka till Android strukturen i trädvyn.  Lägg till namnområdet för Amazon i rotelementets manifest:

    ```xml
    xmlns:amazon="http://schemas.amazon.com/apk/res/android"
    ```
   
    ![Namnområdet för Amazon i manifestet](./media/notification-hubs-kindle-get-started/amazon-namespace-manifest.png)
2. Lägg till behörigheter som det första elementet under manifestelementet. Ersätt **[YOUR PACKAGE NAME]** med det paket som du använde för att skapa din app.

    ```xml
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
    ```
3. Infoga följande element som app-elementets första underordnade. Ersätt **[YOUR PACKAGE NAME]** med det paketnamn som du skapade din app. I nästa steg ska du skapa klassen MyADMMessageHandler. 

    ```xml
        <amazon:enable-feature
            android:name="com.amazon.device.messaging"
            android:required="true"/>
        <service
            android:name="[YOUR PACKAGE NAME].MyADMMessageHandler"
            android:exported="false" />
        <receiver
            android:name="[YOUR PACKAGE NAME].MyADMMessageHandler$Receiver"
            android:permission="com.amazon.device.messaging.permission.SEND" >

            <!-- To interact with ADM, your app must listen for the following intents. -->
            <intent-filter>
                <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
                <action android:name="com.amazon.device.messaging.intent.RECEIVE" />
                <!-- Replace the name in the category tag with your app's package name. -->
                <category android:name="[YOUR PACKAGE NAME]" />
            </intent-filter>
        </receiver>
    ```

## <a name="create-your-adm-message-handler"></a>Skapa en meddelandehanterare för ADM

1. Lägg till en ny klass till den `com.fabrikam.mykindleapp` paketet i projektet som ärver från `com.amazon.device.messaging.ADMMessageHandlerBase` och ge den namnet `MyADMMessageHandler`, enligt följande bild:

    ![Skapa MyADMMessageHandler-klass](./media/notification-hubs-kindle-get-started/create-adm-message-handler.png)

2. Lägg till följande `import` -uttryck för att den `MyADMMessageHandler` klass:

    ```java
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.support.v4.app.NotificationCompat;
    import android.util.Log;
    import com.amazon.device.messaging.ADMMessageReceiver;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    ```

3. Lägg till följande kod i den klass som du har skapat. Kom ihåg den `[HUB NAME]` och `[LISTEN CONNECTION STRING]` med namnet på din notification hub och lyssna anslutningssträng: 

    ```java
    public static final int NOTIFICATION_ID = 1;
    private NotificationManager mNotificationManager;
    NotificationCompat.Builder builder;
    private static NotificationHub hub;

    public static NotificationHub getNotificationHub(Context context) {
        Log.v("com.wa.hellokindlefire", "getNotificationHub");
        if (hub == null) {
            hub = new NotificationHub("[HUB NAME]", "[HUB NAMESPACE CONNECTION STRING]", context);
        }
        return hub;
    }

    public MyADMMessageHandler() {
        super("MyADMMessageHandler");
    }

    @Override
    protected void onMessage(Intent intent) {
        String nhMessage = intent.getExtras().getString("msg");
        sendNotification(nhMessage);
    }

    @Override
    protected void onRegistrationError(String s) {

    }

    @Override
    protected void onRegistered(String s) {
        try {
            getNotificationHub(getApplicationContext()).register(s);
        } catch (Exception e) {
            Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
        }
    }

    @Override
    protected void onUnregistered(String s) {
        try {
            getNotificationHub(getApplicationContext()).unregister();
        } catch (Exception e) {
            Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
        }
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
    ```

## <a name="add-your-api-key-to-your-app"></a>Lägg till API-nyckeln i din app
1. Följ dessa steg för att lägga till en mapp med tillgångar i projektet. 
    1. Växla till den **projekt** vy. 
    2. Högerklicka på **app**.
    3. Välj **Ny**.
    4. Välj **mappen**. 
    5. Välj **resursmapp**. 

        ![Tillgångar mappen menyn Lägg till](./media/notification-hubs-kindle-get-started/add-assets-folder-menu.png)    
    6. På den **konfigurera komponenten** gör du följande steg:
        1. Välj **ändra plats för mappen**
        2. Bekräfta att mappen är inställd på: `src/main/assets`.
        3. Välj **Slutför**. 
        
            ![Konfigurera resursmapp](./media/notification-hubs-kindle-get-started/configure-asset-folder.png)
2. Lägg till en fil med namnet **api_key.txt** till den **tillgångar** mapp. I trädvyn expanderar **app**, expandera **src**, expandera **huvudsakliga**, och högerklicka på **tillgångar**, peka på **New**, och välj sedan **filen**. Ange **api_key.txt** för filnamnet. 3. 
5. Kopiera API-nyckeln som du genererade på Amazon developer-portalen till filen api_key.txt. 
6. Bygga projektet. 

## <a name="run-the-app"></a>Kör appen
1. På Kindle-enhet, sveper du uppifrån och klickar på **inställningar**, och klicka sedan på **mitt konto** och registrera med ett giltigt Amazon-konto.
2. Kör appen på en Kindle-enhet från Android Studio. 

> [!NOTE]
> Om problem uppstår, kontrollerar du tiden för emulatorn (eller enheten). Tidsvärdet måste vara korrekt. Om du vill ändra Kindle-emulatorns tid kan du köra följande kommando från katalogen för plattformsverktygen för Android SDK:

```shell
adb shell  date -s "yyyymmdd.hhmmss"
```

## <a name="send-a-notification-message"></a>Skicka ett aviseringsmeddelande

Att skicka ett meddelande med hjälp av .NET:

```csharp
static void Main(string[] args)
{
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

    hub.SendAdmNativeNotificationAsync("{\"data\":{\"msg\" : \"Hello from .NET!\"}}").Wait();
}
```

Exempelkoden finns [det här exemplet på GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/blob/master/Samples/SendPushSample/SendPushSample/Program.cs).

![][7]

## <a name="next-steps"></a>Nästa steg

I de här självstudierna har du skickat meddelanden till alla Kindle-enheter som är registrerade hos serverdelen. Information om hur du skickar meddelanden till specifika Kindle-enheter finns i följande självstudie:  Följande självstudie visar hur du skickar push-meddelanden till specifika Android-enheter. Men du kan använda samma logik för att skicka push-meddelanden till specifika Kindle-enheter.

> [!div class="nextstepaction"]
>[Skicka meddelanden till specifika enheter](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- URLs. -->
[Amazon developer portal]: https://developer.amazon.com/home.html
[download the SDK]: https://developer.amazon.com/public/resources/development-tools/sdk
[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png
