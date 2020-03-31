---
title: Lägga till push-meddelanden i din Xamarin.iOS-app
description: Läs om hur du använder Azure App Service för att skicka push-meddelanden till din Xamarin.iOS-app.
ms.assetid: 2921214a-49f8-45e1-a306-a85ce21defca
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: f9c70491d06f61931ebabda859ff3a86ed035b44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249287"
---
# <a name="add-push-notifications-to-your-xamarinios-app"></a>Lägga till push-meddelanden i din Xamarin.iOS-app

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Översikt

I den här självstudien lägger du till push-meddelanden i [snabbstartsprojektet Xamarin.iOS](app-service-mobile-xamarin-ios-get-started.md) så att ett push-meddelande skickas till enheten varje gång en post infogas.

Om du inte använder det hämtade snabbstartsserverprojektet behöver du tilläggspaketet för push-meddelande. Mer information [finns i Arbeta med .NET-server för server för server för server för server för server för server för server för azure mobile apps.](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

## <a name="prerequisites"></a>Krav

* Slutför [snabbstartshandledningen Xamarin.iOS.](app-service-mobile-xamarin-ios-get-started.md)
* En fysisk iOS-enhet. Push-meddelanden stöds inte av iOS-simulatorn.

## <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registrera appen för push-meddelanden på Apples utvecklarportal

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-your-mobile-app-to-send-push-notifications"></a>Konfigurera mobilappen så att push-meddelanden skickas

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Uppdatera serverprojektet för att skicka push-meddelanden

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-your-xamarinios-project"></a>Konfigurera ditt Xamarin.iOS-projekt

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

## <a name="add-push-notifications-to-your-app"></a>Lägg till push-meddelanden i appen

1. I **QSTodoService**lägger du till följande egenskap så att **AppDelegate** kan förvärva den mobila klienten:

    ```csharp
    public MobileServiceClient GetClient {
        get
        {
            return client;
        }
        private set
        {
            client = value;
        }
    }
    ```

2. Lägg till `using` följande sats högst upp i **AppDelegate.cs-filen.**

    ```csharp
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

3. I **AppDelegate**åsidosätter du händelsen **Avslutad start:**

   ```csharp
    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // registers for push for iOS8
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

        return true;
    }
    ```

4. Åsidosätt händelsen i `RegisteredForRemoteNotifications` samma fil. I den här koden registrerar du dig för ett enkelt mallmeddelande som skickas över alla plattformar som stöds av servern.

    Mer information om mallar med meddelandehubbar finns i [Mallar](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

    ```csharp
    public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
    {
        MobileServiceClient client = QSTodoService.DefaultService.GetClient;

        const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
        {
            {"body", templateBodyAPNS}
        };

        // Register for push with your mobile app
        var push = client.GetPush();
        push.RegisterAsync(deviceToken, templates);
    }
    ```

5. Åsidosätt sedan händelsen **DidReceivedRemoteNotification:**

   ```csharp
    public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
    {
        NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

        string alert = string.Empty;
        if (aps.ContainsKey(new NSString("alert")))
            alert = (aps [new NSString("alert")] as NSString).ToString();

        //show alert
        if (!string.IsNullOrEmpty(alert))
        {
            UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
            avAlert.Show();
        }
    }
    ```

Din app uppdateras nu för att stödja push-meddelanden.

## <a name="test-push-notifications-in-your-app"></a><a name="test"></a>Testa push-meddelanden i appen

1. Tryck på **knappen Kör** för att skapa projektet och starta appen på en iOS-kompatibel enhet och klicka sedan på **OK** för att acceptera push-meddelanden.

   > [!NOTE]
   > Du måste uttryckligen acceptera push-meddelanden från din app. Den här begäran inträffar bara första gången appen körs.

2. Skriv en uppgift i appen och klicka**+** sedan på plusikonen ( ).
3. Kontrollera att ett meddelande tas emot och klicka sedan på **OK** för att avvisa meddelandet.
4. Upprepa steg 2 och stäng omedelbart appen och kontrollera sedan att ett meddelande visas.

Du har slutfört den här självstudien.
