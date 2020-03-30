---
title: Lägga till push-meddelanden i din UWP-app
description: Lär dig hur du använder Azure App Service Mobile Apps och Azure Notification Hubs för att skicka push-meddelanden till din UWP-app (Universal Windows Platform).
ms.assetid: 6de1b9d4-bd28-43e4-8db4-94cd3b187aa3
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 6f200e9649a00bfe890d46f86e62404f1a7e844f
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366291"
---
# <a name="add-push-notifications-to-your-windows-app"></a>Lägga till push-meddelanden i Din Windows-app

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Översikt

I den här självstudien lägger du till push-meddelanden i [snabbstartsprojektet i Windows](app-service-mobile-windows-store-dotnet-get-started.md) så att ett push-meddelande skickas till enheten varje gång en post infogas.

Om du inte använder det hämtade snabbstartsserverprojektet behöver du tilläggspaketet för push-meddelande. Mer information [finns i Arbeta med .NET-server för server för server för server för server för server för server för server för azure mobile apps.](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

## <a name="configure-a-notification-hub"></a><a name="configure-hub"></a>Konfigurera en meddelandehubb

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-your-app-for-push-notifications"></a>Registrera din app för push-meddelanden

Du måste skicka in appen till Microsoft Store och sedan konfigurera serverprojektet så att det integreras med [WNS (Windows Push Notification Services)](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) för att skicka push.

1. Högerklicka på UWP-appprojektet i Visual Studio Solution Explorer, klicka på **Store** > **Associera-app med Store...**.

    ![Associera app med Microsoft Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)

2. Klicka på **Nästa**i guiden, logga in med ditt Microsoft-konto, skriv ett namn på appen i **Reservera ett nytt appnamn**och klicka sedan på **Reservera**.
3. När appregistreringen har skapats väljer du det nya appnamnet, klickar på **Nästa**och klickar sedan på **Associera**. Detta lägger till den nödvändiga registreringsinformationen för Microsoft Store i programmanifestet.
4. Navigera till [programregistreringsportalen](https://apps.dev.microsoft.com/) och logga in med ditt Microsoft-konto. Klicka på den Windows Store-app som du kopplade till i föregående steg.
5. På registreringssidan noterar du värdet under **Programhemligheter** och **Package SID**, som du nästa användning för att konfigurera din mobilappsservering.

    ![Associera app med Microsoft Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

   > [!IMPORTANT]
   > Klienthemligheten och paket-SID:et är viktiga säkerhetsuppgifter. Lämna aldrig ut dessa uppgifter till någon och distribuera dem inte tillsammans med din app. **Program-ID:et** används med hemligheten för att konfigurera Microsoft-kontoautentisering.

[App Center](https://docs.microsoft.com/appcenter/sdk/push/uwp#prerequisite---register-your-app-for-windows-notification-services-wns) har också instruktioner för att konfigurera UWP-appar för push-meddelanden.

## <a name="configure-the-backend-to-send-push-notifications"></a>Konfigurera serveringsavstämningen för att skicka push-meddelanden

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

## <a name="update-the-server-to-send-push-notifications"></a><a id="update-service"></a>Uppdatera servern för att skicka push-meddelanden

Använd proceduren nedan som matchar&mdash;backend-projekttypen antingen [.NET-backend](#dotnet) eller [Node.js backend](#nodejs).

### <a name="net-backend-project"></a><a name="dotnet"></a>.NET-backend-projekt

1. Högerklicka på serverprojektet i Visual Studio och klicka på **Hantera NuGet-paket**, söka efter Microsoft.Azure.NotificationHubs och klicka sedan på **Installera**. Detta installerar meddelandehubbarklientbiblioteket.
2. Expandera **styrenheter,** öppna TodoItemController.cs och lägg till följande med hjälp av satser:

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

    // Get the Notification Hubs credentials for the Mobile App.
    string notificationHubName = settings.NotificationHubName;
    string notificationHubConnection = settings
        .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

    // Create the notification hub client.
    NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

    // Define a WNS payload
    var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">"
                            + item.Text + @"</text></binding></visual></toast>";
    try
    {
        // Send the push notification.
        var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

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

    Den här koden talar om för meddelandehubben att skicka ett push-meddelande när ett nytt objekt har infogats.

4. Publicera om serverprojektet.

### <a name="nodejs-backend-project"></a><a name="nodejs"></a>Nod.js backend-projekt
1. Ställ in ditt backend-projekt.
2. Ersätt den befintliga koden i filen todoitem.js med följande:

    ```javascript
    var azureMobileApps = require('azure-mobile-apps'),
    promises = require('azure-mobile-apps/src/utilities/promises'),
    logger = require('azure-mobile-apps/src/logger');

    var table = azureMobileApps.table();

    table.insert(function (context) {
    // For more information about the Notification Hubs JavaScript SDK,
    // see https://aka.ms/nodejshubs
    logger.info('Running TodoItem.insert');

    // Define the WNS payload that contains the new item Text.
    var payload = "<toast><visual><binding template=\ToastText01\><text id=\"1\">"
                                + context.item.text + "</text></binding></visual></toast>";

    // Execute the insert.  The insert returns the results as a Promise,
    // Do the push as a post-execute action within the promise flow.
    return context.execute()
        .then(function (results) {
            // Only do the push if configured
            if (context.push) {
                // Send a WNS native toast notification.
                context.push.wns.sendToast(null, payload, function (error) {
                    if (error) {
                        logger.error('Error while sending push notification: ', error);
                    } else {
                        logger.info('Push notification sent successfully!');
                    }
                });
            }
            // Don't forget to return the results from the context.execute()
            return results;
        })
        .catch(function (error) {
            logger.error('Error while running context.execute: ', error);
        });
    });

    module.exports = table;
    ```

    Detta skickar ett popup-meddelande från WNS som innehåller objektet.text när ett nytt todo-objekt infogas.

3. När du redigerar filen på den lokala datorn publicerar du om serverprojektet.

## <a name="add-push-notifications-to-your-app"></a><a id="update-app"></a>Lägg till push-meddelanden i appen
Därefter måste appen registrera sig för push-meddelanden vid start. När du redan har aktiverat autentisering kontrollerar du att användaren loggar in innan du försöker registrera dig för push-meddelanden.

1. Öppna **App.xaml.cs** projektfilen och lägg `using` till följande satser:

    ```csharp
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    ```

2. Lägg till följande **InitNotificationsAsync-metoddefinition i** klassen **App** i samma fil:

    ```csharp
    private async Task InitNotificationsAsync()
    {
        // Get a channel URI from WNS.
        var channel = await PushNotificationChannelManager
            .CreatePushNotificationChannelForApplicationAsync();

        // Register the channel URI with Notification Hubs.
        await App.MobileService.GetPush().RegisterAsync(channel.Uri);
    }
    ```

    Den här koden hämtar ChannelURI för appen från WNS och registrerar sedan den ChannelURI med din App Service Mobile App.

3. Lägg till **asynkronmodifieraren** i metoddefinitionen högst upp i händelsehanteraren **OnLaunched** i **App.xaml.cs**och lägg till följande anrop i den nya **metoden InitNotificationsAsync,** som i följande exempel:

    ```csharp
    protected async override void OnLaunched(LaunchActivatedEventArgs e)
    {
        await InitNotificationsAsync();

        // ...
    }
    ```

    Detta garanterar att den kortlivade ChannelURI registreras varje gång programmet startas.

4. Bygg om ditt UWP-appprojekt. Appen är nu redo att ta emot popup-meddelanden.

## <a name="test-push-notifications-in-your-app"></a><a id="test"></a>Testa push-meddelanden i appen

[!INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

## <a name="next-steps"></a><a id="more"></a>Nästa steg

Läs mer om push-meddelanden:

* [Så här använder du den hanterade klienten för Azure Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md#pushnotifications) Mallar ger dig flexibilitet att skicka plattformsoberoende pushes och lokaliserade pushes. Läs om hur du registrerar mallar.
* [Diagnostisera problem med push-meddelanden](../notification-hubs/notification-hubs-push-notification-fixer.md) Det finns olika orsaker till varför meddelanden kan tas bort eller inte hamnar på enheter. Det här avsnittet visar hur du analyserar och räkna ut orsaken till push-meddelandefel.

Överväg att fortsätta till någon av följande självstudier:

* [Lägga till autentisering i appen](app-service-mobile-windows-store-dotnet-get-started-users.md) Läs om hur du autentiserar användare i din app med en identitetsprovider.
* [Aktivera offlinesynkronisering för din app](app-service-mobile-windows-store-dotnet-get-started-offline-data.md) Läs om hur du lägger till offlinesupport för din app med hjälp av en backend för mobilappen. Med offlinesynkronisering kan slutanvändarna interagera med mobilappen och &mdash;se, lägga till och ändra data&mdash; även när det inte finns någon nätverksanslutning.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->
