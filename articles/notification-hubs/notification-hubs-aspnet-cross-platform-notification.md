---
title: Skicka meddelanden över flera plattformar till användare med Azure Notification Hubs (ASP.NET)
description: Lär dig hur du använder Mallar för meddelandehubbar för att i en enda begäran skicka ett plattformsoberoende meddelande som riktar sig till alla plattformar.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 11d2131b-f683-47fd-a691-4cdfc696f62b
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 09/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: fc3cec348517244c8a7f54d2d3d17298c58e4262
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127050"
---
# <a name="send-cross-platform-notifications-with-azure-notification-hubs"></a>Skicka meddelanden över flera plattformar med Azure Notification Hubs

Den här självstudien bygger på föregående självstudiekurs, [Skicka meddelanden till specifika användare med hjälp av Azure Notification Hubs]. I självstudien beskrivs hur du skickar meddelanden till alla enheter som är registrerade för en viss autentiserade användare. Den metoden krävde flera begäranden för att skicka ett meddelande till varje klientplattform som stöds. Azure Notification Hubs stöder mallar som du kan ange hur en viss enhet vill ta emot meddelanden. Den här metoden gör det enklare att skicka meddelanden över flera plattformar.

Den här artikeln visar hur du kan dra nytta av mallar för att skicka ett meddelande som riktar sig till alla plattformar. Den här artikeln använder en enda begäran för att skicka ett plattformsneutralt meddelande. Mer detaljerad information om mallar finns i [Översikt över meddelandehubbar][Templates].

> [!IMPORTANT]
> Windows Phone-projekt 8.1 och tidigare stöds inte i Visual Studio 2019. Mer information finns i [Visual Studio 2019 Plattformsinriktning och kompatibilitet](/visualstudio/releases/2019/compatibility).

> [!NOTE]
> Med Notification Hubs kan en enhet registrera flera mallar med samma tagg. I det här fallet resulterar ett inkommande meddelande som riktar sig till taggen i att flera meddelanden levereras till enheten, ett för varje mall. Med den här processen kan du visa samma meddelande i flera visuella meddelanden, till exempel både ett märke och som ett popup-meddelande i en Windows Store-app.

## <a name="send-cross-platform-notifications-using-templates"></a>Skicka meddelanden över flera plattformar med hjälp av mallar

I det här avsnittet används exempelkoden som du har skapat i [send-meddelanden till specifika användare med hjälp av självstudiekursen Azure Notification Hubs.] Du kan hämta exemplet från [GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers).

Så här skickar du meddelanden över flera plattformar med hjälp av mallar:

1. Expandera mappen **Controllers** i Visual Studio i **Solution Explorer**och öppna sedan *RegisterController.cs* filen.

1. Leta reda på kodblocket i metoden `Put` som skapar en `switch` ny registrering och ersätt innehållet med följande kod:

    ```csharp
    switch (deviceUpdate.Platform)
    {
        case "mpns":
            var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                    "<wp:Toast>" +
                        "<wp:Text1>$(message)</wp:Text1>" +
                    "</wp:Toast> " +
                "</wp:Notification>";
            registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "wns":
            toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
            registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "apns":
            var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
            registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
            break;
        case "fcm":
            var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
            registration = new FcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }
    ```

    Den här koden anropar den plattformsspecifika metoden för att skapa en mallregistrering i stället för en inbyggd registrering. Eftersom mallregistreringar härleds från inbyggda registreringar behöver du inte ändra befintliga registreringar.

1. Öppna *den NotificationsController.cs* filen i mappen **Solution** **Explorer**. Ersätt metoden `Post` med följande kod:

    ```csharp
    public async Task<HttpResponseMessage> Post()
    {
        var user = HttpContext.Current.User.Identity.Name;
        var userTag = "username:" + user;

        var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
        await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

    Den här koden skickar ett meddelande till alla plattformar samtidigt. Du anger inte en inbyggd nyttolast. Meddelandehubbar bygger och levererar rätt nyttolast till varje enhet med det angivna taggvärdet, enligt vad som anges i de registrerade mallarna.

1. Publicera om webb-API-projektet.

1. Kör klientappen igen för att kontrollera att registreringen har lyckats.

1. Du kan också distribuera klientappen till en andra enhet och sedan köra appen. Ett meddelande visas på varje enhet.

## <a name="next-steps"></a>Nästa steg

Nu när du har slutfört den här självstudien kan du läsa mer om meddelandehubbar och mallar i följande artiklar:

* Ett annat scenario för hur du använder mallar finns i [Push-meddelandena till specifika Windows-enheter som kör självstudiekursen för Universella Windows-program.][Use Notification Hubs to send breaking news]
* Mer detaljerad information om mallar finns i [Översikt över meddelandehubbar][Templates].

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: https://visualstudio.microsoft.com/downloads/

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: https://go.microsoft.com/fwlink/p/?LinkId=314257
[Skicka push-meddelanden till specifika användare med hjälp av Azure Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: https://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: https://msdn.microsoft.com/library/windowsazure/jj927172.aspx
