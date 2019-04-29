---
title: ta med fil
description: ta med fil
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/02/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c15d695e072e72c6e7be6dcf49f3ea049a9b70b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60560508"
---
När du skickar meddelanden om mallar, behöver du bara ange en uppsättning egenskaper. I det här scenariot innehåller uppsättningen egenskaper den lokaliserade versionen av den aktuella nyheten.

```json
{
    "News_English": "World News in English!",
    "News_French": "World News in French!",
    "News_Mandarin": "World News in Mandarin!"
}
```

### <a name="send-notifications-using-a-c-console-app"></a>Skicka meddelanden med hjälp av en C#-konsolapp

Det här avsnittet visar hur du skickar meddelanden med hjälp av en konsolapp. Koden skickar meddelanden till Windows Store- och iOS-enheter. Ändra metoden `SendTemplateNotificationAsync` i den konsolapp som du skapade tidigare med följande kod:

```csharp
private static async void SendTemplateNotificationAsync()
{
    // Define the notification hub.
    NotificationHubClient hub = 
        NotificationHubClient.CreateClientFromConnectionString(
            "<connection string with full access>", "<hub name>");

    // Sending the notification as a template notification. All template registrations that contain 
    // "messageParam" or "News_<local selected>" and the proper tags will receive the notifications. 
    // This includes APNS, GCM, WNS, and MPNS template registrations.
    Dictionary<string, string> templateParams = new Dictionary<string, string>();

    // Create an array of breaking news categories.
    var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
    var locales = new string[] { "English", "French", "Mandarin" };

    foreach (var category in categories)
    {
        templateParams["messageParam"] = "Breaking " + category + " News!";

        // Sending localized News for each tag too...
        foreach( var locale in locales)
        {
            string key = "News_" + locale;

            // Your real localized news content would go here.
            templateParams[key] = "Breaking " + category + " News in " + locale + "!";
        }

        await hub.SendTemplateNotificationAsync(templateParams, category);
    }
}
```

Metoden SendTemplateNotificationAsync levererar lokaliserade del av nyheter till **alla** dina enheter, oavsett plattformen. Din meddelandehubb bygger och levererar rätt interna nyttolasten för alla enheter som prenumererar på en specifik tagg.

### <a name="sending-notification-with-mobile-services"></a>Skicka meddelanden med Mobile Services

Använd följande skript i dina mobiltjänster-scheduler:

```csharp
var azure = require('azure');
var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
var notification = {
        "News_English": "World News in English!",
        "News_French": "World News in French!",
        "News_Mandarin", "World News in Mandarin!"
}
notificationHubService.send('World', notification, function(error) {
    if (!error) {
        console.warn("Notification successful");
    }
});
```
