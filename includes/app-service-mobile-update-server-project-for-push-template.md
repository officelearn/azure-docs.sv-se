---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 675ad278cb8bdc0ced4eff3bd77572f44c9808fc
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857509"
---
I det här avsnittet uppdaterar du kod i ditt befintliga Mobile Apps Server dels projekt för att skicka ett push-meddelande varje gång ett nytt objekt läggs till. Den här processen drivs av [mal](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) funktionen i Azure Notification Hubs, vilket möjliggör push-meddelanden mellan plattformar. De olika klienterna är registrerade för push-meddelanden med hjälp av mallar och en enda Universal push kan komma till alla klient plattformar.

Välj en av följande procedurer som matchar backend-projektets typ&mdash;, antingen [.NET Server](#dotnet) del eller [Node. js-Server delen](#nodejs).

### <a name="dotnet"></a>.NET-Server dels projekt

1. I Visual Studio högerklickar du på Server projektet. Välj sedan **Hantera NuGet-paket**. Sök efter `Microsoft.Azure.NotificationHubs`och välj sedan **Installera**. Den här processen installerar Notification Hubs bibliotek för att skicka meddelanden från Server delen.
2. Öppna **kontrollanter** > **TodoItemController.cs**i Server projektet. Lägg sedan till följande using-satser:

    ```csharp
    using System.Collections.Generic;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.Mobile.Server.Config;
    ```

3. I **PostTodoItem** -metoden lägger du till följande kod efter anropet till **InsertAsync**:  

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

    Den här processen skickar ett meddelande från en mall som innehåller objektet. Text när ett nytt objekt infogas.

4. Publicera om Server projektet.

### <a name="nodejs"></a>Node. js-backend-projekt

1. Konfigurera ditt Server dels projekt.
2. Ersätt den befintliga koden i todoitem. js med följande kod:

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

    Den här processen skickar ett meddelande i en mall som innehåller objektet. text när ett nytt objekt infogas.

3. Publicera om Server projektet när du redigerar filen på den lokala datorn.
