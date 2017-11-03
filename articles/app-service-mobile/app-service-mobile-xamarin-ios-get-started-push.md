---
title: "Lägga till push-meddelanden för Xamarin.iOS-app med Azure App Service"
description: "Lär dig hur du använder Azure App Service för att skicka push-meddelanden till Xamarin.iOS-app"
services: app-service\mobile
documentationcenter: xamarin
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 2921214a-49f8-45e1-a306-a85ce21defca
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: glenga
ms.openlocfilehash: bf922e49c4c92d0065817a5dd6c7d10a04737304
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="add-push-notifications-to-your-xamarinios-app"></a>Lägga till push-meddelanden för Xamarin.iOS-App
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Översikt
I kursen får du lägga till push-meddelanden till den [Xamarin.iOS Snabbstart](app-service-mobile-xamarin-ios-get-started.md) projekt så att ett push-meddelande skickas till enheten varje gång en post infogas.

Om du inte använder hämtade Snabbstart serverprojekt behöver push notification extension-paketet. Se [arbeta med serverdelen .NET SDK för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) för mer information.

## <a name="prerequisites"></a>Krav
* Slutför den [Xamarin.iOS quickstart](app-service-mobile-xamarin-ios-get-started.md) kursen.
* En fysisk iOS-enhet. Push-meddelanden stöds inte av iOS-simulatorn.

## <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registrera appen för push-meddelanden på Apple developer-portalen
[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-your-mobile-app-to-send-push-notifications"></a>Konfigurera Mobilappen för att skicka push-meddelanden
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Uppdatera serverprojekt för att skicka push-meddelanden
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-your-xamarinios-project"></a>Konfigurera Xamarin.iOS-projekt
[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

## <a name="add-push-notifications-to-your-app"></a>Lägg till push-meddelanden i appen
1. I **QSTodoService**, Lägg till följande egenskap så att **AppDelegate** kan hämta den mobila klienten:
   
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
2. Lägg till följande `using` uttryck överst i den **AppDelegate.cs** fil.
   
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;
3. I **AppDelegate**, åsidosätta den **FinishedLaunching** händelse:
   
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
4. I samma fil åsidosätta den **RegisteredForRemoteNotifications** händelse. I den här koden registrerar du för en enkel mall-meddelandet som skickas över alla plattformar som stöds av servern.
   
    Mer information om mallar med Notification Hubs finns [mallar](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

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


1. Sedan kan åsidosätta den **DidReceivedRemoteNotification** händelse:
   
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

Appen har uppdaterats för att stödja push-meddelanden.

## <a name="test"></a>Testa push-meddelanden i appen
1. Tryck på den **kör** knappen för att bygga projektet och starta appen i en kompatibla iOS-enhet och klicka sedan på **OK** att ta emot push-meddelanden.
   
   > [!NOTE]
   > Du måste uttryckligen godkänna push-meddelanden från din app. Denna begäran inträffar bara första gången som appen körs.
   > 
   > 
2. I appen, skriver du en uppgift och klicka sedan på plustecknet (**+**) ikon.
3. Kontrollera att ett meddelande tas emot och klickar sedan **OK** att stänga meddelandet.
4. Upprepa steg 2 och omedelbart stänga appen och sedan kontrollera att ett meddelande visas.

Du har slutfört den här kursen.

<!-- Images. -->

<!-- URLs. -->



