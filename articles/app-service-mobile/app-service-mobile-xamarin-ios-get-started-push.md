---
title: Lägg till push-meddelanden i din Xamarin. iOS-app
description: Lär dig hur du använder Azure App Service för att skicka push-meddelanden till din Xamarin. iOS-app.
ms.assetid: 2921214a-49f8-45e1-a306-a85ce21defca
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: f9c70491d06f61931ebabda859ff3a86ed035b44
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461359"
---
# <a name="add-push-notifications-to-your-xamarinios-app"></a>Lägg till push-meddelanden i din Xamarin. iOS-app

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Översikt

I den här självstudien lägger du till push-meddelanden i [snabb starts projektet Xamarin. iOS](app-service-mobile-xamarin-ios-get-started.md) så att ett push-meddelande skickas till enheten varje gång en post infogas.

Om du inte använder det nedladdade snabb start Server-projektet behöver du ett tilläggs paket för push-meddelanden. Mer information finns i [arbeta med .NET-Server del Server SDK för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

## <a name="prerequisites"></a>Förutsättningar

* Slutför [snabb starts guiden för Xamarin. iOS](app-service-mobile-xamarin-ios-get-started.md) .
* En fysisk iOS-enhet. Push-meddelanden stöds inte av iOS-simulatorn.

## <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registrera appen för push-meddelanden på Apples utvecklings Portal

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-your-mobile-app-to-send-push-notifications"></a>Konfigurera mobilappen för att skicka push-meddelanden

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Uppdatera Server projektet för att skicka push-meddelanden

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-your-xamarinios-project"></a>Konfigurera ditt Xamarin. iOS-projekt

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

## <a name="add-push-notifications-to-your-app"></a>Lägga till push-meddelanden för appen

1. I **QSTodoService**lägger du till följande egenskap så att **AppDelegate** kan hämta mobil klienten:

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

2. Lägg till följande `using`-instruktion överst i **AppDelegate.cs** -filen.

    ```csharp
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

3. I **AppDelegate**åsidosätter du **FinishedLaunching** -händelsen:

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

4. I samma fil, Åsidosätt `RegisteredForRemoteNotifications`-händelsen. I den här koden registrerar du dig för ett enkelt mal meddelande som skickas mellan alla plattformar som stöds av servern.

    Mer information om mallar med Notification Hubs finns i [mallar](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

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

5. Åsidosätt sedan **DidReceivedRemoteNotification** -händelsen:

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

Appen har nu uppdaterats för att stödja push-meddelanden.

## <a name="test"></a>Testa push-meddelanden i din app

1. Tryck på **Kör** -knappen för att skapa projektet och starta appen i en iOS-kompatibel enhet. Klicka sedan på **OK** för att acceptera push-meddelanden.

   > [!NOTE]
   > Du måste uttryckligen acceptera push-meddelanden från din app. Den här begäran sker bara första gången appen körs.

2. Skriv en aktivitet i appen och klicka sedan på plus ikonen ( **+** ).
3. Verifiera att ett meddelande har tagits emot och klicka sedan på **OK** för att stänga meddelandet.
4. Upprepa steg 2 och Stäng appen omedelbart och kontrol lera att ett meddelande visas.

Du har slutfört den här självstudien.
