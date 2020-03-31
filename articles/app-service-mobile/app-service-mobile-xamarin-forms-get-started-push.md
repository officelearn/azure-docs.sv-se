---
title: Lägga till push-meddelanden i din Xamarin.Forms-app
description: Lär dig hur du använder Azure-tjänster för att skicka push-meddelanden med flera plattformar till dina Xamarin.Forms-appar.
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: f23ac2d693492695c398893c103d5a77a0e93129
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461478"
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Lägga till push-meddelanden i din Xamarin.Forms-app

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Översikt

I den här självstudien lägger du till push-meddelanden till alla projekt som blev resultatet av [snabbstarten Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md). Det innebär att ett push-meddelande skickas till alla plattformsoberoende klienter varje gång en post infogas.

Om du inte använder det hämtade snabbstartsserverprojektet behöver du tilläggspaketet för push-meddelande. Mer information finns i [Arbeta med .NET-server för server för server för server för server för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Krav

För iOS behöver du ett medlemskap i [Apple Developer Program](https://developer.apple.com/programs/ios/) och en fysisk iOS-enhet. [IOS-simulatorn stöder inte push-meddelanden](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

## <a name="configure-a-notification-hub"></a><a name="configure-hub"></a>Konfigurera en meddelandehubb

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Uppdatera serverprojektet för att skicka push-meddelanden

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Konfigurera och köra Android-projektet (valfritt)

Fyll i det här avsnittet för att aktivera push-meddelanden för Xamarin.Forms Droid-projektet för Android.

### <a name="enable-firebase-cloud-messaging-fcm"></a>Aktivera Firebase Cloud Messaging (FCM)

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>Konfigurera backenden för mobilappar för att skicka push-begäranden med fcm

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Lägga till push-meddelanden i Android-projektet

Med serverdelen konfigurerad med FCM kan du lägga till komponenter och koder till klienten för att registrera dig hos FCM. Du kan också registrera dig för push-meddelanden med Azure Notification Hubs via den bakre delen av mobilapparna och ta emot meddelanden.

1. Högerklicka **på** **Referenser > Hantera NuGet-paket ...**.
1. I fönstret NuGet Package Manager söker du efter **Xamarin.Firebase.Messaging-paketet** och lägger till det i projektet.
1. I projektegenskaperna för **Droid-projektet** ställer du in appen så att den kompileras med Android version 7.0 eller senare.
1. Lägg till **google-services.json** fil, laddas ner från Firebase konsolen, till roten av **Droid** projektet och ställa in sin bygga åtgärder till **GoogleServicesJson**. Mer information finns i [Lägga till Google Services JSON-fil](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/#Add_the_Google_Services_JSON_File).

#### <a name="registering-with-firebase-cloud-messaging"></a>Registering med Firebase Cloud Messaging

1. Öppna filen **AndroidManifest.xml** och infoga följande `<receiver>`-element i elementet `<application>`:

    ```xml
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
        </intent-filter>
    </receiver>
    ```

#### <a name="implementing-the-firebase-instance-id-service"></a>Implementera firebase-instans-ID-tjänsten

1. Lägg till en ny klass i **Droid-projektet** med namnet `FirebaseRegistrationService`och se till att följande `using` satser finns högst upp i filen:

    ```csharp
    using System.Threading.Tasks;
    using Android.App;
    using Android.Util;
    using Firebase.Iid;
    using Microsoft.WindowsAzure.MobileServices;
    ```

1. Ersätt den `FirebaseRegistrationService` tomma klassen med följande kod:

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
    public class FirebaseRegistrationService : FirebaseInstanceIdService
    {
        const string TAG = "FirebaseRegistrationService";

        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "Refreshed token: " + refreshedToken);
            SendRegistrationTokenToAzureNotificationHub(refreshedToken);
        }

        void SendRegistrationTokenToAzureNotificationHub(string token)
        {
            // Update notification hub registration
            Task.Run(async () =>
            {
                await AzureNotificationHubService.RegisterAsync(TodoItemManager.DefaultManager.CurrentClient.GetPush(), token);
            });
        }
    }
    ```

    Klassen `FirebaseRegistrationService` ansvarar för att generera säkerhetstoken som tillåter programmet att komma åt FCM. Metoden `OnTokenRefresh` anropas när programmet tar emot en registreringstoken från FCM. Metoden hämtar token från `FirebaseInstanceId.Instance.Token` egenskapen, som är asynkront uppdaterad av FCM. Metoden `OnTokenRefresh` anropas sällan, eftersom token bara uppdateras när programmet installeras eller avinstalleras, när användaren tar bort programdata, när programmet raderar instans-ID:t eller när tokens säkerhet har komprometterats. Dessutom begär FCM-instans-ID-tjänsten att programmet uppdaterar sin token med jämna mellanrum, vanligtvis var sjätte månad.

    Metoden `OnTokenRefresh` anropar också `SendRegistrationTokenToAzureNotificationHub` metoden, som används för att associera användarens registreringstoken med Azure Notification Hub.

#### <a name="registering-with-the-azure-notification-hub"></a>Registrera dig hos Azure Notification Hub

1. Lägg till en ny klass i **Droid-projektet** med namnet `AzureNotificationHubService`och se till att följande `using` satser finns högst upp i filen:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Android.Util;
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

1. Ersätt den `AzureNotificationHubService` tomma klassen med följande kod:

    ```csharp
    public class AzureNotificationHubService
    {
        const string TAG = "AzureNotificationHubService";

        public static async Task RegisterAsync(Push push, string token)
        {
            try
            {
                const string templateBody = "{\"data\":{\"message\":\"$(messageParam)\"}}";
                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                    {"body", templateBody}
                };

                await push.RegisterAsync(token, templates);
                Log.Info("Push Installation Id: ", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                Log.Error(TAG, "Could not register with Notification Hub: " + ex.Message);
            }
        }
    }
    ```

    Metoden `RegisterAsync` skapar en enkel meddelandemeddelandemall som JSON och registrerar mallmeddelanden från meddelandehubben med hjälp av Firebase-registreringstoken. Detta säkerställer att alla meddelanden som skickas från Azure Notification Hub kommer att rikta enheten som representeras av registreringstoken.

#### <a name="displaying-the-contents-of-a-push-notification"></a>Visa innehållet i ett push-meddelande

1. Lägg till en ny klass i **Droid-projektet** med namnet `FirebaseNotificationService`och se till att följande `using` satser finns högst upp i filen:

    ```csharp
    using Android.App;
    using Android.Content;
    using Android.Media;
    using Android.Support.V7.App;
    using Android.Util;
    using Firebase.Messaging;
    ```

1. Ersätt den `FirebaseNotificationService` tomma klassen med följande kod:

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
    public class FirebaseNotificationService : FirebaseMessagingService
    {
        const string TAG = "FirebaseNotificationService";

        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);

            // Pull message body out of the template
            var messageBody = message.Data["message"];
            if (string.IsNullOrWhiteSpace(messageBody))
                return;

            Log.Debug(TAG, "Notification message body: " + messageBody);
            SendNotification(messageBody);
        }

        void SendNotification(string messageBody)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            //Unique request code to avoid PendingIntent collision.
            var requestCode = new Random().Next();
            var pendingIntent = PendingIntent.GetActivity(this, requestCode, intent, PendingIntentFlags.OneShot);
            var notificationBuilder = new NotificationCompat.Builder(this)
                .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
                .SetContentTitle("New Todo Item")
                .SetContentText(messageBody)
                .SetContentIntent(pendingIntent)
                .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))
                .SetAutoCancel(true);

            var notificationManager = NotificationManager.FromContext(this);
            notificationManager.Notify(0, notificationBuilder.Build());
        }
    }
    ```

    Metoden, `OnMessageReceived` som anropas när ett program tar emot ett meddelande från FCM, extraherar meddelandeinnehållet `SendNotification` och anropar metoden. Den här metoden konverterar meddelandeinnehållet till ett lokalt meddelande som startas medan programmet körs, med meddelandet som visas i meddelandefältet.

Nu är du redo att testa push-meddelanden i appen som körs på en Android-enhet eller emulatorn.

### <a name="test-push-notifications-in-your-android-app"></a>Testa push-meddelanden i din Android-app

De två första stegen krävs bara när du testar på en emulator.

1. Kontrollera att du distribuerar till eller felsöker på en enhet eller emulator som är konfigurerad med Google Play-tjänster. Detta kan verifieras genom att kontrollera att **Play-apparna** är installerade på enheten eller emulatorn.
2. Lägg till ett Google-konto på Android-enheten genom att klicka på**Lägg till konto****för** >  **inställningar för appar** > . Följ sedan anvisningarna om att lägga till ett befintligt Google-konto på enheten eller skapa ett nytt.
3. Högerklicka på **Droid-projektet** i Visual Studio eller Xamarin Studio och klicka på **Ange som startprojekt**.
4. Klicka på **Kör** för att skapa projektet och starta appen på din Android-enhet eller emulator.
5. Skriv en uppgift i appen och klicka**+** sedan på plusikonen ( ).
6. Kontrollera att ett meddelande tas emot när ett objekt läggs till.

## <a name="configure-and-run-the-ios-project-optional"></a>Konfigurera och köra iOS-projektet (valfritt)

Det här avsnittet handlar om att köra Xamarin iOS-projektet för iOS-enheter. Du kan hoppa över det här avsnittet om du inte arbetar med iOS-enheter.

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>Konfigurera meddelandehubben för APNS

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Därefter konfigurerar du iOS-projektinställningen i Xamarin Studio eller Visual Studio.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>Lägga till push-meddelanden i din iOS-app

1. Öppna AppDelegate.cs **i iOS-projektet** och lägg till följande satsen överst i kodfilen.

    ```csharp
    using Newtonsoft.Json.Linq;
    ```

2. I klassen **AppDelegate** lägger du till en åsidosättning för händelsen **RegisteredForRemoteNotifications** för att registrera meddelanden:

    ```csharp
    public override void RegisteredForRemoteNotifications(UIApplication application,
        NSData deviceToken)
    {
        const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
            {
                {"body", templateBodyAPNS}
            };

        // Register for push with your mobile app
        Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
        push.RegisterAsync(deviceToken, templates);
    }
    ```

3. I **AppDelegate**lägger du även till följande åsidosättning för händelsehanteraren **DidReceiveRemoteNotification:**

    ```csharp
    public override void DidReceiveRemoteNotification(UIApplication application,
        NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
    {
        NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

        string alert = string.Empty;
        if (aps.ContainsKey(new NSString("alert")))
            alert = (aps[new NSString("alert")] as NSString).ToString();

        //show alert
        if (!string.IsNullOrEmpty(alert))
        {
            UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
            avAlert.Show();
        }
    }
    ```

    Den här metoden hanterar inkommande meddelanden medan appen körs.

4. Lägg till följande kod i metoden **Färdigstart i** klassen **AppDelegate:**

    ```csharp
    // Register for push notifications.
    var settings = UIUserNotificationSettings.GetSettingsForTypes(
        UIUserNotificationType.Alert
        | UIUserNotificationType.Badge
        | UIUserNotificationType.Sound,
        new NSSet());

    UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
    UIApplication.SharedApplication.RegisterForRemoteNotifications();
    ```

    Detta möjliggör stöd för fjärrmeddelanden och begäranden push registrering.

Din app uppdateras nu för att stödja push-meddelanden.

#### <a name="test-push-notifications-in-your-ios-app"></a>Testa push-meddelanden i din iOS-app

1. Högerklicka på iOS-projektet och klicka på **Ange som StartUp Project**.
2. Tryck på **körknappen** eller **F5** i Visual Studio för att skapa projektet och starta appen på en iOS-enhet. Klicka sedan på **OK** för att acceptera push-meddelanden.

   > [!NOTE]
   > Du måste uttryckligen acceptera push-meddelanden från din app. Den här begäran inträffar bara första gången appen körs.

3. Skriv en uppgift i appen och klicka**+** sedan på plusikonen ( ).
4. Kontrollera att ett meddelande tas emot och klicka sedan på **OK** för att avvisa meddelandet.

## <a name="configure-and-run-windows-projects-optional"></a>Konfigurera och köra Windows-projekt (valfritt)

Det här avsnittet är för att köra Xamarin.Forms WinApp och WinPhone81 projekt för Windows-enheter. De här stegen stöder också UWP-projekt (Universal Windows Platform). Du kan hoppa över det här avsnittet om du inte arbetar med Windowsenheter.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>Registrera din Windows-app för push-meddelanden med Windows Notification Service (WNS)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>Konfigurera meddelandehubben för WNS

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Lägga till push-meddelanden i Din Windows-app

1. Öppna **App.xaml.cs** i ett Windows-projekt i Visual Studio och lägg till följande satser.

    ```csharp
    using Newtonsoft.Json.Linq;
    using Microsoft.WindowsAzure.MobileServices;
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    using <your_TodoItemManager_portable_class_namespace>;
    ```

    Ersätt `<your_TodoItemManager_portable_class_namespace>` med namnområdet för det bärbara `TodoItemManager` projektet som innehåller klassen.

2. I App.xaml.cs lägger du till följande **InitNotificationsAsync-metod:**

    ```csharp
    private async Task InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager
            .CreatePushNotificationChannelForApplicationAsync();

        const string templateBodyWNS =
            "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

        JObject headers = new JObject();
        headers["X-WNS-Type"] = "wns/toast";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
        {
            {"body", templateBodyWNS},
            {"headers", headers} // Needed for WNS.
        };

        await TodoItemManager.DefaultManager.CurrentClient.GetPush()
            .RegisterAsync(channel.Uri, templates);
    }
    ```

    Den här metoden hämtar push-meddelandekanalen och registrerar en mall för att ta emot mallmeddelanden från meddelandehubben. Ett mallmeddelande som stöder *messageParam* levereras till den här klienten.

3. I App.xaml.cs uppdaterar du metoddefinitionen **för OnLaunched-händelsehanterare** genom att lägga till `async` modifieraren. Lägg sedan till följande kodrad i slutet av metoden:

    ```csharp
    await InitNotificationsAsync();
    ```

    Detta säkerställer att push-meddelanderegistreringen skapas eller uppdateras varje gång appen startas. Det är viktigt att göra detta för att garantera att WNS push-kanal alltid är aktiv.  

4. Öppna filen **Package.appxmanifest** i Solution Explorer för Visual Studio och ange **Toast-kompatibel** till **Ja** under **Meddelanden**.
5. Skapa appen och kontrollera att du inte har några fel. Klientappen ska nu registrera sig för mallmeddelanden från sluten start i mobilapparna. Upprepa det här avsnittet för alla Windows-projekt i din lösning.

#### <a name="test-push-notifications-in-your-windows-app"></a>Testa push-meddelanden i Din Windows-app

1. Högerklicka på ett Windows-projekt i Visual Studio och klicka på **Ange som startprojekt**.
2. Tryck på **Run** (Kör) för att skapa projektet och starta appen.
3. Skriv ett namn på en ny todoitem i appen**+** och klicka sedan på plusikonen ( för att lägga till den.
4. Kontrollera att ett meddelande tas emot när objektet läggs till.

## <a name="next-steps"></a>Nästa steg

Du kan läsa mer om push-meddelanden:

* [Skicka push-meddelanden från Azure Mobile Apps](https://developer.xamarin.com/guides/xamarin-forms/cloud-services/push-notifications/azure/)
* [Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/firebase-cloud-messaging/)
* [Fjärrmeddelanden med Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/)
* [Diagnostisera problem med push-meddelanden](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  Det finns olika orsaker till varför meddelanden kan tas bort eller inte hamnar på enheter. Det här avsnittet visar hur du analyserar och räkna ut orsaken till push-meddelandefel.

Du kan också fortsätta till någon av följande självstudier:

* [Lägg till autentisering i appen](app-service-mobile-xamarin-forms-get-started-users.md)  
  Läs om hur du autentiserar användare i appen med en identitetsleverantör.
* [Aktivera offlinesynkronisering av appen](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Läs om hur du lägger till offlinestöd i appen genom en Mobile Apps-serverdel. Med offlinesynkronisering kan användare&mdash;interagera med en mobilapp&mdash;som visar, lägger till eller ändrar data även när det inte finns någon nätverksanslutning.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: https://go.microsoft.com/fwlink/p/?LinkId=272333
