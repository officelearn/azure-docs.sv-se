---
title: Skicka meddelanden till Xamarin.Android-appar med Azure Notification Hubs | Microsoft Docs
description: I den här självstudiekursen beskrivs hur du använder Azure Notification Hubs för att skicka push-meddelanden till en Xamarin-Android-app.
author: dimazaid
manager: kpiteira
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
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: f75671e2e5511054f3db550a8c24e62d031492c3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776720"
---
# <a name="tutorial-push-notifications-to-xamarinandroid-apps-using-azure-notification-hubs"></a>Självstudier: Skicka meddelanden till Xamarin.Android-appar med Azure Notification Hubs
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Översikt
I den här självstudiekursen beskrivs hur du använder Azure Notification Hubs för att skicka push-meddelanden till en Xamarin.Android-app. Du skapar en tom Xamarin.Android-app som tar emot push-meddelanden via Firebase Cloud Messaging (FCM). Du använder meddelandehubben för att sända push-meddelanden till alla enheter som kör appen. Den färdiga koden finns tillgänglig i exemplet [NotificationHubs-app][GitHub].

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa ett Firebase-projekt och aktivera Firebase Cloud Messaging
> * Skapa en meddelandehubb
> * Skapa en Xamarin.Android-app och anslut den till meddelandehubben
> * Skicka testmeddelanden från Azure Portal

## <a name="prerequisites"></a>Nödvändiga komponenter

- **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- [Visual Studio med Xamarin] på Windows eller [Visual Studio för Mac] på OS X.
- Aktivt Google-konto

## <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging"></a>Skapa ett Firebase-projekt och aktivera Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="create-a-notification-hub"></a>Skapa en meddelandehubb
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcm-settings-for-the-notification-hub"></a>Konfigurera GCM-inställningarna för meddelandehubben

1. Välj **Google (GCM)** i avsnittet **MEDDELANDEINSTÄLLNINGAR**. 
2. Ange den **äldre servernyckel** du antecknade från Google Firebase-konsolen. 
3. Välj **Spara** i verktygsfältet. 

    ![](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Meddelandehubben har konfigurerats för att fungera med FCM och du har anslutningssträngar för att registrera din app för att både ta emot meddelanden och skicka push-meddelanden.

## <a name="create-xamainandroid-app-and-connect-it-to-notification-hub"></a>Skapa en Xamain.Android-app och anslut den till meddelandehubben

### <a name="create-visual-studio-project-and-add-nuget-packages"></a>Skapa Visual Studio-projekt och lägg till NuGet-paket
1. Peka på **Arkiv** i Visual Studio, välj **Nytt** och sedan **Projekt**. 
   
      ![Visual Studio – Skapa ett nytt Android-projekt](./media/partner-xamarin-notification-hubs-android-get-started/new-project-dialog.png)
2. Utvidga **Egenskaper** i fönstret **Solution Explorer** och klicka på **AndroidManifest.xml**. Uppdatera paketnamnet så att det matchar paketnamnet som du angav när du lade till Firebase Cloud Messaging till ditt projekt i Google Firebase-konsolen. 

      ![Paketnamn i GCM](./media/partner-xamarin-notification-hubs-android-get-started/package-name-gcm.png)
3. Högerklicka på projektet och välj **Hantera NuGet-paket...**. 
4. Välj fliken **Bläddra**. Sök efter **Xamarin.GooglePlayServices.Base**. Välj **Xamarin.GooglePlayServices.Base** i resultatlistan. Markera **Installera**. 

      ![Google Play Services NuGet](./media/partner-xamarin-notification-hubs-android-get-started/google-play-services-nuget.png)
5. Sök efter **Xamarin.Firebase.Messaging** i fönstret **NuGet Package Manager**. Välj **Xamarin.Firebase.Messaging** i resultatlistan. Markera **Installera**. 
6. Sök efter **Xamarin.Azure.NotificationHubs.Android**. Välj **Xamarin.Azure.NotificationHubs.Android** i resultatlistan. Markera **Installera**. 

### <a name="add-the-google-services-json-file"></a>Lägg till Google Services JSON-filen

1. Kopiera **google services.json** som du hämtade från Google Firebase-konsolen till projektmappen.
2. Lägg till **google-services.json** till projektet.
3. Välj **google-services.json** i fönstret **Solution Explorer**.
4. Ställ in Skapa-åtgärden på **GoogleServicesJson** i rutan **Egenskaper**. Om **GoogleServicesJson** inte visas, så stäng och starta om Visual Studio, öppna projektet på nytt och försök igen. 

      ![Skapa-åtgärd för GoogleServicesJson](./media/partner-xamarin-notification-hubs-android-get-started/google-services-json-build-action.png)

### <a name="set-up-notification-hubs-in-your-project"></a>Konfigurera meddelandehubbar i projektet

#### <a name="registering-with-firebase-cloud-messaging"></a>Registering med Firebase Cloud Messaging

Öppna filen **AndroidManifest.xml** och infoga följande `<receiver>`-element i elementet `<application>`:

        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
          <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="${applicationId}" />
          </intent-filter>
        </receiver>

1. Samla in följande information för Android-appen och meddelandehubben:
   
   * **Lyssna anslutningssträng**: På instrumentpanelen på [Azure Portal] väljer du **Visa anslutningssträngar**. Kopiera anslutningssträngen *DefaultListenSharedAccessSignature* för det här värdet.
   * **Hubbnamn**: Namnet på hubben från [Azure Portal]. Till exempel *mynotificationhub2*.
     
2. Högerklicka på ditt **projekt** i fönstret **Solution Explorer**, peka på **Lägg till** och välj **Klass**. 
4. Skapa en **Constants.cs**-klass för Xamarin-projektet och definiera följande konstanta värden i klassen. Ersätt platshållarna med värdena.
    
    ```csharp
        public static class Constants
        {
           public const string ListenConnectionString = "<Listen connection string>";
           public const string NotificationHubName = "<hub name>";
        }
    ```
3. Lägg till följande using-instruktioner till **MainActivity.cs** :
   
    ```csharp
        using Android.Util;
    ```
4. Lägg till en variabelinstans till den **MainActivity.cs** som ska användas för att visa en varningsruta när appen körs:
   
    ```csharp
        public const string TAG = "MainActivity";
    ```
5. Lägg till följande kod till `OnCreate` i **MainActivity.cs** efter `base.OnCreate(savedInstanceState)`:

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
    ```
7. Skapa en ny klass, **MyFirebaseIIDService**, så som du skapade klassen **Konstanter**. 
8. Lägg till följande using-satser till **MyFirebaseIIDService.cs**:
   
    ```csharp
    using Android.Util;
    using WindowsAzure.Messaging;
    using Firebase.Iid;
    ```

9. Lägg till följande **klass**deklaration i **MyFirebaseIIDService.cs** och gör så att klassen ärver från **FirebaseInstanceIdService**:
   
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
        public class MyFirebaseIIDService : FirebaseInstanceIdService
    ```
10. Lägg till följande kod i **MyFirebaseIIDService.cs**:
   
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
11. Skapa ytterligare en ny klass för ditt projekt och ge det namnet **MyFirebaseMessagingService**.
12. Lägg till följande using-satser till **MyFirebaseMessagingService.cs**.
    
    ```csharp
        using Android.Util;
        using Firebase.Messaging;
    ```
13. Lägg till följande ovanför klassdeklarationen och gör så att klassen ärver från **FirebaseMessagingService**:
    
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class MyFirebaseMessagingService : FirebaseMessagingService
    ```    
14. Lägg till följande kod till **MyFirebaseMessagingService.cs**:
    
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

            var notificationBuilder = new Notification.Builder(this)
                        .SetContentTitle("FCM Message")
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(messageBody)
                        .SetAutoCancel(true)
                        .SetContentIntent(pendingIntent);

            var notificationManager = NotificationManager.FromContext(this);

            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```
15. **Skapa** ditt projekt. 
16. **Kör** appen på din enhet eller i en inläst emulator

## <a name="send-test-notification-from-the-azure-portal"></a>Skicka ett testmeddelande från Azure Portal
Du kan testa att ta emot meddelanden i appen med alternativet *Skicka test* i [Azure Portal]. Den skickar ett test-push-meddelande till enheten.

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
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Visual Studio med Xamarin]: https://docs.microsoft.com/visualstudio/install/install-visual-studio
[Visual Studio för Mac]: https://www.visualstudio.com/vs/visual-studio-mac/

[Azure Portal]: https://portal.azure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Use Notification Hubs to push notifications to users]: /manage/services/notification-hubs/notify-users-aspnet
[Use Notification Hubs to send breaking news]: /manage/services/notification-hubs/breaking-news-dotnet
[GitHub]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid
