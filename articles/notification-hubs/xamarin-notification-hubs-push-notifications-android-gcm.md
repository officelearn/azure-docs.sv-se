<properties
    pageTitle="Komma igång med Notification Hubs för Xamarin.Android-appar | Microsoft Azure"
    description="I den här självstudiekursen beskrivs hur du använder Azure Notification Hubs för att skicka push-meddelanden till en Xamarin-Android-app."
    authors="wesmc7777"
    manager="erikre"
    editor=""
    services="notification-hubs"
    documentationCenter="xamarin"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="06/29/2016"
    ms.author="wesmc"/>


# Komma igång med Notification Hubs med Xamarin för Android

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Översikt

I den här självstudiekursen beskrivs hur du använder Azure Notification Hubs för att skicka push-meddelanden till en Xamarin.Android-app.
Du skapar en tom Xamarin.Android-app som tar emot push-meddelanden genom att använda Google Cloud Messaging (GCM). När du är klar kan du använda meddelandehubben till att skicka push-meddelanden till alla enheter som kör appen. Den färdiga koden finns i exemplet [NotificationHubs app][GitHub].

I den här självstudiekursen visas ett enkelt scenario för sändning med Notification Hubs.


## Innan du börjar

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Den färdiga koden för den här självstudiekursen hittar du på GitHub [här](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid).



##Krav

För den här kursen behöver du följande:

+ Visual Studio med Xamarin i Windows eller Xamarin Studio i Mac OS X. Fullständiga installationsanvisningar finns i avsnittet [Konfiguration och installation av Visual Studio och Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).
+ Aktivt Google-konto
+ [Azure Messaging-komponent]
+ [Google Cloud Messaging-klientkomponent]

Du måste slutföra den här självstudiekursen innan du påbörjar någon annan kurs om Notification Hubs för Xamarin.Android-appar.

> [AZURE.IMPORTANT] Du måste ha ett aktivt Azure-konto för att slutföra den här kursen. Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [här](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F).

##Aktivera Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##Konfigurera meddelandehubben

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


<ol start="7">
<li><p>Klicka på fliken <b>Konfigurera</b> högst upp, ange <b>API-nyckelvärdet</b> som du fick i det förra avsnittet och klicka sedan på <b>Spara</b>.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hub-configure-android.png)


Meddelandehubben har nu konfigurerats för att fungera med GCM och du har anslutningssträngar för att registrera din app för att både ta emot meddelanden och skicka push-meddelanden.

##Anslut appen till meddelandehubben

###Skapa ett nytt projekt

1. I Xamarin Studio klickar du på **Ny lösning**, sedan på **Android-app** och till sist på **Nästa**.

    ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png)

2. Ange **appens namn** och **ID**. Klicka på de **målplattformar** du vill stödja och klicka sedan på **Nästa** och **Skapa**.

    ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png)


    På så sätt skapas ett nytt Android-projekt.

2. Öppna projektegenskaperna genom att högerklicka på det nya projektet i vyn Lösning och välj **Alternativ**. Välj objektet för **Android-appen** i avsnittet **Skapa**.

    Kontrollera att den första bokstaven i **paketnamnet** är en gemen.

    > [AZURE.IMPORTANT] Den första bokstaven i paketnamnet måste vara en gemen. Annars visas appmanifestfel när du registrerar **BroadcastReceiver** och **IntentFilter** för push-meddelanden nedan.

    ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub--xamarin-android-app-options.png)


3. Du kan också ställa in den **äldsta tillåtna Android-versionen** till en annan API-nivå.

4. Du kan också ställa in **målversionen av Android** till en annan API-version som du vill ha som mål (måste vara API-nivå 8 eller senare).

Klicka på **OK** och stäng dialogrutan Projektalternativ.


###Lägg till de nödvändiga komponenterna i projektet

Google Cloud Messaging-klienten i Xamarin-komponentlagret gör det enklare att ge stöd för push-meddelanden i Xamarin.Android.

1. Högerklicka på mappen Komponenter i Xamarin.Android-appen och välj **Get more Components** (Få fler komponenter).

2. Sök efter **Azure Messaging**-komponenten och lägg till den i projektet.

3. Sök efter **Google Cloud Messaging-klienten** och lägg till den i projektet.


###Konfigurera meddelandehubbar i projektet

1. Samla in följande information för Android-appen och meddelandehubben:

    - **Google-projektnummer**: Hämta värdet för det här projektnumret från översikten över appen på Google Developer-portalen. Du antecknade värdet tidigare när du skapade appen på portalen.
    - **Lyssna anslutningssträng**: På instrumentpanelen på den [Klassisk Azure-portal] klickar du på **Visa anslutningssträngar**. Kopiera anslutningssträngen *DefaultListenSharedAccessSignature* för det här värdet.
    - **Hubbnamn**: Det här är namnet på hubben från den [Klassisk Azure-portal]. Till exempel *mynotificationhub2*.

    Skapa en **Constants.cs**-klass för Xamarin-projektet och definiera följande konstanta värden i klassen. Ersätt platshållarna med värdena.

        public static class Constants
        {
            public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number
            public const string ListenConnectionString = "<Listen connection string>";
            public const string NotificationHubName = "<hub name>";
        }

2. Lägg till följande using-instruktioner till **MainActivity.cs** :

        using Android.Util;
        using Gcm.Client;

3. Lägg till en variabelinstans till den `MainActivity`-klass som ska användas för att visa en varningsruta när appen körs:

        public static MainActivity instance;


3. Skapa följande metod i klassen **MainActivity**:

        private void RegisterWithGCM()
        {
            // Check to ensure everything's set up right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            Log.Info("MainActivity", "Registering...");
            GcmClient.Register(this, Constants.SenderID);
        }

4. I `OnCreate`-metoden för **MainActivity.cs** initierar du `instance`-variabeln och lägger till ett anrop till `RegisterWithGCM`:

        protected override void OnCreate (Bundle bundle)
        {
            instance = this;

            base.OnCreate (bundle);

            // Set your view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Get your button from the layout resource,
            // and attach an event to it
            Button button = FindViewById<Button> (Resource.Id.myButton);

            RegisterWithGCM();
        }


4. Skapa en ny klass, **MyBroadcastReceiver**.

    > [AZURE.NOTE] Nedan går vi igenom hur du skapar en **BroadcastReceiver**-klass från grunden. Ett snabbt alternativ till att skapa **MyBroadcastReceiver.cs** manuellt är att öppna filen **GcmService.cs**, som finns i exemplet på Xamarin.Android-projektet som ingår i exemplen [NotificationHubs ][GitHub]. Att duplicera **GcmService.cs** och ändra klassnamn kan också vara en bra start.

5. Lägg till följande using-instruktioner till **MyBroadcastReceiver.cs** (hänvisa till komponenten och sammansättningen som du lagt till tidigare):

        using System.Collections.Generic;
        using System.Text;
        using Android.App;
        using Android.Content;
        using Android.Util;
        using Gcm.Client;
        using WindowsAzure.Messaging;

5. I **MyBroadcastReceiver.cs** lägger du till följande behörighetsbegäranden mellan **using**-instruktionen och deklarationen för **namnområdet**:

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

        //GET_ACCOUNTS is needed only for Android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

6. I **MyBroadcastReceiver.cs** ändrar du **MyBroadcastReceiver**-klassen så att den stämmer överens med följande:

        [BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<PushHandlerService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };

            public const string TAG = "MyBroadcastReceiver-GCM";
        }

7. Lägg till ytterligare en klass i **MyBroadcastReceiver.cs** med namnet **PushHandlerService**, som kommer från **GcmServiceBase**. Tillämpa attributet för **tjänsten** på klassen:

        [Service] // Must use the service tag
        public class PushHandlerService : GcmServiceBase
        {
            public static string RegistrationID { get; private set; }
            private NotificationHub Hub { get; set; }

            public PushHandlerService() : base(Constants.SenderID)
            {
                Log.Info(MyBroadcastReceiver.TAG, "PushHandlerService() constructor");
            }
        }


8. **GcmServiceBase** implementerar metoderna **OnRegistered()**, **OnUnRegistered()**, **OnMessage()**, **OnRecoverableError()** och **OnError()**. Vår implementeringsklass **PushHandlerService** måste åsidosätta de här metoderna, vilka utlöses som svar på interaktionen med meddelandehubben.


9. Åsidosätt metoden **OnRegistered()** i **PushHandlerService** med följande kod:

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("PushHandlerService-GCM Registered...",
                                "The device has been Registered!");

            Hub = new NotificationHub(Constants.NotificationHubName, Constants.ListenConnectionString,
                                        context);
            try
            {
                Hub.UnregisterAll(registrationId);
            }
            catch (Exception ex)
            {
                Log.Error(MyBroadcastReceiver.TAG, ex.Message);
            }

            //var tags = new List<string>() { "falcons" }; // create tags if you want
            var tags = new List<string>() {};

            try
            {
                var hubRegistration = Hub.Register(registrationId, tags.ToArray());
            }
            catch (Exception ex)
            {
                Log.Error(MyBroadcastReceiver.TAG, ex.Message);
            }
        }

    > [AZURE.NOTE] Observera möjligheten att specificera taggar för registrering för vissa meddelandekanaler i koden för **OnRegistered()**.

10. Åsidosätt metoden **OnMessage** i **PushHandlerService** med följande kod:

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(MyBroadcastReceiver.TAG, "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            string messageText = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty (messageText))
            {
                createNotification ("New hub message!", messageText);
            }
            else
            {
                createNotification ("Unknown message details", msg.ToString ());
            }
        }

11. Lägg till följande **createNotification**- och **dialogNotify**-metoder till **PushHandlerService** för att meddela användarna när ett meddelande tas emot.

    >[AZURE.NOTE] Meddelandedesign i Android-versionen 5.0 eller senare skiljer sig avsevärt från tidigare versioner. Om du testar detta i Android 5.0 eller senare måste appen köras för att ta emot meddelandet. Mer information om Android-meddelanden finns [här](http://go.microsoft.com/fwlink/?LinkId=615880).

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show UI
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Create the notification
            var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);

            //Auto-cancel will remove the notification once the user touches it
            notification.Flags = NotificationFlags.AutoCancel;

            //Set the notification info
            //we use the pending intent, passing our ui intent over, which will get called
            //when the notification is tapped.
            notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));

            //Show the notification
            notificationManager.Notify(1, notification);
            dialogNotify (title, desc);
        }

        protected void dialogNotify(String title, String message)
        {

            MainActivity.instance.RunOnUiThread(() => {
                AlertDialog.Builder dlg = new AlertDialog.Builder(MainActivity.instance);
                AlertDialog alert = dlg.Create();
                alert.SetTitle(title);
                alert.SetButton("Ok", delegate {
                    alert.Dismiss();
                });
                alert.SetMessage(message);
                alert.Show();
            });
        }


12. Åsidosätt de abstrakta medlemmarna **OnUnRegistered()**, **OnRecoverableError()** och **OnError()** så att din kod kompileras:

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Unregistered: " + registrationId);

            createNotification("GCM Unregistered...", "The device has been unregistered!");
        }

        protected override bool OnRecoverableError(Context context, string errorId)
        {
            Log.Warn(MyBroadcastReceiver.TAG, "Recoverable Error: " + errorId);

            return base.OnRecoverableError (context, errorId);
        }

        protected override void OnError(Context context, string errorId)
        {
            Log.Error(MyBroadcastReceiver.TAG, "GCM Error: " + errorId);
        }


##Kör appen i emulatorn

Se till att använda en virtuell Android-enhet (AVD) som har stöd för Google-API:er om du kör den här appen i emulatorn.

> [AZURE.IMPORTANT] Om du vill kunna ta emot push-meddelanden måste du skapa ett Google-konto på din virtuella Android-enhet. (I emulatorn navigerar du till **Inställningar** och klickar på **Lägg till konto**.) Se också till att emulatorn är ansluten till Internet.

>[AZURE.NOTE] Meddelandedesign i Android-versionen 5.0 eller senare skiljer sig avsevärt från tidigare versioner. Mer information om Android-meddelanden finns [här](http://go.microsoft.com/fwlink/?LinkId=615880).


1. I **Verktyg** klickar du på **Open Android Emulator Manager** (Öppna hanteraren för Android-emulator), väljer enheten och klickar sedan på **Redigera**.

    ![][18]

2. Välj **Google APIs** (API:er för Google) i **Mål** och klicka på **OK**.

    ![][19]

3. Klicka på **Kör** i det översta verktygsfältet och välj appen. Då startas emulatorn och kör appen.

  Appen hämtar *registrerings-ID:t* från GCM och registreras med meddelandehubben.

##Skicka meddelanden från serverdelen


Du testar att ta emot meddelanden i appen genom att skicka meddelanden i den [Klassisk Azure-portal] via felsökningsfliken i meddelandehubben, så som visas på skärmen nedan.

![][30]


Push-meddelanden skickas vanligtvis via en serverdelstjänst som Mobile Services eller ASP.NET med hjälp av ett kompatibelt bibliotek. Du kan också använda REST-API:et direkt för att skicka meddelanden om ett bibliotek inte är tillgängligt för din serverdel.

Här är en lista med andra självstudiekurser du kan gå igenom som handlar om att skicka meddelanden:

- ASP.NET: Se [Använda Notification Hubs för push-meddelanden till användare].
- Azure Notification Hubs Java SDK: Se [Använda Notification Hubs från Java](notification-hubs-java-push-notification-tutorial.md) för att skicka meddelanden från Java. Det här har testats i Eclipse för Android-utveckling.
- PHP: Se [Använda Notification Hubs från PHP](notification-hubs-php-push-notification-tutorial.md).


I nästa underavsnitt i självstudiekursen skickar du meddelanden med hjälp av en app för .NET-konsolen och genom att använda en mobiltjänst via ett nod-skript.

####(Valfritt) Skicka meddelanden med hjälp av en .NET-app

I det här avsnittet skickar du meddelanden med hjälp av en .NET-konsolapp

1. Skapa en ny Visual C#-konsolapp:

    ![][20]

2. I Visual Studio klickar du på **Verktyg**, **NuGet Package Manager** och sedan på **Package Manager-konsolen**.

    Då visas Package Manager-konsolen i Visual Studio.

3. I fönstret Package Manager-konsol ställer du in **standardprojektet** till det nya projektet för konsolprogrammet. Sedan kör du följande kommando i konsolfönstret:

        Install-Package Microsoft.Azure.NotificationHubs

    Då läggs en referens till i Azure Notification Hubs SDK med hjälp av <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification Hubs-NuGet-paketet</a>.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

4. Öppna Program.cs-filen och lägg till följande `using`-instruktion:

        using Microsoft.Azure.NotificationHubs;

5. Lägg till följande metod i `Program`-klassen. Uppdatera platshållartexten med anslutningssträngen *DefaultFullSharedAccessSignature* och hubbens namn från den [Klassisk Azure-portal].

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"message\":\"Hello from Azure!\"}}");
        }

6. Lägg till följande rader i **Main**-metoden:

         SendNotificationAsync();
         Console.ReadLine();

7. Kör appen genom att trycka på F5. Du bör få ett meddelande i appen.

    ![][21]

####(Valfritt) Skicka meddelanden med hjälp av en mobiltjänst

1. Följ [Komma igång med Mobile Services].

1. Logga in på den [Klassisk Azure-portal] och välj mobiltjänst.

2. Välj fliken **Schemaläggaren** högst upp.

    ![][22]

3. Skapa ett nytt schemalagt jobb, infoga ett namn och välj **På begäran**.

    ![][23]

4. Klicka på jobbnamnet när jobbet skapats. Klicka på fliken **Skript** i det översta fältet.

5. Infoga följande skript i schemaläggarfunktionen. Ersätt platshållarna med namnet på din meddelandehubb och anslutningssträngen för *DefaultFullSharedAccessSignature* som du fick tidigare. Klicka på **Spara**.

        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string>');
        notificationHubService.gcm.send(null,'{"data":{"message" : "Hello from Mobile Services!"}}',
          function (error)
          {
            if (!error) {
               console.warn("Notification successful");
            }
            else
            {
              console.warn("Notification failed" + error);
            }
          }
        );

6. Klicka på **Kör en gång** i det nedre fältet. Du bör få ett popup-meddelande.

##Nästa steg

I det här enkla exemplet skickade du meddelanden till alla dina Android-enheter. Mer information om hur du riktar in dig på specifika användare finns i självstudiekursen [Använda Notification Hubs för push-meddelanden till användare]. Om du vill dela in användarna efter intressegrupper läser du [Använda Notification Hubs för att skicka de senaste nyheterna]. Mer information om hur du använder Notification Hubs finns i [Riktlinjer för Notification Hubs] och [Notification Hubs-instruktioner för Android].

<!-- Anchors. -->
[Aktivera Google Cloud Messaging]: #register
[Konfigurera din meddelandehubb]: #configure-hub
[Ansluta appen till meddelandehubben]: #connecting-app
[Kör appen med emulatorn]: #run-app
[Skicka meddelanden från serverdelen]: #send
[Nästa steg]:#next-steps

<!-- Images. -->

[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler2.png

[30]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png


<!-- URLs. -->
[Skicka en app-sida]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mina program]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK för Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Komma igång med Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript och HTML]: /develop/mobile/tutorials/get-started-with-push-js

[Klassisk Azure-portal]: https://manage.windowsazure.com/
[wns-objekt]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Riktlinjer för Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs-instruktioner för Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Använda Notification Hubs för push-meddelanden till användare]: /manage/services/notification-hubs/notify-users-aspnet
[Använda Notification Hubs för att skicka de senaste nyheterna]: /manage/services/notification-hubs/breaking-news-dotnet
[Sidan GCMClient-komponent]: http://components.xamarin.com/view/GCMClient
[Sidan Xamarin.NotificationHub GitHub]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Google Cloud Messaging-klientkomponent]: http://components.xamarin.com/view/GCMClient/
[Azure Messaging-komponent]: http://components.xamarin.com/view/azure-messaging



<!--HONumber=Sep16_HO3-->


