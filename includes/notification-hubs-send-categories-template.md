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
ms.openlocfilehash: f0ff729084d194ff2e05e89eadc45782f775b1c5
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64929459"
---
I det här avsnittet skickar du de senaste nyheterna som taggade mallmeddelanden från en .NET-konsolapp. 

1. Skapa en ny visualisering i Visual Studio C# konsolapp: en. På menyn, Välj **filen** > **New** > **projekt**.
    b. Expandera **Visual C#** , och välj **Windows Desktop**. 
    c. Välj **Konsolapp (.NET Framework)** i listan över mallar. 
    d. Ange en **namn** för appen. 
    e. Välj en **mappen** för appen.
    f. Klicka på **OK** för att skapa projektet. 
2. På huvudmenyn i Visual Studio väljer **verktyg** > **NuGet-Pakethanteraren** > **Pakethanterarkonsolen** och sedan i konsolfönstret Ange följande sträng:
   
    ```
    Install-Package Microsoft.Azure.NotificationHubs
    ```
   
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
[Notification Hubs REST interface]: https://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Add push notifications for Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Microsoft.Azure.Notification Hubs NuGet-paketet]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
