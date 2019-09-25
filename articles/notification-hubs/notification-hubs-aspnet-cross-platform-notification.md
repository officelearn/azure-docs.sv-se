---
title: Skicka meddelanden mellan plattformar till användare med Azure Notification Hubs (ASP.NET)
description: Lär dig hur du använder Notification Hubs mallar för att skicka, i en enskild begäran, ett oberoende meddelande som är avsett för alla plattformar.
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
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: cea0d63c20af781fcfc6ba5d7c06061b12992702
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212021"
---
# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Skicka meddelanden mellan plattformar till användare med Notification Hubs

I en tidigare självstudie kan du [meddela användare med Notification Hubs], och du har lärt dig hur du skickar meddelanden till alla enheter som är registrerade för en speciell autentiserad användare. I den här självstudien krävdes flera begär Anden för att skicka ett meddelande till varje klient plattform som stöds. Azure Notification Hubs stöder mallar, där du kan ange hur en speciell enhet vill ta emot meddelanden. Den här metoden fören klar sändningen av plattforms oberoende meddelanden.

Den här artikeln visar hur du kan dra nytta av mallar för att skicka, i en enda begäran, ett oberoende meddelande som är riktat mot alla plattformar. Mer detaljerad information om mallar finns i [Översikt över Azure Notification Hubs][Templates].

> [!IMPORTANT]
> Windows Phone Project 8,1 och tidigare stöds inte i Visual Studio 2017. Mer information finns i [Visual Studio 2017 Platform Targeting and Compatibility](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs) (Visual Studio 2017 – målplattform och plattformskompatibilitet).

> [!NOTE]
> Med Notification Hubs kan en enhet registrera flera mallar med samma tagg. I det här fallet resulterar ett inkommande meddelande som är mål för taggen i flera meddelanden som skickas till enheten, en för varje mall. Med den här processen kan du visa samma meddelande i flera visuella meddelanden, till exempel båda som en skylt och som ett popup-meddelande i en Windows Store-app.

## <a name="send-cross-platform-notifications-using-templates"></a>Skicka meddelanden mellan plattformar med mallar

Om du vill skicka meddelanden mellan plattformar med hjälp av mallar gör du följande:

1. I Solution Explorer i Visual Studio expanderar du mappen **kontrollanter** och öppnar sedan filen RegisterController.cs.

2. Leta upp kod blocket i `Put` -metoden som skapar en ny registrering och `switch` ersätt sedan innehållet med följande kod:

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

    Den här koden anropar den plattformsspecifika metoden för att skapa en mall registrering i stället för en intern registrering. Eftersom mallens registreringar härleds från interna registreringar behöver du inte ändra befintliga registreringar.

3. Ersätt metoden med följande kod i kontrollenheten:`Notifications` `sendNotification`

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

    Den här koden skickar ett meddelande till alla plattformar samtidigt, utan att du behöver ange en intern nytto Last. Notification Hubs skapar och levererar rätt nytto Last till varje enhet med det angivna *taggnamnet* , som anges i de registrerade mallarna.

4. Publicera om ditt WebApi-backend-projekt.

5. Kör klient programmet igen och kontrol lera att registreringen är klar.

6. Valfritt Distribuera klient programmet till en andra enhet och kör sedan appen.
    Ett meddelande visas på varje enhet.

## <a name="next-steps"></a>Nästa steg

Nu när du har slutfört den här självstudien får du lära dig mer om Notification Hubs och mallar i följande avsnitt:

* [Use Notification Hubs to send breaking news]: Demonstrates another scenario for using templates.
* [Översikt över Azure-Notification Hubs][Templates]: Innehåller mer detaljerad information om mallar.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: https://go.microsoft.com/fwlink/?LinkId=257546

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: https://go.microsoft.com/fwlink/p/?LinkId=314257
[Meddela användare med Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: https://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: https://msdn.microsoft.com/library/windowsazure/jj927172.aspx
