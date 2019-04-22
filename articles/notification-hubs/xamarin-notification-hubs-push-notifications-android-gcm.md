---
title: Skicka meddelanden till Xamarin.Android-appar med Azure Notification Hubs | Microsoft Docs
description: I den här självstudiekursen beskrivs hur du använder Azure Notification Hubs för att skicka push-meddelanden till en Xamarin-Android-app.
author: jwargo
manager: patniko
editor: spelluru
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/28/2019
ms.author: jowargo
ms.openlocfilehash: 79913fc300f2ca66a84cf47c0e5b650b9ea2cc59
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58878782"
---
# <a name="tutorial-push-notifications-to-xamarinandroid-apps-using-azure-notification-hubs"></a>Självstudier: Skicka push-meddelanden till Xamarin.Android-appar med hjälp av Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Översikt

I den här självstudiekursen beskrivs hur du använder Azure Notification Hubs för att skicka push-meddelanden till en Xamarin.Android-app. Du skapar en tom Xamarin.Android-app som tar emot push-meddelanden via Firebase Cloud Messaging (FCM). Du använder meddelandehubben för att sända push-meddelanden till alla enheter som kör appen. Den färdiga koden finns i den [NotificationHubs app](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/Xamarin/GetStartedXamarinAndroid) exemplet.

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa ett Firebase-projekt och aktivera Firebase Cloud Messaging
> * Skapa en meddelandehubb
> * Skapa en Xamarin.Android-app och anslut den till meddelandehubben
> * Skicka testmeddelanden från Azure Portal

## <a name="prerequisites"></a>Nödvändiga komponenter

* **Azure-prenumeration**. Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
* [Visual Studio med Xamarin] på Windows eller [Visual Studio för Mac] på OS X.
* Aktivt Google-konto

## <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging"></a>Skapa ett Firebase-projekt och aktivera Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging-xamarin.md)]

## <a name="create-a-notification-hub"></a>Skapa en meddelandehubb

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcm-settings-for-the-notification-hub"></a>Konfigurera GCM-inställningarna för meddelandehubben

1. Välj **Google (GCM)** i avsnittet **MEDDELANDEINSTÄLLNINGAR**.
2. Ange den **äldre servernyckel** du antecknade från Google Firebase-konsolen.
3. Välj **Spara** i verktygsfältet.

    ![](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Meddelandehubben har konfigurerats för att fungera med FCM och du har anslutningssträngar för att registrera din app för att både ta emot meddelanden och skicka push-meddelanden.

## <a name="create-a-xamarinandroid-app-and-connect-it-to-notification-hub"></a>Skapa en Xamarin.Android-app och anslut den till meddelandehubben

### <a name="create-visual-studio-project-and-add-nuget-packages"></a>Skapa Visual Studio-projekt och lägg till NuGet-paket

1. I Visual Studio öppnar du menyn **Arkiv**. Välj **Nytt** och sedan **Projekt**. I den **nytt projekt** fönstret gör du följande: 
    1. Expandera **installerad**, **Visual C#** , och klicka sedan på **Android**.
    2. Välj **Android-App (Xamarin)** i listan. 
    3. Ange ett **namn** för projektet. 
    4. Välj en **plats** för projektet. 
    5. Välj **OK** 

        ![Dialogrutan Nytt projekt](./media/partner-xamarin-notification-hubs-android-get-started/new-project-dialog-new.png)        
2. På den **ny Android-App** dialogrutan **tom App**, och välj **OK**. 

    ![Dialogrutan Nytt projekt](./media/partner-xamarin-notification-hubs-android-get-started/new-android-app-dialog.png)
1. Utvidga **Egenskaper** i fönstret **Solution Explorer** och klicka på **AndroidManifest.xml**. Uppdatera paketnamnet så att det matchar paketnamnet som du angav när du lade till Firebase Cloud Messaging till ditt projekt i Google Firebase-konsolen.

    ![Paketnamn i GCM](./media/partner-xamarin-notification-hubs-android-get-started/package-name-gcm.png)
3. Högerklicka på projektet och välj **Hantera NuGet-paket...**.
4. Välj fliken **Bläddra**. Sök efter **Xamarin.GooglePlayServices.Base**. Välj **Xamarin.GooglePlayServices.Base** i resultatlistan. Markera **Installera**.

    ![Google Play Services NuGet](./media/partner-xamarin-notification-hubs-android-get-started/google-play-services-nuget.png)
5. Sök efter **Xamarin.Firebase.Messaging** i fönstret **NuGet Package Manager**. Välj **Xamarin.Firebase.Messaging** i resultatlistan. Markera **Installera**.
6. Sök efter **Xamarin.Azure.NotificationHubs.Android**. Välj **Xamarin.Azure.NotificationHubs.Android** i resultatlistan. Markera **Installera**.

### <a name="add-the-google-services-json-file"></a>Lägg till Google Services JSON-filen

1. Kopiera den `google-services.json`-fil som du laddade ned från Google Firebase-konsolen till projektmappen.
2. Lägg till `google-services.json` i projektet.
3. Välj `google-services.json` i **Solution Explorer**-fönstret.
4. Ställ in Skapa-åtgärden på **GoogleServicesJson** i rutan **Egenskaper**. Om **GoogleServicesJson** inte visas, så stäng och starta om Visual Studio, öppna projektet på nytt och försök igen.

    ![Skapa-åtgärd för GoogleServicesJson](./media/partner-xamarin-notification-hubs-android-get-started/google-services-json-build-action.png)

### <a name="set-up-notification-hubs-in-your-project"></a>Konfigurera meddelandehubbar i projektet

#### <a name="registering-with-firebase-cloud-messaging"></a>Registering med Firebase Cloud Messaging

1. Öppna filen `AndroidManifest.xml` och infoga följande `<receiver>`-element i elementet `<application>`:

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
2. Lägg till följande uttryck **innan programmet** element. 

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    ```
1. Samla in följande information för Android-appen och meddelandehubben:

   * **Lyssna anslutningssträng**: På instrumentpanelen i [Azure Portal] väljer du **Visa anslutningssträngar**. Kopiera `DefaultListenSharedAccessSignature`-anslutningssträngen för det här värdet.
   * **Hubbnamn**: Namnet på hubben från [Azure Portal]. Till exempel *mynotificationhub2*.
3. I **Solution Explorer**-fönstret högerklickar du på ditt **projekt** och väljer **Lägg till** följt av **Klass**.
4. Skapa en `Constants.cs`-klass för Xamarin-projektet och definiera följande konstantvärden i klassen. Ersätt platshållarna med värdena.

    ```csharp
    public static class Constants
    {
        public const string ListenConnectionString = "<Listen connection string>";
        public const string NotificationHubName = "<hub name>";
    }
    ```
5. Lägg till följande using-uttryck i `MainActivity.cs`:

    ```csharp
    using Android.Util;
    using Android.Gms.Common;
    ```
6. Lägg till följande egenskaper till MainActivity-klassen. TAGG-variabeln används för att visa en varningsruta när appen körs:

    ```csharp
    public const string TAG = "MainActivity";
    internal static readonly string CHANNEL_ID = "my_notification_channel";
    ```
7. Lägga till följande metod i klassen MainActivity. Kontrollerar den om **Google Play-tjänster** är tillgängliga på enheten. 

    ```csharp
    public bool IsPlayServicesAvailable()
    {
        int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.Success)
        {
            if (GoogleApiAvailability.Instance.IsUserResolvableError(resultCode))
                Log.Debug(TAG, GoogleApiAvailability.Instance.GetErrorString(resultCode));
            else
            {
                Log.Debug(TAG, "This device is not supported");
                Finish();
            }
            return false;
        }
     
        Log.Debug(TAG, "Google Play Services is available.");
        return true;
    }
    ```
1. Lägga till följande metod i klassen MainActivity som skapar en meddelandekanal.

    ```csharp
    private void CreateNotificationChannel()
    {
        if (Build.VERSION.SdkInt < BuildVersionCodes.O)
        {
            // Notification channels are new in API 26 (and not a part of the
            // support library). There is no need to create a notification
            // channel on older versions of Android.
            return;
        }
     
        var channelName = CHANNEL_ID;
        var channelDescription = string.Empty;
        var channel = new NotificationChannel(CHANNEL_ID, channelName, NotificationImportance.Default)
        {
            Description = channelDescription
        };
     
        var notificationManager = (NotificationManager)GetSystemService(NotificationService);
        notificationManager.CreateNotificationChannel(channel);
    }
    ```
1. I `MainActivity.cs` lägger du till följande kod i `OnCreate` efter `base.OnCreate(savedInstanceState)`:

    ```csharp
    if (Intent.Extras != null)
    {
        foreach (var key in Intent.Extras.KeySet())
        {
            if(key!=null)
            {
                var value = Intent.Extras.GetString(key);
                Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
            }
        }
    }
    
    IsPlayServicesAvailable();
    CreateNotificationChannel();
    ```
8. Skapa en ny klass, `MyFirebaseIIDService`, på samma sätt som du skapade klassen `Constants`.
9. Lägg till följande using-uttryck i `MyFirebaseIIDService.cs`:

    ```csharp
    using Android.App;
    using Android.Util;
    using WindowsAzure.Messaging;
    using Firebase.Iid;
    ```

10. I `MyFirebaseIIDService.cs` lägger du till följande `class`-deklaration och gör så att klassen ärver från `FirebaseInstanceIdService`:

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
    public class MyFirebaseIIDService : FirebaseInstanceIdService
    ```
11. I `MyFirebaseIIDService.cs` lägger du till följande kod:

    ```csharp
    const string TAG = "MyFirebaseIIDService";
    NotificationHub hub;

    public override void OnTokenRefresh()
    {
        var refreshedToken = FirebaseInstanceId.Instance.Token;
        Log.Debug(TAG, "FCM token: " + refreshedToken);
        SendRegistrationToServer(refreshedToken);
    }

    void SendRegistrationToServer(string token)
    {
        // Register with Notification Hubs
        hub = new NotificationHub(Constants.NotificationHubName,
                                    Constants.ListenConnectionString, this);

        var tags = new List<string>() { };
        var regID = hub.Register(token, tags.ToArray()).RegistrationId;

        Log.Debug(TAG, $"Successful registration of ID {regID}");
    }
    ```
12. Skapa ytterligare en ny klass för ditt projekt och ge den namnet `MyFirebaseMessagingService`.
13. Lägg till följande using-uttryck i `MyFirebaseMessagingService.cs`.

    ```csharp
    using Android.App;
    using Android.Util;
    using Firebase.Messaging;
    using Android.OS;
    using Android.Support.V4.App;
    using Build = Android.OS.Build;
    ```
14. Lägg till följande ovanför klassdeklarationen och gör så att klassen ärver från `FirebaseMessagingService`:

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
    public class MyFirebaseMessagingService : FirebaseMessagingService
    ```
15. Lägg till följande kod i `MyFirebaseMessagingService.cs`:

    ```csharp
    const string TAG = "MyFirebaseMsgService";
    public override void OnMessageReceived(RemoteMessage message)
    {
        Log.Debug(TAG, "From: " + message.From);
        if(message.GetNotification()!= null)
        {
            //These is how most messages will be received
            Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
            SendNotification(message.GetNotification().Body);
        }
        else
        {
            //Only used for debugging payloads sent from the Azure portal
            SendNotification(message.Data.Values.First());

        }
    }

    void SendNotification(string messageBody)
    {
        var intent = new Intent(this, typeof(MainActivity));
        intent.AddFlags(ActivityFlags.ClearTop);
        var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

        var notificationBuilder = new NotificationCompat.Builder(this)
                    .SetContentTitle("FCM Message")
                    .SetSmallIcon(Resource.Drawable.ic_launcher)
                    .SetContentText(messageBody)
                    .SetAutoCancel(true)
                    .SetShowWhen(false)
                    .SetContentIntent(pendingIntent);

        if (Build.VERSION.SdkInt >= BuildVersionCodes.O)
        {
            notificationBuilder.SetChannelId(MainActivity.CHANNEL_ID);
        }

        var notificationManager = NotificationManager.FromContext(this);

        notificationManager.Notify(0, notificationBuilder.Build());
    }
    ```
16. **Skapa** ditt projekt.
17. **Kör** appen på din enhet eller i en inläst emulator

## <a name="send-test-notification-from-the-azure-portal"></a>Skicka ett testmeddelande från Azure Portal

Du kan testa att ta emot meddelanden i appen med alternativet **Skicka test** i [Azure Portal]. Den skickar ett test-push-meddelande till enheten.

![Azure Portal – Skicka test](media/partner-xamarin-notification-hubs-android-get-started/send-test-notification.png)

Push-meddelanden skickas vanligtvis via en serverdelstjänst som Mobile Services eller ASP.NET med hjälp av ett kompatibelt bibliotek. Du kan också använda REST-API:et direkt för att skicka meddelanden om ett bibliotek inte är tillgängligt för din serverdel.

## <a name="next-steps"></a>Nästa steg

I de här självstudierna har du skickat meddelanden till alla Android-enheter som är registrerade hos serverdelen. Information om hur du skickar meddelanden till specifika Android-enheter finns i följande självstudier:

> [!div class="nextstepaction"]
>[Skicka meddelanden till specifika enheter](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->
[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png
[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png
[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png
[24]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-xamarin-android-app-options.png
[25]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-google-services-json.png
[30]: ./media/notification-hubs-android-get-started/notification-hubs-test-send.png

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Visual Studio med Xamarin]: https://docs.microsoft.com/visualstudio/install/install-visual-studio
[Visual Studio för Mac]: https://www.visualstudio.com/vs/visual-studio-mac/
[Azure Portal]: https://portal.azure.com/
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Android]: https://msdn.microsoft.com/library/dn282661.aspx
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[GitHub]: https://github.com/Azure/azure-notificationhubs-android
