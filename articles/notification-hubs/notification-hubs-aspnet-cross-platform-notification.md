---
title: Skicka plattformsoberoende meddelanden till användare med Azure Notification Hubs (ASP.NET)
description: Lär dig hur du använder Meddelandehubbar mallar för att skicka i en enskild begäran en plattformsoberoende meddelande som är inriktad på alla plattformar.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 11d2131b-f683-47fd-a691-4cdfc696f62b
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 0f92b49c9d77029a9624782b49eb23f7083c49aa
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57863108"
---
# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Skicka plattformsoberoende meddelanden till användare med Meddelandehubbar

I en tidigare självstudiekurs [meddela användare med Meddelandehubbar], du har lärt dig hur du push-meddelanden till alla enheter som är registrerade till en specifik autentiserade användare. I självstudien, var flera begäranden tvungna att skicka ett meddelande till varje klientplattform som stöds. Azure Notification Hubs stöder mallar, som du kan ange hur en specifik enhet vill ta emot meddelanden. Den här metoden förenklar skicka plattformsoberoende meddelanden.

Den här artikeln visar hur du drar nytta av mallar för att skicka i en enskild begäran en plattformsoberoende meddelande som är inriktad på alla plattformar. Mer detaljerad information om mallar finns i [översikt över Azure Notification Hubs][Templates].

> [!IMPORTANT]
> Projekt för Windows Phone 8.1 och tidigare stöds inte i Visual Studio 2017. Mer information finns i [Visual Studio 2017 Platform Targeting and Compatibility](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs) (Visual Studio 2017 – målplattform och plattformskompatibilitet).

> [!NOTE]
> Med Notification Hubs, kan en enhet registrera flera mallar med samma tagg. I det här fallet ett inkommande meddelande som riktar sig mot taggen resultaten i flera meddelanden levereras till enheten, en för varje mall. Den här processen kan du visa samma meddelande i flera visual meddelanden, till exempel både som en Aktivitetsikon och popup-meddelande i en Windows Store-app.

## <a name="send-cross-platform-notifications-using-templates"></a>Skicka plattformsoberoende meddelanden med hjälp av mallar

Om du vill skicka plattformsoberoende meddelanden med hjälp av mallar, gör du följande:

1. I Solution Explorer i Visual Studio, expanderar den **domänkontrollanter** och sedan öppna filen RegisterController.cs.

2. Leta upp kodblocket i den `Put` metod som skapar en ny registrering och Ersätt sedan den `switch` innehåll med följande kod:

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

    Den här koden anropar metoden plattformsspecifika för att skapa en mall för registrering i stället för en intern registrering. Eftersom mallregistreringar härleds från interna registreringar, behöver du inte ändra befintliga registreringar.

3. I den `Notifications` styrenhet, ersätter den `sendNotification` metoden med följande kod:

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

    Den här koden skickar ett meddelande till alla plattformar på samma gång, utan att du har problem att ange en intern nyttolast. Notification Hubs bygger och levererar rätt nyttolasten till alla enheter med de angivna *taggen* värdet som anges i de registrerade mallarna.

4. Publicera ditt projekt för WebApi-serverdelen.

5. Kör klientappen igen och sedan verifiera att registreringen har slutförts.

6. (Valfritt) Distribuera klientappen till en andra enhet och kör sedan appen.
    Ett meddelande visas på varje enhet.

## <a name="next-steps"></a>Nästa steg

Nu när du har slutfört den här självstudiekursen lär du dig mer om Meddelandehubbar och mallar i följande avsnitt:

* [Use Notification Hubs to send breaking news]: Demonstrates another scenario for using templates.
* [Översikt över Azure Notification Hubs][Templates]: Innehåller mer detaljerad information om mallar.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: https://go.microsoft.com/fwlink/?LinkId=257546

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: https://go.microsoft.com/fwlink/p/?LinkId=314257
[Meddela användare med Meddelandehubbar]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: https://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: https://msdn.microsoft.com/library/windowsazure/jj927172.aspx
