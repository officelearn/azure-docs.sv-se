---
title: Kom igång med Azure Notification Hubs genom att använda Baidu | Microsoft Docs
description: I den här självstudiekursen får du lära dig hur du använder Azure Notification Hubs för att skicka push-meddelanden till Android-enheter med hjälp av Baidu.
services: notification-hubs
documentationcenter: android
author: wesmc7777
manager: dwrede
editor: ''

ms.service: notification-hubs
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: mobile-baidu
ms.workload: mobile
ms.date: 08/19/2016
ms.author: wesmc

---
# Kom igång med Notification Hub genom att använda Baidu
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## Översikt
Baidu Cloud Push är en kinesisk molntjänst som du kan använda för att skicka push-meddelanden till mobila enheter. Tjänsten är särskilt användbar i Kina, där det är ganska komplicerat att leverera push-meddelanden till Android på grund av att det finns många olika appbutiker och push-tjänster. Dessutom finns det många Android-enheter som normalt inte är anslutna till GCM (Google Cloud Messaging).

## Krav
Följande krävs för den här självstudiekursen:

* Android SDK (vi antar att du kommer att använda Eclipse) som du kan hämta från <a href="http://go.microsoft.com/fwlink/?LinkId=389797">webbplatsen för Android</a>
* [Mobile Services Android SDK]
* [Baidu Push Android SDK]

> [!NOTE]
> Du måste ha ett aktivt Azure-konto för att slutföra den här självstudiekursen. Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [här](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-baidu-get-started%2F).
> 
> 

## Skapa ett Baidu-konto
Om du vill använda Baidu måste du ha ett Baidu-konto. Om du redan har ett sådant, loggar du in på [Baidu-portalen] och sedan hoppar du över nästa steg. Annars läser du anvisningarna nedan om hur du skapar ett Baidu-konto.  

1. Gå till [Baidu-portalen] och klicka på länken **登录** (**Logga in**). Klicka på **立即注册** för att starta kontoregistreringsprocessen.
   
    ![][1]
2. Ange den information som krävs: telefon/e-postadress, lösenord och verifieringskod. Sedan klickar du på **Signup** (Registrera).
   
    ![][2]
3. Du kommer att få ett mejl med en länk för att aktivera Baidu-kontot. Det skickas till den e-postadress som du angav vid registreringen.
   
    ![][3]
4. Logga in på ditt e-postkonto, öppna aktiveringsmejlet från Baidu och klicka på aktiveringslänken för att aktivera ditt Baidu-konto.
   
    ![][4]

När du har ett aktiverat Baidu-konto kan du logga in på [Baidu-portalen].

## Registrera dig som Baidu-utvecklare.
1. När du har loggat in på [Baidu-portalen], klickar du på **更多 >>** (**mer**).
   
    ![][5]
2. Rulla nedåt i avsnittet **站长与开发者服务 (tjänster för webbadministratörer och utvecklare)** och klicka på **百度开放云平台** (**Öppen molnplattform för Baidu**).
   
    ![][6]
3. På nästa sida klickar du på **开发者服务** (**Utvecklartjänster**) i det övre högra hörnet.
   
    ![][7]
4. På nästa sida klickar du på **注册开发者** (**Registrerade utvecklare**) på menyn i det övre högra hörnet.
   
    ![][8]
5. Ange ditt namn, en beskrivning och ett mobiltelefonnummer för att få ett textmeddelande för verifiering. Klicka sedan på **送验证码** (**Skicka verifieringskod**). Observera att du måste ange landskoden inom parentes för internationella telefonnummer. För ett mobiltelefonnummer från USA ska du till exempel ange **(1) 1234567890**.
   
    ![][9]
6. Du bör sedan få ett textmeddelande med ett verifieringsnummer, som i följande exempel:
   
    ![][10]
7. Ange verifieringsnumret från meddelandet i **验证码** (**Bekräftelsekod**).
8. Slutför registreringen som utvecklare genom att godkänna avtalet från Baidu och klicka på **提交** (**Skicka**). Följande sida kommer att visas om registreringen har genomförts på rätt sätt:
   
    ![][11]

## Skapa ett Baidu Cloud Push-projekt
När du skapar ett Baidu Cloud Push-projekt, kommer du att få ett app-ID, en API-nyckel och en hemlig nyckel.

1. När du har loggat in på [Baidu-portalen], klickar du på **更多 >>** (**mer**).
   
    ![][5]
2. Rulla nedåt i avsnittet **站长与开发者服务** (**Tjänster för webbadministratörer och utvecklare**) och klicka på **百度开放云平台** (**Öppen molnplattform för Baidu**).
   
    ![][6]
3. På nästa sida klickar du på **开发者服务** (**Utvecklartjänster**) i det övre högra hörnet.
   
    ![][7]
4. På nästa sida klickar du på **云推送** (**Cloud Push**) från avsnittet **云服务** (**Molntjänster**).
   
    ![][12]
5. När du har registrerat dig som utvecklare visas **管理控制台** (**Hanteringskonsolen**) i övermenyn. Klicka på **开发者服务管理** (**Tjänstehantering för utvecklare**).
   
    ![][13]
6. På nästa sida klickar du på **创建工程** (**Skapa projekt**).
   
    ![][14]
7. Ange ett programnamn och klicka på **创建** (**Skapa**).
   
    ![][15]
8. När ett Baidu Cloud Push-projekt har skapats på rätt sätt, kommer du att se en sida med **AppID**, **API-nyckeln** och en **hemlig nyckel**. Skriv ner API-nyckeln och den hemliga nyckeln, dessa ska användas senare.
   
    ![][16]
9. Konfigurera projektet för push-meddelanden genom att klicka på **云推送** (**Cloud Push**) i den vänstra rutan.
   
    ![][31]
10. På nästa sida klickar du på knappen **推送设置** (**Push-inställningar**).
    
    ![][32]  
11. På konfigurationssidan lägger du till det paketnamn som du ska använda i Android-projektet i fältet **应用包名** (**Programpaket**). Sedan klickar du på **保存设置** (**Spara**).  
    
    ![][33]

Meddelandet **保存成功!** (**Ändringarna har sparats!**) visas.

## Konfigurera meddelandehubben
1. Logga in på den [Klassisk Azure-portal] och klicka sedan på **+NY** längst ner på skärmen.
2. Klicka på **Apptjänster**, sedan på**Service Bus** och därefter på **Meddelandehubb**. Slutligen klickar du på **Snabbregistrering**.
3. Ange ett namn för din **Meddelandehubb**, välj en **Region** och ett **Namnområde** där den här meddelandehubben ska skapas och klicka sedan på **Skapa en ny meddelandehubb**.  
   
    ![][17]
4. Klicka på det namnområde där du skapade din meddelandehubb och klicka sedan på **Meddelandehubb** högst upp.
   
    ![][18]
5. Markera den meddelandehubb som du precis skapat och klicka sedan på **Konfigurera** i övermenyn.
   
    ![][19]
6. Rulla ned till avsnittet **Meddelandeinställningar för Baidu** och ange den API-nyckel och hemliga nyckel som du fick från konsolen Baidu tidigare för ditt Baidu Cloud Push-projekt. Klicka på **Spara**.
   
    ![][20]
7. Klicka på fliken **Instrumentpanel** överst för meddelandehubben och klicka sedan på **Visa anslutningssträng**.
   
    ![][21]
8. Skriv ned **DefaultListenSharedAccessSignature** och **DefaultFullSharedAccessSignature** från fönstret **Anslutningsinformation för åtkomst**.
   
    ![][22]

## Anslut appen till meddelandehubben
1. Skapa ett nytt Android-projekt i Eclipse ADT (**Arkiv** > **Ny** > **Android-approjekt**).
   
    ![][23]
2. Ange ett **programnamn** och kontrollera att versionen för **Minsta nödvändiga SDK** är inställd på **API 16: Android 4.1**.
   
    ![][24]
3. Klicka på **Nästa** och fortsätt följa anvisningarna i guiden tills fönstret **Skapa aktivitet** visas. Se till att **Tom aktivitet** är markerad och välj slutligen **Slutför** för att skapa en ny Android-app.
   
    ![][25]
4. Kontrollera att **Mål för projektgenerering** är korrekt inställt.
   
    ![][26]
5. Hämta filen notification-hubs-0.4.jar från fliken **Filer** under [Notification-Hubs-Android-SDK på Bintray](https://bintray.com/microsoftazuremobile/SDK/Notification-Hubs-Android-SDK/0.4). Lägg till filen i mappen **libs** i ditt Eclipse-projekt och uppdatera denna *mapp*.
6. Hämta och packa upp [Baidu Push Android SDK], öppna mappen **libs** och kopiera sedan jar-filen **pushservice x.y.z** och mapparna **armeabi** & **mips** i mappen **libs** för din Android-app.
7. Öppna filen **AndroidManifest.xml** för ditt Android-projekt och lägg till de behörigheter som krävs för Baidu-SDK:et.
   
        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.READ_PHONE_STATE" />
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.WRITE_SETTINGS" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
        <uses-permission android:name="android.permission.DISABLE_KEYGUARD" />
        <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
        <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
        <uses-permission android:name="android.permission.ACCESS_DOWNLOAD_MANAGER" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION" />
8. Lägg till egenskapen**android:name** i **app**-elementet i **AndroidManifest.xml** genom att byta ut *yourprojectname* (t.ex. **com.example.BaiduTest**). Kontrollera att projektnamnet motsvarar det som du konfigurerade i Baidu-konsolen.
   
        <application android:name="yourprojectname.DemoApplication"
9. Lägg till följande konfiguration i app-elementet efter aktivitetselementet **.MainActivity** genom att byta ut *yourprojectname* (t.ex. **com.example.BaiduTest**):
   
        <receiver android:name="yourprojectname.MyPushMessageReceiver">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.MESSAGE" />
                <action android:name="com.baidu.android.pushservice.action.RECEIVE" />
                <action android:name="com.baidu.android.pushservice.action.notification.CLICK" />
            </intent-filter>
        </receiver>
   
        <receiver android:name="com.baidu.android.pushservice.PushServiceReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED" />
                <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
                <action android:name="com.baidu.android.pushservice.action.notification.SHOW" />
            </intent-filter>
        </receiver>
   
        <receiver android:name="com.baidu.android.pushservice.RegistrationReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.METHOD" />
                <action android:name="com.baidu.android.pushservice.action.BIND_SYNC" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.PACKAGE_REMOVED"/>
                <data android:scheme="package" />
            </intent-filter>
        </receiver>
   
        <service
            android:name="com.baidu.android.pushservice.PushService"
            android:exported="true"
            android:process=":bdservice_v1"  >
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.PUSH_SERVICE" />
            </intent-filter>
        </service>
10. Lägg till en ny klass med namnet **ConfigurationSettings.java** i projektet.
    
     ![][28]
    
     ![][29]
11. Lägg till följande kod i den:
    
        public class ConfigurationSettings {
                public static String API_KEY = "...";
                public static String NotificationHubName = "...";
                public static String NotificationHubConnectionString = "...";
            }
    
    Justerar värdet för **API_KEY** med det du hämtade från Baidu Cloud-projektet tidigare. Ersätt **NotificationHubName** med ditt meddelandehubbsnamn och **NotificationHubConnectionString** med DefaultListenSharedAccessSignature, båda från den klassiska Azure-portalen.
12. Lägg till en ny klass med namnet **DemoApplication.java** och lägg till följande kod i den:
    
        import com.baidu.frontia.FrontiaApplication;
    
        public class DemoApplication extends FrontiaApplication {
            @Override
            public void onCreate() {
                super.onCreate();
            }
        }
13. Lägg till ytterligare en ny klass med namnet **MyPushMessageReceiver.java** och lägg till den kod som visas nedan i den. Det här är klassen som hanterar de push-meddelanden som tas emot från push-servern i Baidu.
    
        import java.util.List;
        import android.content.Context;
        import android.os.AsyncTask;
        import android.util.Log;
        import com.baidu.frontia.api.FrontiaPushMessageReceiver;
        import com.microsoft.windowsazure.messaging.NotificationHub;
    
        public class MyPushMessageReceiver extends FrontiaPushMessageReceiver {
            /** TAG to Log */
            public static NotificationHub hub = null;
            public static String mChannelId, mUserId;
            public static final String TAG = MyPushMessageReceiver.class
                    .getSimpleName();
    
            @Override
            public void onBind(Context context, int errorCode, String appid,
                    String userId, String channelId, String requestId) {
                String responseString = "onBind errorCode=" + errorCode + " appid="
                        + appid + " userId=" + userId + " channelId=" + channelId
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
                mChannelId = channelId;
                mUserId = userId;
    
                try {
                 if (hub == null) {
                        hub = new NotificationHub(
                                ConfigurationSettings.NotificationHubName,
                                ConfigurationSettings.NotificationHubConnectionString,
                                context);
                        Log.i(TAG, "Notification hub initialized");
                    }
                } catch (Exception e) {
                   Log.e(TAG, e.getMessage());
                }
    
                registerWithNotificationHubs();
            }
    
            private void registerWithNotificationHubs() {
               new AsyncTask<Void, Void, Void>() {
                  @Override
                  protected Void doInBackground(Void... params) {
                     try {
                         hub.registerBaidu(mUserId, mChannelId);
                         Log.i(TAG, "Registered with Notification Hub - '"
                                + ConfigurationSettings.NotificationHubName + "'"
                                + " with UserId - '"
                                + mUserId + "' and Channel Id - '"
                                + mChannelId + "'");
                     } catch (Exception e) {
                         Log.e(TAG, e.getMessage());
                     }
                     return null;
                 }
               }.execute(null, null, null);
            }
    
            @Override
            public void onSetTags(Context context, int errorCode,
                    List<String> sucessTags, List<String> failTags, String requestId) {
                String responseString = "onSetTags errorCode=" + errorCode
                        + " sucessTags=" + sucessTags + " failTags=" + failTags
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
            }
    
            @Override
            public void onDelTags(Context context, int errorCode,
                    List<String> sucessTags, List<String> failTags, String requestId) {
                String responseString = "onDelTags errorCode=" + errorCode
                        + " sucessTags=" + sucessTags + " failTags=" + failTags
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
            }
    
            @Override
            public void onListTags(Context context, int errorCode, List<String> tags,
                    String requestId) {
                String responseString = "onListTags errorCode=" + errorCode + " tags="
                        + tags;
                Log.d(TAG, responseString);
            }
    
            @Override
            public void onUnbind(Context context, int errorCode, String requestId) {
                String responseString = "onUnbind errorCode=" + errorCode
                        + " requestId = " + requestId;
                Log.d(TAG, responseString);
            }
    
            @Override
            public void onNotificationClicked(Context context, String title,
                    String description, String customContentString) {
                String notifyString = "title=\"" + title + "\" description=\""
                        + description + "\" customContent=" + customContentString;
                Log.d(TAG, notifyString);
            }
    
            @Override
            public void onMessage(Context context, String message,
                    String customContentString) {
                String messageString = "message=\"" + message + "\" customContentString=" + customContentString;
                Log.d(TAG, messageString);
            }
        }
14. Öppna **MainActivity.java** och lägg till följande i metoden **onCreate**:
    
            PushManager.startWork(getApplicationContext(),
                    PushConstants.LOGIN_TYPE_API_KEY, ConfigurationSettings.API_KEY);
15. Öppna följande importuttryck längst upp:
    
            import com.baidu.android.pushservice.PushConstants;
            import com.baidu.android.pushservice.PushManager;

## Skicka meddelanden till din app
Du kan snabbt testa att ta emot meddelanden i appen genom att skicka meddelanden [Azure Portal](https://portal.azure.com/) med knappen **Testsänd** i meddelandehubben, så som visas på skärmen nedan.

![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)

Push-meddelanden skickas vanligtvis i en serverdelstjänst som Mobile Services eller ASP.NET med hjälp av ett kompatibelt bibliotek. Du kan också använda REST-API:et direkt för att skicka meddelanden om ett bibliotek inte är tillgängligt för din serverdel.

I den här enkla självstudiekursen visar vi hur du testar klientappen genom att skicka meddelanden med .NET SDK för meddelandehubbar i ett konsolprogram i stället för med en serverdelstjänst. Vi rekommenderar att du går vidare med självstudiekursen [Använda Notification Hubs för att skicka push-meddelanden till användare](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) som nästa steg i att skicka meddelanden från en ASP.NET-serverdel. Följande åtgärder kan dock användas för att skicka meddelanden:

* **REST-gränssnitt**: Du kan använda meddelanden på alla serverdelsplattformar med [REST-gränssnittet](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).
* **Microsoft Azure Notification Hubs .NET SDK**: I Nuget Package Manager för Visual Studio kör du [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).
* **Node.js**: [Använda Notification Hubs från Node.js](notification-hubs-nodejs-push-notification-tutorial.md).
* **Azure Mobile Services**: Ett exempel på hur du skickar meddelanden från en serverdel för Azure Mobile Services som är integrerad med Notification Hubs finns i [Lägga till push-meddelanden i din Mobile Services-app](../mobile-services/mobile-services-javascript-backend-windows-universal-dotnet-get-started-push.md).
* **Java/PHP**: Ett exempel på hur du skickar meddelanden med hjälp av REST-API finns i avsnittet Använda Notification Hubs från Java/PHP ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)).

## (Valfritt) Skicka meddelanden från en .NET-konsolapp
I det här avsnittet kommer du att lära dig hur du skickar meddelanden med hjälp av en .NET-konsolapp.

1. Skapa en ny Visual C#-konsolapp:
   
    ![][30]
2. I fönstret för Package Manager-konsolen ställer du in **standardprojektet** till det nya projektet för konsolappen. Sedan kör du följande kommando i konsolfönstret:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
    Då läggs en referens till i Azure Notification Hubs SDK med hjälp av <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification Hubs NuGet-paketet</a>.
   
    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)
3. Öppna filen **Program.cs** och lägg till följande using-programsats:
   
        using Microsoft.Azure.NotificationHubs;
4. I klassen `Program` lägger du till följande metod och ersätter *DefaultFullSharedAccessSignatureSASConnectionString* och *NotificationHubName* med de värden du har.
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
            string message = "{\"title\":\"((Notification title))\",\"description\":\"Hello from Azure\"}";
            var result = await hub.SendBaiduNativeNotificationAsync(message);
        }
5. Lägg till följande rader i metoden **Main**:
   
         SendNotificationAsync();
         Console.ReadLine();

## Testa din app
Om du vill testa den här appen med en riktig mobil behöver du bara ansluta den till datorn med en USB-kabel. Då läses din app in på den anslutna mobilen.

Klicka på **Kör** i verktygsfältet högst upp i Eclipse och välj sedan din app om du vill testa den med emulatorn. Detta startar emulatorn som sedan läser in och kör appen.

Appen hämtar “userId” och “channelId” från tjänsten Baidu Push Notification och registreras i meddelandehubben.

Du kan använda fliken för felsökning i den klassiska Azure-portalen för att skicka ett testmeddelande. Om du har byggt .NET-konsolappen för Visual Studio, behöver du bara trycka på tangenten F5 i Visual Studio för att köra appen. Appen skickar ett meddelande som kommer att visas i det övre meddelandefältet i enheten eller emulatorn.

<!-- Images. -->
[1]: ./media/notification-hubs-baidu-get-started/BaiduRegistration.png
[2]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationInput.png
[3]: ./media/notification-hubs-baidu-get-started/BaiduConfirmation.png
[4]: ./media/notification-hubs-baidu-get-started/BaiduActivationEmail.png
[5]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationMore.png
[6]: ./media/notification-hubs-baidu-get-started/BaiduOpenCloudPlatform.png
[7]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperServices.png
[8]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperRegistration.png
[9]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationInput.png
[10]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationConfirmation.png
[11]: ./media/notification-hubs-baidu-get-started/BaiduDevConfirmationFinal.png
[12]: ./media/notification-hubs-baidu-get-started/BaiduCloudPush.png
[13]: ./media/notification-hubs-baidu-get-started/BaiduDevSvcMgmt.png
[14]: ./media/notification-hubs-baidu-get-started/BaiduCreateProject.png
[15]: ./media/notification-hubs-baidu-get-started/BaiduCreateProjectInput.png
[16]: ./media/notification-hubs-baidu-get-started/BaiduProjectKeys.png
[17]: ./media/notification-hubs-baidu-get-started/AzureNHCreation.png
[18]: ./media/notification-hubs-baidu-get-started/NotificationHubs.png
[19]: ./media/notification-hubs-baidu-get-started/NotificationHubsConfigure.png
[20]: ./media/notification-hubs-baidu-get-started/NotificationHubBaiduConfigure.png
[21]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionStringView.png
[22]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionString.png
[23]: ./media/notification-hubs-baidu-get-started/EclipseNewProject.png
[24]: ./media/notification-hubs-baidu-get-started/EclipseProjectCreation.png
[25]: ./media/notification-hubs-baidu-get-started/EclipseBlankActivity.png
[26]: ./media/notification-hubs-baidu-get-started/EclipseProjectBuildProperty.png
[27]: ./media/notification-hubs-baidu-get-started/EclipseBaiduReferences.png
[28]: ./media/notification-hubs-baidu-get-started/EclipseNewClass.png
[29]: ./media/notification-hubs-baidu-get-started/EclipseConfigSettingsClass.png
[30]: ./media/notification-hubs-baidu-get-started/ConsoleProject.png
[31]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig1.png
[32]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig2.png
[33]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig3.png

<!-- URLs. -->
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Baidu Push Android SDK]: http://developer.baidu.com/wiki/index.php?title=docs/cplat/push/sdk/clientsdk
[Klassisk Azure-portal]: https://manage.windowsazure.com/
[Baidu-portalen]: http://www.baidu.com/



<!--HONumber=Sep16_HO3-->


