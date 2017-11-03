---
title: "Lägg till push-meddelanden i appen Xamarin.Forms | Microsoft Docs"
description: "Lär dig hur du använder Azure-tjänster för att skicka flera plattformar push-meddelanden till Xamarin.Forms-appar."
services: app-service\mobile
documentationcenter: xamarin
author: ysxu
manager: syntaxc4
editor: 
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: yuaxu
ms.openlocfilehash: 912367636f1b26b3b07fbd5fe3fe8ed053218fd5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Lägg till push-meddelanden i appen Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Översikt
I kursen får du lägga till push-meddelanden till alla projekt som härrör från den [Xamarin.Forms Snabbstart](app-service-mobile-xamarin-forms-get-started.md). Det innebär att ett push-meddelande skickas till alla plattformar klienter varje gång en post infogas.

Om du inte använder hämtade Snabbstart serverprojekt behöver push notification extension-paketet. Mer information finns i [arbeta med serverdelen .NET SDK för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Krav
För iOS, behöver du en [Apple Developer Program medlemskap](https://developer.apple.com/programs/ios/) och en fysisk iOS-enhet. Den [stöder inte push-meddelanden i iOS-simulatorn](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

## <a name="configure-hub"></a>Konfigurera en meddelandehubb
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Uppdatera serverprojekt för att skicka push-meddelanden
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Konfigurera och köra androidprojektet (valfritt)
Slutför det här avsnittet om du vill aktivera push-meddelanden för Xamarin.Forms Droid-projektet för Android.

### <a name="enable-firebase-cloud-messaging-fcm"></a>Aktivera Firebase Cloud Messaging (FCM)
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>Konfigurera Mobile Apps serverdel för att skicka push-begäranden med hjälp av FCM
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Lägg till push-meddelanden i Android-projekt
På serversidan som har konfigurerats med FCM kan du lägga till komponenter och koder till klienten och registrera med FCM. Du kan också registrera för push-meddelanden med Azure Notification Hubs via Mobile Apps-serverdel och ta emot meddelanden.

1. I den **Droid** projekt, högerklicka på den **komponenter** mappen och klicka på **få fler komponenter...** . Sök sedan efter den **Google Cloud Messaging-klienten** komponenten och Lägg till den i projektet. Den här komponenten stöder push-meddelanden för en Xamarin Android-projekt.
2. Öppna projektfilen MainActivity.cs och Lägg till följande uttryck överst i filen:

        using Gcm.Client;
3. Lägg till följande kod i den **OnCreate** metod efter anropet till **LoadApplication**:

        try
        {
            // Check to ensure everything's set up right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
        }
        catch (Java.Net.MalformedURLException)
        {
            CreateAndShowDialog("There was an error creating the client. Verify the URL.", "Error");
        }
        catch (Exception e)
        {
            CreateAndShowDialog(e.Message, "Error");
        }
4. Lägg till en ny **CreateAndShowDialog** helper-metoden på följande sätt:

        private void CreateAndShowDialog(String message, String title)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);

            builder.SetMessage (message);
            builder.SetTitle (title);
            builder.Create().Show ();
        }
5. Lägg till följande kod i den **MainActivity** klass:

        // Create a new instance field for this activity.
        static MainActivity instance = null;

        // Return the current activity instance.
        public static MainActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }

    Detta visar aktuellt **MainActivity** instans, så vi kan köra på huvudsakliga UI-tråden.
6. Initiera den `instance` variabeln i början av den **OnCreate** metoden på följande sätt.

        // Set the current instance of MainActivity.
        instance = this;
7. Lägg till en ny klassfil och den **Droid** projektet med namnet `GcmService.cs`, och kontrollera att följande **med** instruktioner finns högst upp i filen:

        using Android.App;
        using Android.Content;
        using Android.Media;
        using Android.Support.V4.App;
        using Android.Util;
        using Gcm.Client;
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.Text;
8. Lägg till följande behörighetsbegäranden överst i filen, efter den **med** instruktioner och innan den **namnområde** deklaration.

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
9. Lägg till följande klassdefinition i namnområdet.

       [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
       public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
       {
           public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };
       }

   > [!NOTE]
   > Ersätt **< PROJECT_NUMBER >** med din projektnumret som du antecknade tidigare.    
   >
   >
10. Ersätt tomt **GcmService** klassen med följande kod, som använder den nya broadcast mottagaren:

         [Service]
         public class GcmService : GcmServiceBase
         {
             public static string RegistrationID { get; private set; }

             public GcmService()
                 : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
         }
11. Lägg till följande kod i den **GcmService** klass. Åsidosätts den **OnRegistered** händelsehanteraren och implementerar en **registrera** metod.

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose("PushHandlerBroadcastReceiver", "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            var push = TodoItemManager.DefaultManager.CurrentClient.GetPush();

            MainActivity.CurrentActivity.RunOnUiThread(() => Register(push, null));
        }

        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";

                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                    {"body", templateBodyGCM}
                };

                await push.RegisterAsync(RegistrationID, templates);
                Log.Info("Push Installation Id", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

    Observera att den här koden används den `messageParam` parameter i mallen för registrering.
12. Lägg till följande kod som implementerar **OnMessage**:

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info("PushHandlerBroadcastReceiver", "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            //Store the message
            var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
            var edit = prefs.Edit();
            edit.PutString("last_msg", msg.ToString());
            edit.Commit();

            string message = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty(message))
            {
                createNotification("New todo item!", "Todo item: " + message);
                return;
            }

            string msg2 = intent.Extras.GetString("msg");
            if (!string.IsNullOrEmpty(msg2))
            {
                createNotification("New hub message!", msg2);
                return;
            }

            createNotification("Unknown message details", msg.ToString());
        }

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Use Notification Builder
            NotificationCompat.Builder builder = new NotificationCompat.Builder(this);

            //Create the notification
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            var notification = builder.SetContentIntent(PendingIntent.GetActivity(this, 0, uiIntent, 0))
                    .SetSmallIcon(Android.Resource.Drawable.SymActionEmail)
                    .SetTicker(title)
                    .SetContentTitle(title)
                    .SetContentText(desc)

                    //Set the notification sound
                    .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))

                    //Auto cancel will remove the notification once the user touches it
                    .SetAutoCancel(true).Build();

            //Show the notification
            notificationManager.Notify(1, notification);
        }

    Detta hanterar inkommande meddelanden och skickar dem till notification manager som ska visas.
13. **GcmServiceBase** måste du implementera även de **OnUnRegistered** och **VidFel** hanterare metoder som du kan göra följande:

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
        }

        protected override void OnError(Context context, string errorId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
        }

Du är nu redo testa push-meddelanden i appen körs på en Android-enhet eller emulatorn.

### <a name="test-push-notifications-in-your-android-app"></a>Testa push-meddelanden i din Android-app
De två första stegen krävs bara när du testar på en emulator.

1. Se till att du distribuerar till eller felsökning på en virtuell enhet som har Google APIs som mål, enligt nedan i hanteraren för Android-enhet.
2. Lägg till ett Google-konto för Android-enhet genom att klicka på **appar** > **inställningar** > **Lägg till konto**. Följ sedan anvisningarna för att lägga till ett befintligt Google-konto till enheten eller skapa en ny.
3. I Visual Studio eller Xamarin Studio högerklickar du på den **Droid** projektet och klicka på **Ställ in som Startprojekt**.
4. Klicka på **kör** att skapa projektet och starta appen på din Android-enhet eller emulator.
5. I appen, skriver du en uppgift och klicka sedan på plustecknet (**+**) ikon.
6. Kontrollera att ett meddelande tas emot när ett objekt läggs till.

## <a name="configure-and-run-the-ios-project-optional"></a>Konfigurera och köra iOS-projektet (valfritt)
Det här avsnittet handlar om att köra Xamarin iOS-projektet för iOS-enheter. Du kan hoppa över det här avsnittet om du inte arbetar med iOS-enheter.

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>Konfigurera meddelandehubben för APNS
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Därefter konfigurerar du inställningen för iOS-projekt i Xamarin Studio eller Visual Studio.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>Lägg till push-meddelanden till iOS-app
1. I den **iOS** projekt, öppna AppDelegate.cs och Lägg till följande uttryck överst i kodfilen.

        using Newtonsoft.Json.Linq;
2. I den **AppDelegate** klassen, lägga till en åsidosättning för den **RegisteredForRemoteNotifications** händelse registreras för meddelanden om:

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
3. I **AppDelegate**, även lägga till följande åsidosättningen för den **DidReceiveRemoteNotification** händelsehanteraren:

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

    Den här metoden hanterar inkommande meddelanden när appen körs.
4. I den **AppDelegate** klassen, Lägg till följande kod i den **FinishedLaunching** metod:

        // Register for push notifications.
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

    Detta möjliggör stöd för remote meddelanden och att registrera push-begäranden.

Appen har uppdaterats för att stödja push-meddelanden.

#### <a name="test-push-notifications-in-your-ios-app"></a>Testa push-meddelanden i iOS-app
1. Högerklicka på iOS-projektet och klicka på **Set as StartUp Project**.
2. Tryck på den **kör** knappen eller **F5** i Visual Studio för att skapa projektet och starta appen i en iOS-enhet. Klicka på **OK** att ta emot push-meddelanden.

   > [!NOTE]
   > Du måste uttryckligen godkänna push-meddelanden från din app. Denna begäran inträffar bara första gången som appen körs.
   >
   >
3. I appen, skriver du en uppgift och klicka sedan på plustecknet (**+**) ikon.
4. Kontrollera att ett meddelande tas emot och klicka sedan på **OK** att stänga meddelandet.

## <a name="configure-and-run-windows-projects-optional"></a>Konfigurera och köra Windows projekt (valfritt)
Det här avsnittet avser kör Xamarin.Forms, WinApp och WinPhone81 projekt för Windows-enheter. De här stegen stöder också universella Windowsplattformen (UWP)-projekt. Du kan hoppa över det här avsnittet om du inte arbetar med Windowsenheter.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>Registrera din Windows-app för push-meddelanden med Windows Notification Service (WNS)
[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>Konfigurera meddelandehubben för WNS
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Lägg till push-meddelanden i appen för Windows
1. Öppna i Visual Studio **App.xaml.cs** i en Windows projekt och Lägg till följande uttryck.

        using Newtonsoft.Json.Linq;
        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;
        using <your_TodoItemManager_portable_class_namespace>;

    Ersätt `<your_TodoItemManager_portable_class_namespace>` med namnområdet för projektet bärbara som innehåller den `TodoItemManager` klass.
2. Lägg till följande i App.xaml.cs **InitNotificationsAsync** metod:

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

    Den här metoden hämtar meddelandekanal push och registrerar en mall för att ta emot meddelanden om mallar från din meddelandehubb. Ett meddelande i mallen som stöder *messageParam* levereras till klienten.
3. I filen App.xaml.cs ska du uppdatera den **OnLaunched** händelsehanterare metoddefinition genom att lägga till den `async` modifierare. Lägg sedan till följande kodrad i slutet av metoden:

        await InitNotificationsAsync();

    Detta säkerställer att registreringen av push-meddelanden skapas eller uppdateras varje gång appen startas. Det är viktigt att göra detta för att garantera att push-kanal WNS alltid är aktiv.  
4. I Solution Explorer för Visual Studio, öppna den **Package.appxmanifest** fil och anger **popup-kompatibel** till **Ja** under **meddelanden**.
5. Skapa appen och kontrollera att du har några fel. Klientappen bör nu registrera för mallen meddelanden från Mobile Apps-serverdel. Upprepa det här avsnittet för varje Windows-projekt i din lösning.

#### <a name="test-push-notifications-in-your-windows-app"></a>Testa push-meddelanden i Windows-appen
1. Högerklicka på en Windows-projekt i Visual Studio och klicka på **Ställ in som Startprojekt**.
2. Tryck på **Run** (Kör) för att skapa projektet och starta appen.
3. Skriv ett namn för en ny todoitem i appen och klicka sedan på plustecknet (**+**) ikon för att lägga till den.
4. Kontrollera att ett meddelande tas emot när objektet har lagts till.

## <a name="next-steps"></a>Nästa steg
Mer information om push-meddelanden:

* [Diagnostisera problem för push-meddelande](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  Det finns olika orsaker till varför meddelanden kan hämta bort eller inte hamnar på enheter. Det här avsnittet visar hur du analyserar och ta reda på orsaken till fel för push-meddelande.

Du kan också fortsätta in på något av följande kurser:

* [Lägg till autentisering till din app ](app-service-mobile-xamarin-forms-get-started-users.md)  
  Läs om hur du autentiserar användare i appen med en identitetsleverantör.
* [Aktivera offlinesynkronisering av appen](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Läs om hur du lägger till offlinestöd i appen genom en Mobile Apps-serverdel. Med offlinesynkronisering, användare kan interagera med en mobil app&mdash;visa, lägga till eller ändra data&mdash;även om det inte finns någon nätverksanslutning.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
