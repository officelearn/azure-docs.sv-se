---
title: ta med fil
description: ta med fil
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/30/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 19352df7abff23ed44521a11e7907c84c8c0327f
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "33835856"
---
I det här avsnittet skickar du de senaste nyheterna som taggade mallmeddelanden från en .NET-konsolapp. 

1. Skapa en ny Visual C#-konsolapp i Visual Studio:
   
      ![Konsolapplänken][13]

2. På huvudmenyn i Visual Studio, markera **Verktyg** > **Library Package Manager** > **Package Manager Console** och sedan, i konsolfönstret, anger du följande sträng:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
3. Välj **Retur**.  
    Den här åtgärden lägger till en referens i Azure Notification Hubs SDK med hjälp av [Microsoft.Azure.Notification Hubs NuGet-paketet].

4. Öppna filen Program.cs och lägg till följande `using`-uttryck:
   
    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

5. I `Program`-klassen, lägger du till följande metod eller ersätter den om den redan finns:
   
    ```csharp
    private static async void SendTemplateNotificationAsync()
    {
        // Define the notification hub.
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");

        // Create an array of breaking news categories.
        var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};

        // Send the notification as a template notification. All template registrations that contain
        // "messageParam" and the proper tags will receive the notifications.
        // This includes APNS, GCM, WNS, and MPNS template registrations.

        Dictionary<string, string> templateParams = new Dictionary<string, string>();

        foreach (var category in categories)
        {
            templateParams["messageParam"] = "Breaking " + category + " News!";
            await hub.SendTemplateNotificationAsync(templateParams, category);
        }
    }
    ```   
   
    Den här koden skickar ett mallmeddelande för var och en av de sex taggarna i strängmatrisen. Användning av taggar garanterar att enheter endast får meddelanden för registrerade kategorier.

5. I föregående kod, ersätter du `<hub name>`- och `<connection string with full access>`-platshållarna med namnet på din meddelandehubb och anslutningssträngen för *DefaultFullSharedAccessSignature* från instrumentpanelen i din meddelandehubb.

6. Lägg till följande rader i **Main**-metoden:
   
    ```csharp
    SendTemplateNotificationAsync();
    Console.ReadLine();
    ```

7. Skapa konsolappen.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Get started with Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs REST interface]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Add push notifications for Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Microsoft.Azure.Notification Hubs NuGet-paketet]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
