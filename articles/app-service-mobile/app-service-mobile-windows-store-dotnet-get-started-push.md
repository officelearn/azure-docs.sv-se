---
title: Lägg till push-meddelanden till din app för Universal Windows Platform (UWP) | Microsoft Docs
description: Lär dig hur du använder Azure App Service Mobile Apps och Azure Notification Hubs för att skicka push-meddelanden till din app för Universal Windows Platform (UWP).
services: app-service\mobile,notification-hubs
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 6de1b9d4-bd28-43e4-8db4-94cd3b187aa3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: c137f6cfdff9d043a656783f38ec9b894e644338
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50913027"
---
# <a name="add-push-notifications-to-your-windows-app"></a>Lägg till push-meddelanden till din Windows-app

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Översikt

I den här självstudien lägger du till push-meddelanden till den [Windows Snabbstart](app-service-mobile-windows-store-dotnet-get-started.md) projekt så att ett push-meddelande skickas till enheten varje gång en post infogas.

Om du inte använder serverprojekt hämtade Snabbstart måste tilläggspaket för push-meddelande. Se [arbeta med SDK för .NET-serverdelen för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) för mer information.

## <a name="configure-hub"></a>Konfigurera en Notification Hub

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-your-app-for-push-notifications"></a>Registrera din app för push-meddelanden

Du måste skicka in din app till Microsoft Store och sedan konfigurera serverprojektet att integrera med [Windows Notification Services (WNS)](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) att skicka push.

1. I Visual Studio Solution Explorer högerklickar du på UWP-appsprojektet, klickar du på **Store** > **associera App med Store...** .

    ![Associera app med Microsoft Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)

2. I guiden klickar du på **nästa**, logga in med ditt Microsoft-konto, ange ett namn för din app i **reservera ett nytt appnamn**, klicka sedan på **reservera**.
3. När appregistreringen har skapats, Välj namnet på nya appen, klicka på **nästa**, och klicka sedan på **associera**. Detta lägger till registreringsinformationen som krävs Microsoft Store till i programmanifestet.
4. Navigera till den [Programregistreringsportalen](https://apps.dev.microsoft.com/) och logga in med ditt Microsoft-konto. Klicka på Windows Store-app som du har associerat i föregående steg.
5. På registreringssidan anteckna värdet under **programhemligheter** och **paket-SID**, som du sedan använder för att konfigurera din mobilappsserverdel.

    ![Associera app med Microsoft Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

   > [!IMPORTANT]
   > Klienthemligheten och paket-SID:et är viktiga säkerhetsuppgifter. Lämna aldrig ut dessa uppgifter till någon och distribuera dem inte tillsammans med din app. Den **program-Id** används med hemligheten för att konfigurera Account autentisering.

[App Center](https://docs.microsoft.com/appcenter/sdk/push/uwp#prerequisite---register-your-app-for-windows-notification-services-wns) har också instruktioner för att konfigurera UWP-appar för push-meddelanden.

## <a name="configure-the-backend-to-send-push-notifications"></a>Konfigurera serverdelen för att skicka push-meddelanden

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

## <a id="update-service"></a>Uppdatera servern för att skicka push-meddelanden

Använd proceduren nedan som matchar din serverdel projekttyp&mdash;antingen [.NET-serverdel](#dotnet) eller [Node.js-serverdel](#nodejs).

### <a name="dotnet"></a>.NET serverdelsprojekt

1. I Visual Studio högerklickar du på serverprojektet och klicka på **hantera NuGet-paket**, Sök efter Microsoft.Azure.NotificationHubs och sedan på **installera**. Detta installerar klientbiblioteket för Meddelandehubbar.
2. Expandera **domänkontrollanter**, öppna TodoItemController.cs och Lägg till följande using-satser:

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

    Den här koden visar meddelandehubben för att skicka ett push-meddelande när ett nytt objekt är infogning.

4. Publicera om serverprojektet.

### <a name="nodejs"></a>Node.js-serverdel-projekt
1. Om du inte redan gjort det, [hämta quickstart-projektet](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) eller annan användning den [onlineredigeraren i Azure-portalen](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
2. Ersätt den befintliga koden i filen todoitem.js med följande:

    ```javascript
    var azureMobileApps = require('azure-mobile-apps'),
    promises = require('azure-mobile-apps/src/utilities/promises'),
    logger = require('azure-mobile-apps/src/logger');

    var table = azureMobileApps.table();

    table.insert(function (context) {
    // For more information about the Notification Hubs JavaScript SDK,
    // see http://aka.ms/nodejshubs
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

    Detta skickar ett popup-meddelande med WNS som innehåller item.text när en ny att göra-objekt har infogats.

3. När du redigerar filen på din lokala dator, publicera om serverprojektet.

## <a id="update-app"></a>Lägg till push-meddelanden till din app
Nu ska måste din app registrera för push-meddelanden på Start. När du har redan aktiverat autentisering, se till att användaren loggar in innan du försöker registrera för push-meddelanden.

1. Öppna den **App.xaml.cs** projekt filen och Lägg till följande `using` instruktioner:

    ```csharp
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    ```

2. Lägg till följande i samma fil, **InitNotificationsAsync** metoddefinitionen i **App** klass:

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

3. Överst på den **OnLaunched** händelsehanteraren i **App.xaml.cs**, lägga till den **async** modifieraren till metoddefinitionen och Lägg till följande anrop till den nya  **InitNotificationsAsync** metod som i följande exempel:

    ```csharp
    protected async override void OnLaunched(LaunchActivatedEventArgs e)
    {
        await InitNotificationsAsync();

        // ...
    }
    ```

    Detta garanterar att tillfällig ChannelURI är registrerat varje gång programmet startas.

4. Återskapa din UWP-app-projekt. Appen är nu redo att ta emot popup-meddelanden.

## <a id="test"></a>Test-push-meddelanden i din app

[!INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

## <a id="more"></a>Nästa steg

Mer information om push-meddelanden:

* [Hur du använder den hanterade klienten för Azure Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md#pushnotifications) mallar får du flexibilitet för att skicka plattformsoberoende push-meddelanden och lokaliserade push-meddelanden. Lär dig hur du registrerar mallar.
* [Diagnostisera problem med push-meddelande](../notification-hubs/notification-hubs-push-notification-fixer.md) det finns olika orsaker till varför meddelanden kan tas bort eller inte hamnar på enheter. Det här avsnittet visar hur du analyserar och ta reda på orsaken till fel för push-meddelande.

Överväg fortsätter in på någon av följande självstudiekurser:

* [Lägga till autentisering i appen](app-service-mobile-windows-store-dotnet-get-started-users.md) Läs om hur du autentiserar användare i din app med en identitetsprovider.
* [Aktivera synkronisering offline för din app](app-service-mobile-windows-store-dotnet-get-started-offline-data.md) Lär dig hur du lägger till offlinestöd i appen genom en mobilappsserverdel. Med offlinesynkronisering kan slutanvändarna interagera med mobilappen och &mdash;se, lägga till och ändra data&mdash; även när det inte finns någon nätverksanslutning.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->
