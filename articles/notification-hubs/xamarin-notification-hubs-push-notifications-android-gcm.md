---
title: "Komma igång med Notification Hubs för Xamarin.Android-appar | Microsoft Docs"
description: "I den här självstudiekursen beskrivs hur du använder Azure Notification Hubs för att skicka push-meddelanden till en Xamarin-Android-app."
author: jwhitedev
manager: kpiteira
editor: 
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 1cb6fbc82c493e17815dc60ddcff183a47513bc6
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2018
---
# <a name="get-started-with-notification-hubs-for-xamarinandroid-apps"></a>Komma igång med Notification Hubs för Xamarin.Android-appar
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Översikt

I den här självstudiekursen beskrivs hur du använder Azure Notification Hubs för att skicka push-meddelanden till en Xamarin.Android-app. Du skapar en tom Xamarin.Android-app som tar emot push-meddelanden via Firebase Cloud Messaging (FCM). När du är klar kan du använda meddelandehubben för att sända push-meddelanden till alla enheter som kör appen. Den färdiga koden finns tillgänglig i exemplet [NotificationHubs-app][GitHub].

I den här självstudiekursen visas ett enkelt scenario för sändning med Notification Hubs.

## <a name="before-you-begin"></a>Innan du börjar
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Den färdiga koden för den här självstudiekursen hittar du på GitHub [här][GitHub].

## <a name="prerequisites"></a>Nödvändiga komponenter
För den här kursen behöver du följande:

* [Visual Studio med Xamarin] på Windows eller [Visual Studio för Mac] på OS X.
* Aktivt Google-konto

Du måste slutföra den här självstudiekursen innan du påbörjar någon annan kurs om Notification Hubs för Xamarin.Android-appar.

> [!IMPORTANT]
> Du måste ha ett aktivt Azure-konto för att slutföra den här kursen. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F).
> 
> 

## <a name="enable-firebase-cloud-messaging"></a>Aktivera Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-your-notification-hub"></a>Konfigurera meddelandehubben
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li><p>Välj fliken <b>Konfigurera</b> högst upp, ange <b>API-nyckelvärdet</b> som du fick i det förra avsnittet och välj sedan <b>Spara</b>.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Meddelandehubben har konfigurerats för att fungera med FCM och du har anslutningssträngar för att registrera din app för att både ta emot meddelanden och skicka push-meddelanden.

## <a name="connect-your-app-to-the-notification-hub"></a>Anslut appen till meddelandehubben
Först skapar du ett nytt projekt. 

1. I Visual Studio väljer du **Ny lösning**  > **Android-app** och slutligen **Nästa**.
   
      ![Visual Studio – Skapa ett nytt Android-projekt][22]

2. Ange **appens namn** och **ID**. Markera de **målplattformar** du vill stödja och välj sedan **Nästa** och **Skapa**.
   
      ![Visual Studio – Android-appkonfiguration][23]

    På så sätt skapas ett nytt Android-projekt.

3. Öppna projektegenskaperna genom att högerklicka på det nya projektet i vyn Lösning och välj **Alternativ**. Välj objektet för **Android-appen** i avsnittet **Skapa**.
   
    Kontrollera att den första bokstaven i **paketnamnet** är en gemen.
   
   > [!IMPORTANT]
   > Den första bokstaven i paketnamnet måste vara en gemen. Annars visas appmanifestfel när du registrerar en app för push-meddelanden nedan.
   > 
   > 
   
      ![Visual Studio – Alternativ för Android-projekt][24]
4. Du kan också ställa in den **äldsta tillåtna Android-versionen** till en annan API-nivå.
5. Du kan också ställa in **målversionen av Android** till en annan API-version som du vill ha som mål (måste vara API-nivå 8 eller senare).
6. Välj **OK** och stäng dialogrutan Projektalternativ.

### <a name="add-the-required-packages-to-your-project"></a>Lägg till paketen som krävs i projektet

1. Högerklicka på projektet och välj **Lägg till** > **Lägg till NuGet-paket**
2. Sök efter **Xamarin.Azure.NotificationHubs.Android** och lägg till det i projektet.
3. Sök efter **Xamarin.Firebase.Messaging** och lägg till det i projektet.

### <a name="set-up-notification-hubs-in-your-project"></a>Konfigurera meddelandehubbar i projektet
1. Samla in följande information för Android-appen och meddelandehubben:
   
   * **Lyssna anslutningssträng**: På instrumentpanelen på [Azure Portal] väljer du **Visa anslutningssträngar**. Kopiera anslutningssträngen *DefaultListenSharedAccessSignature* för det här värdet.
   * **Hubbnamn**: Det här är namnet på hubben från [Azure Portal]. Till exempel *mynotificationhub2*.
     
2. Skapa en **Constants.cs**-klass för Xamarin-projektet och definiera följande konstanta värden i klassen. Ersätt platshållarna med värdena.
    
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

6. Högerklicka på projektet och lägg till `google-services.json` som du laddade ned från Firebase-projektet. Högerklicka på den tillagda filen och ange byggåtgärden som `GoogleServicesJson`

    ![Visual Studio – Konfigurera google-services.json][25]

7. Skapa en ny klass, **MyFirebaseIIDService**.

8. Lägg till följande using-satser till **MyFirebaseIIDService.cs**:
   
    ```csharp
        using System;
        using Android.App;
        using Firebase.Iid;
        using Android.Util;
        using WindowsAzure.Messaging;
        using System.Collections.Generic;
    ```

9. Lägg till följande ovanför deklarationen **klass** i **MyFirebaseIIDService.cs** och gör så att klassen ärver från **FirebaseInstanceIdService**:
   
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
        using System;
        using System.Linq;
        using Android;
        using Android.App;
        using Android.Content;
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

15. Kör appen på din enhet eller i en inläst emulator

## <a name="send-notifications-from-the-portal"></a>Skicka meddelanden från portalen
Du kan testa att ta emot meddelanden i appen med alternativet *Skicka test* i [Azure Portal]. Ett test-push-meddelande skickas till enheten.

![Azure Portal – Skicka test][30]

Push-meddelanden skickas vanligtvis via en serverdelstjänst som Mobile Services eller ASP.NET med hjälp av ett kompatibelt bibliotek. Du kan också använda REST-API:et direkt för att skicka meddelanden om ett bibliotek inte är tillgängligt för din serverdel.

Här är en lista med andra självstudiekurser du kan gå igenom som handlar om att skicka meddelanden:

* ASP.NET: Se [Använda Notification Hubs för att skicka push-meddelanden till användare].
* Azure Notification Hubs Java SDK: Se [Använda Notification Hubs från Java](notification-hubs-java-push-notification-tutorial.md) för att skicka meddelanden från Java. Det här har testats i Eclipse för Android-utveckling.
* PHP: Se [Använda Notification Hubs från PHP](notification-hubs-php-push-notification-tutorial.md).

## <a name="next-steps"></a>Nästa steg
I det här enkla exemplet skickade du meddelanden till alla dina Android-enheter. Mer information om hur du riktar in dig på specifika användare finns i självstudiekursen [Använda Notification Hubs för att skicka push-meddelanden till användare]. Om du vill dela in användarna efter intressegrupper läser du [Använda Notification Hubs för att skicka de senaste nyheterna]. Mer information om hur du använder Notification Hubs finns i [Riktlinjer för Notification Hubs] och [Notification Hubs-instruktioner för Android].

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
[Riktlinjer för Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs-instruktioner för Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Använda Notification Hubs för att skicka push-meddelanden till användare]: /manage/services/notification-hubs/notify-users-aspnet
[Använda Notification Hubs för att skicka de senaste nyheterna]: /manage/services/notification-hubs/breaking-news-dotnet
[GitHub]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid
