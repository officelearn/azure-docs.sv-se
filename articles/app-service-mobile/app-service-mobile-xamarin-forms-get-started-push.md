---
title: Lägg till push-meddelanden i din Xamarin. Forms-app | Microsoft Docs
description: Lär dig hur du använder Azure-tjänster för att skicka push-meddelanden med flera plattformar till dina Xamarin. Forms-appar.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 8ce307df954575b3204f7a4b3f46af1f4a9c3089
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027459"
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Lägg till push-meddelanden i din Xamarin. Forms-app

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center stöder utveckling av mobila appar från slut punkt till slut punkt och integrerade tjänster. Utvecklare kan använda **bygge**-, **test** -och **distributions** tjänster för att konfigurera kontinuerlig integrering och leverans pipeliner. När appen har distribuerats kan utvecklare övervaka status och användning av appen med hjälp av **analys** -och **diagnos** tjänster och engagera med användare med **push** -tjänsten. Utvecklare kan också utnyttja **auth** för att autentisera sina användare och **data** tjänster för att spara och synkronisera AppData i molnet.
> Om du vill integrera moln tjänster i ditt mobil program kan du registrera dig med App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) idag.

## <a name="overview"></a>Översikt

I den här självstudien lägger du till push-meddelanden till alla projekt som har resulterat i [Xamarin. formulär snabb start](app-service-mobile-xamarin-forms-get-started.md). Det innebär att ett push-meddelande skickas till alla plattforms oberoende klienter varje gång en post infogas.

Om du inte använder det nedladdade snabb start Server-projektet behöver du ett tilläggs paket för push-meddelanden. Mer information finns i [arbeta med .NET-Server del Server SDK för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Förutsättningar

För iOS behöver du ett [Apple Developer program medlemskap](https://developer.apple.com/programs/ios/) och en fysisk iOS-enhet. [IOS-simulatorn stöder inte push-meddelanden](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

## <a name="configure-hub"></a>Konfigurera en Notification Hub

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Uppdatera Server projektet för att skicka push-meddelanden

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Konfigurera och kör Android-projektet (valfritt)

Slutför det här avsnittet om du vill aktivera push-meddelanden för Xamarin. Forms Droid-projektet för Android.

### <a name="enable-firebase-cloud-messaging-fcm"></a>Aktivera Firebase Cloud Messaging (FCM)

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>Konfigurera Mobile Apps Server delen för att skicka push-begäranden med hjälp av FCM

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Lägg till push-meddelanden i Android-projektet

Med Server delen som kon figurer ATS med FCM kan du lägga till komponenter och koder till klienten som ska registreras med FCM. Du kan också registrera dig för push-meddelanden med Azure Notification Hubs via Mobile Apps Server del och ta emot meddelanden.

1. I **Droid** -projektet högerklickar du på **referenser > Hantera NuGet-paket.** ...
1. I fönstret NuGet Package Manager söker du efter **Xamarin. Firebase. Messaging** -paketet och lägger till det i projektet.
1. I projekt egenskaperna för **Droid** -projektet ställer du in appen att kompilera med Android version 7,0 eller senare.
1. Lägg till filen **Google-Services. JSON** , som hämtats från Firebase-konsolen, till roten i **Droid** -projektet och ange dess build-åtgärd till **GoogleServicesJson**. Mer information finns i [lägga till Google Services JSON-filen](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/#Add_the_Google_Services_JSON_File).

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

#### <a name="implementing-the-firebase-instance-id-service"></a>Implementera Firebase instance ID service

1. Lägg till en ny klass i **Droid** -projektet med namnet `FirebaseRegistrationService` och kontrol lera att följande `using`-satser finns överst i filen:

    ```csharp
    using System.Threading.Tasks;
    using Android.App;
    using Android.Util;
    using Firebase.Iid;
    using Microsoft.WindowsAzure.MobileServices;
    ```

1. Ersätt den tomma `FirebaseRegistrationService`-klassen med följande kod:

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

    Klassen `FirebaseRegistrationService` ansvarar för att skapa säkerhetstoken som ger programmet åtkomst till FCM. Metoden `OnTokenRefresh` anropas när programmet tar emot en registrerings-token från FCM. Metoden hämtar token från egenskapen `FirebaseInstanceId.Instance.Token`, som uppdateras asynkront av FCM. Metoden `OnTokenRefresh` anropas sällan, eftersom token bara uppdateras när programmet installeras eller avinstalleras, när användaren tar bort program data när programmet raderar instans-ID: t eller när säkerheten för token har varit komprometterats. Dessutom kommer FCM instance ID-tjänsten begära att programmet uppdaterar sin token regelbundet, vanligt vis var 6: a månad.

    Metoden `OnTokenRefresh` anropar också metoden `SendRegistrationTokenToAzureNotificationHub` som används för att associera användarens registrerings-token med Azure Notification Hub.

#### <a name="registering-with-the-azure-notification-hub"></a>Registrera med Azure Notification Hub

1. Lägg till en ny klass i **Droid** -projektet med namnet `AzureNotificationHubService` och kontrol lera att följande `using`-satser finns överst i filen:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Android.Util;
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

1. Ersätt den tomma `AzureNotificationHubService`-klassen med följande kod:

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

    Metoden `RegisterAsync` skapar en enkel mall för aviserings meddelanden som JSON och registrerar sig för att ta emot mal meddelanden från Notification Hub med hjälp av Firebase-registrerings-token. Detta säkerställer att alla meddelanden som skickas från Azure Notification Hub är riktade mot enheten som representeras av registrerings-token.

#### <a name="displaying-the-contents-of-a-push-notification"></a>Visa innehållet i ett push-meddelande

1. Lägg till en ny klass i **Droid** -projektet med namnet `FirebaseNotificationService` och kontrol lera att följande `using`-satser finns överst i filen:

    ```csharp
    using Android.App;
    using Android.Content;
    using Android.Media;
    using Android.Support.V7.App;
    using Android.Util;
    using Firebase.Messaging;
    ```

1. Ersätt den tomma `FirebaseNotificationService`-klassen med följande kod:

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
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

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

    Metoden `OnMessageReceived`, som anropas när ett program tar emot ett meddelande från FCM, extraherar meddelande innehållet och anropar `SendNotification`-metoden. Med den här metoden konverteras meddelande innehållet till ett lokalt meddelande som startas medan programmet körs, med meddelandet som visas i meddelande fältet.

Nu är du redo att testa push-meddelanden i appen som körs på en Android-enhet eller emulatorn.

### <a name="test-push-notifications-in-your-android-app"></a>Testa push-meddelanden i din Android-app

De första två stegen krävs bara när du testar på en emulator.

1. Se till att du distribuerar till eller felsöker på en enhet eller emulator som är konfigurerad med Google Play-tjänster. Detta kan verifieras genom att kontrol lera att **Play** -apparna är installerade på enheten eller emulatorn.
2. Lägg till ett Google-konto till Android-enheten genom att klicka på **appar** > **Inställningar** > **Lägg till konto**. Följ sedan anvisningarna för att lägga till ett befintligt Google-konto på enheten eller skapa ett nytt.
3. I Visual Studio eller Xamarin Studio högerklickar du på projektet **Droid** och klickar på **Ställ in som start projekt**.
4. Klicka på **Kör** för att skapa projektet och starta appen på din Android-enhet eller emulator.
5. Skriv en aktivitet i appen och klicka sedan på plus ikonen ( **+** ).
6. Verifiera att ett meddelande tas emot när ett objekt läggs till.

## <a name="configure-and-run-the-ios-project-optional"></a>Konfigurera och kör iOS-projektet (valfritt)

Det här avsnittet handlar om att köra Xamarin iOS-projektet för iOS-enheter. Du kan hoppa över det här avsnittet om du inte arbetar med iOS-enheter.

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>Konfigurera Notification Hub för APN

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Sedan konfigurerar du inställningen för iOS-projektet i Xamarin Studio eller Visual Studio.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>Lägg till push-meddelanden till din iOS-app

1. Öppna AppDelegate.cs i **iOS** -projektet och Lägg till följande-instruktion överst i kod filen.

    ```csharp
    using Newtonsoft.Json.Linq;
    ```

2. I **AppDelegate** -klassen lägger du till en åsidosättning för händelsen **RegisteredForRemoteNotifications** för att registrera för meddelanden:

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

3. I **AppDelegate**lägger du också till följande åsidosättning för händelse hanteraren **DidReceiveRemoteNotification** :

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

    Den här metoden hanterar inkommande meddelanden när appen körs.

4. I **AppDelegate** -klassen lägger du till följande kod i **FinishedLaunching** -metoden:

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

    Detta aktiverar stöd för fjärraviseringar och begär push-registrering.

Appen har nu uppdaterats för att stödja push-meddelanden.

#### <a name="test-push-notifications-in-your-ios-app"></a>Testa push-meddelanden i din iOS-app

1. Högerklicka på iOS-projektet och klicka på **Ställ in som start projekt**.
2. Tryck på knappen **Kör** eller **F5** i Visual Studio för att skapa projektet och starta appen i en iOS-enhet. Klicka sedan på **OK** för att acceptera push-meddelanden.

   > [!NOTE]
   > Du måste uttryckligen acceptera push-meddelanden från din app. Den här begäran sker bara första gången appen körs.

3. Skriv en aktivitet i appen och klicka sedan på plus ikonen ( **+** ).
4. Kontrol lera att ett meddelande har tagits emot och klicka sedan på **OK** för att stänga meddelandet.

## <a name="configure-and-run-windows-projects-optional"></a>Konfigurera och köra Windows-projekt (valfritt)

Det här avsnittet används för att köra Xamarin. Forms WinApp-och WinPhone81-projekt för Windows-enheter. De här stegen stöder även Universell Windows-plattform (UWP)-projekt. Du kan hoppa över det här avsnittet om du inte arbetar med Windowsenheter.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>Registrera Windows-appen för push-meddelanden med Windows Notification Service (WNS)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>Konfigurera Notification Hub för WNS

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Lägga till push-meddelanden i Windows-appen

1. Öppna **app.XAML.cs** i ett Windows-projekt i Visual Studio och Lägg till följande-instruktioner.

    ```csharp
    using Newtonsoft.Json.Linq;
    using Microsoft.WindowsAzure.MobileServices;
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    using <your_TodoItemManager_portable_class_namespace>;
    ```

    Ersätt `<your_TodoItemManager_portable_class_namespace>` med namn området för det bärbara projektet som innehåller klassen `TodoItemManager`.

2. I App.xaml.cs lägger du till följande **InitNotificationsAsync** -Metod:

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

    Den här metoden hämtar push-meddelandets kanal och registrerar en mall för att ta emot mal meddelanden från Notification Hub. Ett meddelande i en mall som stöder *messageParam* levereras till den här klienten.

3. I App.xaml.cs uppdaterar du metod definitionen **OnLaunched** Event handler genom att lägga till modifieraren `async`. Lägg sedan till följande kodrad i slutet av-metoden:

    ```csharp
    await InitNotificationsAsync();
    ```

    Detta säkerställer att registreringen av push-meddelanden skapas eller uppdateras varje gång appen startas. Det är viktigt att du gör detta för att garantera att WNS-push-kanalen alltid är aktiv.  

4. Öppna filen **Package. appxmanifest** i Solution Explorer för Visual Studio och ange att **popup** -funktionen ska vara **Ja** under **meddelanden**.
5. Bygg appen och kontrol lera att du inte har några fel. Nu bör klient programmet registreras för mal meddelanden från Mobile Apps Server del. Upprepa det här avsnittet för alla Windows-projekt i din lösning.

#### <a name="test-push-notifications-in-your-windows-app"></a>Testa push-meddelanden i Windows-appen

1. I Visual Studio högerklickar du på ett Windows-projekt och klickar på **Ställ in som start projekt**.
2. Tryck på **Run** (Kör) för att skapa projektet och starta appen.
3. Skriv ett namn för en ny todoitem i appen och klicka sedan på plus ikonen ( **+** ) för att lägga till den.
4. Verifiera att ett meddelande tas emot när objektet läggs till.

## <a name="next-steps"></a>Nästa steg

Du kan läsa mer om push-meddelanden:

* [Skicka push-meddelanden från Azure Mobile Apps](https://developer.xamarin.com/guides/xamarin-forms/cloud-services/push-notifications/azure/)
* [Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/firebase-cloud-messaging/)
* [Fjärraviseringar med Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/)
* [Diagnostisera problem med push-meddelanden](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  Det finns olika orsaker till varför meddelanden kan släppas eller inte avslutas på enheterna. Det här avsnittet visar hur du analyserar och avbildar rotor saken till problem med push-meddelanden.

Du kan också fortsätta till någon av följande Självstudier:

* [Lägg till autentisering i appen](app-service-mobile-xamarin-forms-get-started-users.md)  
  Läs om hur du autentiserar användare i appen med en identitetsleverantör.
* [Aktivera offlinesynkronisering av appen](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Läs om hur du lägger till offlinestöd i appen genom en Mobile Apps-serverdel. Med offline-synkronisering kan användare interagera med en mobilapp @ no__t-0viewing, lägga till eller ändra data @ no__t-1even när det inte finns någon nätverks anslutning.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: https://go.microsoft.com/fwlink/p/?LinkId=272333
