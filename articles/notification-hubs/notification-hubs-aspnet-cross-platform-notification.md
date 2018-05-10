---
title: Skicka plattformsoberoende meddelanden till användare med Azure Notification Hubs (ASP.NET)
description: Lär dig hur du använder Notification Hubs mallar för att skicka i en enskild begäran, ett plattformsoberoende meddelande som riktar sig till alla plattformar.
services: notification-hubs
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 11d2131b-f683-47fd-a691-4cdfc696f62b
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 95793aac3c25563e3af39f3c47cebdd06e25e35f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Skicka plattformsoberoende meddelanden till användare med Notification Hubs
I en tidigare självstudiekursen [meddela användare med Notification Hubs], du har lärt dig hur du push-meddelanden till alla enheter som är registrerade på en specifik autentiserad användare. Flera begäranden var tvungen att skicka ett meddelande till varje klientplattform som stöds i den här självstudien. Azure Notification Hub har stöd för mallar som du kan ange hur en specifik enhet vill ta emot meddelanden. Den här metoden förenklar skicka plattformsoberoende meddelanden. 

Den här artikeln visar hur du kan dra nytta av mallar för att skicka i en enskild begäran, ett plattformsoberoende meddelande som riktar sig till alla plattformar. Mer detaljerad information om mallar finns [översikt över Azure Notification Hubs][Templates].

> [!IMPORTANT]
> Projekt för Windows Phone 8.1 och tidigare stöds inte i Visual Studio-2017. Mer information finns i [Visual Studio 2017 Platform Targeting and Compatibility](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs) (Visual Studio 2017 – målplattform och plattformskompatibilitet).

> [!NOTE]
> En enhet med Notification Hubs, kan du registrera flera mallar med samma tagg. I det här fallet ett inkommande meddelande som riktar sig till taggen resulterar i flera meddelanden levereras till enheten, en för varje mall. Den här processen kan du visa samma meddelande i flera visuella meddelanden, till exempel både som en Aktivitetsikon och som ett popup-meddelande i en Windows Store-app.
> 
> 

Om du vill skicka plattformsoberoende meddelanden med hjälp av mallar, gör du följande:

1. I Solution Explorer i Visual Studio, expandera den **domänkontrollanter** och sedan öppna filen RegisterController.cs.

2. Leta upp kodblocket i den **placera** metod som skapar en ny registrering och Ersätt den `switch` innehåll med följande kod:
   
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
            case "gcm":
                var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
                registration = new GcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }
   
    Den här koden anropar metoden plattformsspecifika om du vill skapa en mall för registrering i stället för en intern registrering. Eftersom mallen registreringar härledd från interna registreringar, behöver du inte ändra befintliga registreringar.

3. I den **meddelanden** styrenhet, ersätter den **sendNotification** metod med följande kod:
   
        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:" + user;
   
            var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
            await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);
   
            return Request.CreateResponse(HttpStatusCode.OK);
        }
   
    Den här koden skickar ett meddelande till alla plattformar på samma gång, utan du behöver ange en inbyggd nyttolast. Notification Hubs bygger och ger korrekt nyttolasten till alla enheter med de angivna *taggen* värdet som anges i de registrerade mallarna.

4. Publicera projektet WebApi-serverdel.

5. Kör klientappen igen och kontrollera att registreringen har slutförts.

6. (Valfritt) Distribuera klientappen till en annan enhet och kör sedan appen.
    På varje enhet visas ett meddelande.

## <a name="next-steps"></a>Nästa steg
Nu när du har slutfört den här kursen lär du dig mer om Notification Hubs och mallar i följande avsnitt:

* [Use Notification Hubs to send breaking news]: Demonstrates another scenario for using templates.
* [Översikt över Azure Notification Hubs][Templates]: innehåller detaljerad information om mallar.

<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Push to users ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Push to users Mobile Services]: /manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[meddela användare med Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: http://msdn.microsoft.com/library/windowsazure/jj927172.aspx
