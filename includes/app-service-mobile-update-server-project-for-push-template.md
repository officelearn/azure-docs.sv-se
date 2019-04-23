---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: c664b089f316255fabc4c8dc36b291d7d63e6280
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59805199"
---
I det här avsnittet ska uppdatera du koden i projektet backend-befintliga Mobile Apps för att skicka ett push-meddelande varje gång ett nytt objekt läggs. Den här processen baseras på den [mall](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) funktion i Azure Notification Hubs, vilket gör det möjligt för plattformsoberoende push-meddelanden. Olika klienter registreras för push-meddelanden med hjälp av mallar och en enda universal push får alla klientplattformar.

Välj något av följande procedurer som matchar din backend-projekttyp&mdash;antingen [.NET-serverdel](#dotnet) eller [Node.js-serverdel](#nodejs).

### <a name="dotnet"></a>Projekt för .NET-serverdel

1. Högerklicka på serverprojektet i Visual Studio. Välj sedan **hantera NuGet-paket**. Sök efter `Microsoft.Azure.NotificationHubs`, och välj sedan **installera**. Den här processen installerar Notification Hubs-biblioteket för att skicka meddelanden från backend-servern.
2. Öppna i project server **domänkontrollanter** > **TodoItemController.cs**. Lägg till följande using-satser:

    ```csharp
    using System.Collections.Generic;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.Mobile.Server.Config;
    ```

3. I den **PostTodoItem** metoden Lägg till följande kod efter anropet till **InsertAsync**:  

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

    Den här processen skickar ett meddelande i mallen som innehåller objektet. Text när ett nytt objekt har infogats.

4. Publicera om serverprojektet.

### <a name="nodejs"></a>Node.js-backend-projekt

1. Om du inte redan gjort det, [hämta backend-snabbstartsprojektet](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart), eller annan användning den [onlineredigeraren i Azure-portalen](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
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

    Den här processen skickar ett meddelande i mallen som innehåller item.text när ett nytt objekt har infogats.

3. När du redigerar filen på den lokala datorn kan du publicera om serverprojektet.
