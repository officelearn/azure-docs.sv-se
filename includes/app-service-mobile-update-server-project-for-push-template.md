---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 675ad278cb8bdc0ced4eff3bd77572f44c9808fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68857509"
---
I det här avsnittet uppdaterar du koden i ditt befintliga backend-projekt för mobilappar för att skicka ett push-meddelande varje gång ett nytt objekt läggs till. Den här processen drivs av [mallfunktionen](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) i Azure Notification Hubs, som möjliggör push-enheter över flera plattformar. De olika klienterna är registrerade för push-meddelanden med hjälp av mallar, och en enda universell push kan komma till alla klientplattformar.

Välj en av följande procedurer som matchar&mdash;backend-projekttypen antingen [.NET-backend](#dotnet) eller [Node.js-backend](#nodejs).

### <a name="net-back-end-project"></a><a name="dotnet"></a>.NET-backend-projekt

1. Högerklicka på serverprojektet i Visual Studio. Välj sedan **Hantera NuGet-paket**. Sök `Microsoft.Azure.NotificationHubs`efter och välj sedan **Installera**. Den här processen installerar biblioteket Meddelandehubbar för att skicka meddelanden från serverdelen.
2. Öppna **Controllers** > **TodoItemController.cs**i serverprojektet . Lägg sedan till följande med hjälp av satser:

    ```csharp
    using System.Collections.Generic;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.Mobile.Server.Config;
    ```

3. I **posttodoitem-metoden** lägger du till följande kod efter anropet till **InsertAsync:**  

    ```csharp
    // Get the settings for the server project.
    HttpConfiguration config = this.Configuration;
    MobileAppSettingsDictionary settings =
        this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

    // Get the Notification Hubs credentials for the mobile app.
    string notificationHubName = settings.NotificationHubName;
    string notificationHubConnection = settings
        .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

    // Create a new Notification Hub client.
    NotificationHubClient hub = NotificationHubClient
    .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

    // Send the message so that all template registrations that contain "messageParam"
    // receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
    Dictionary<string,string> templateParams = new Dictionary<string,string>();
    templateParams["messageParam"] = item.Text + " was added to the list.";

    try
    {
        // Send the push notification and log the results.
        var result = await hub.SendTemplateNotificationAsync(templateParams);

        // Write the success result to the logs.
        config.Services.GetTraceWriter().Info(result.State.ToString());
    }
    catch (System.Exception ex)
    {
        // Write the failure result to the logs.
        config.Services.GetTraceWriter()
            .Error(ex.Message, null, "Push.SendAsync Error");
    }
    ```

    Den här processen skickar ett mallmeddelande som innehåller objektet. Text när ett nytt objekt infogas.

4. Publicera om serverprojektet.

### <a name="nodejs-back-end-project"></a><a name="nodejs"></a>Nod.js backend-projekt

1. Ställ in ditt backend-projekt.
2. Ersätt den befintliga koden i todoitem.js med följande kod:

    ```javascript
    var azureMobileApps = require('azure-mobile-apps'),
    promises = require('azure-mobile-apps/src/utilities/promises'),
    logger = require('azure-mobile-apps/src/logger');

    var table = azureMobileApps.table();

    table.insert(function (context) {
    // For more information about the Notification Hubs JavaScript SDK,
    // see https://aka.ms/nodejshubs.
    logger.info('Running TodoItem.insert');

    // Define the template payload.
    var payload = '{"messageParam": "' + context.item.text + '" }';  

    // Execute the insert. The insert returns the results as a promise.
    // Do the push as a post-execute action within the promise flow.
    return context.execute()
        .then(function (results) {
            // Only do the push if configured.
            if (context.push) {
                // Send a template notification.
                context.push.send(null, payload, function (error) {
                    if (error) {
                        logger.error('Error while sending push notification: ', error);
                    } else {
                        logger.info('Push notification sent successfully!');
                    }
                });
            }
            // Don't forget to return the results from the context.execute().
            return results;
        })
        .catch(function (error) {
            logger.error('Error while running context.execute: ', error);
        });
    });

    module.exports = table;  
    ```

    Den här processen skickar ett mallmeddelande som innehåller objektet.text när ett nytt objekt infogas.

3. När du redigerar filen på den lokala datorn publicerar du om serverprojektet.
