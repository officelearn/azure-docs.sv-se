---
title: "Lägg till push-meddelanden i appen universella Windowsplattformen (UWP) | Microsoft Docs"
description: "Lär dig hur du använder Azure App Service Mobile Apps och Azure Notification Hubs för att skicka push-meddelanden i appen universella Windowsplattformen (UWP)."
services: app-service\mobile,notification-hubs
documentationcenter: windows
author: conceptdev
manager: crdun
editor: 
ms.assetid: 6de1b9d4-bd28-43e4-8db4-94cd3b187aa3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: d51fa215ace8b33acb5d36b98e9a1a86e3296a65
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2018
---
# <a name="add-push-notifications-to-your-windows-app"></a>Lägg till push-meddelanden i appen för Windows
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Översikt
I kursen får du lägga till push-meddelanden till den [Windows Snabbstart](app-service-mobile-windows-store-dotnet-get-started.md) projekt så att ett push-meddelande skickas till enheten varje gång en post infogas.

Om du inte använder hämtade Snabbstart serverprojekt behöver push notification extension-paketet. Se [arbeta med serverdelen .NET SDK för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) för mer information.

## <a name="configure-hub"></a>Konfigurera en Meddelandehubb
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-your-app-for-push-notifications"></a>Registrera din app för push-meddelanden
Du måste skicka din app till Windows Store och sedan konfigurera serverprojektet att integrera med Windows Notification Services (WNS) att skicka push.

1. Högerklicka på UWP-appsprojektet i Visual Studio Solution Explorer, klicka på **Store** > **associera App med Store...** .

    ![Associera appen med Windows Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)
2. I guiden klickar du på **nästa**, logga in med ditt Microsoft-konto, Skriv ett namn för din app i **reservera ett nytt namn för appen**, klicka på **reservera**.
3. Efter registreringen app har skapats, Välj ny programnamn, klicka på **nästa**, och klicka sedan på **associera**. Detta lägger till den registreringsinformation som krävs för Windows Store i programmanifestet.  
4. Navigera till den [Windows Dev Center](https://dev.windows.com/en-us/overview), logga in med ditt Microsoft-konto, klickar du på den nya app registreringen i **Mina appar**, expandera **Services** > **Push-meddelanden**.
5. I den **Push-meddelanden** klickar du på **webbplatsen Live-tjänster** under **Microsoft Azure Mobile Services**.
6. På registreringssidan anteckna värdet under **programmet hemligheter** och **paket-SID**, som du sedan använder för att konfigurera din mobilappsserverdel.

    ![Associera appen med Windows Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

   > [!IMPORTANT]
   > Klienthemligheten och paket-SID:et är viktiga säkerhetsuppgifter. Lämna aldrig ut dessa uppgifter till någon och distribuera dem inte tillsammans med din app. Den **program-Id** används med hemlighet för att konfigurera Account autentisering.
   >
   >

## <a name="configure-the-backend-to-send-push-notifications"></a>Konfigurera serverdel och skicka push-meddelanden
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

## <a id="update-service"></a>Uppdatera servern för att skicka push-meddelanden
Använd proceduren nedan som matchar din serverdel projekttypen&mdash;antingen [.NET-serverdel](#dotnet) eller [Node.js-serverdel](#nodejs).

### <a name="dotnet"></a>.NET serverdelsprojektet
1. Högerklicka på serverprojekt i Visual Studio och klicka på **hantera NuGet-paket**, söka efter Microsoft.Azure.NotificationHubs och sedan på **installera**. Detta installerar klientbiblioteket Notification Hubs.
2. Expandera **domänkontrollanter**, öppna TodoItemController.cs och Lägg till följande using-instruktioner:

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;
3. I den **PostTodoItem** metod, Lägg till följande kod efter anropet till **InsertAsync**:

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

    Den här koden visar meddelandehubben för att skicka ett push-meddelande när ett nytt objekt är infogning.
4. Publicera om serverprojektet.

### <a name="nodejs"></a>Node.js serverdelsprojektet
1. Om du inte redan gjort det, [hämta snabbstartsprojektet](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) eller annan användning av [online redigeraren i Azure portal](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
2. Ersätt den befintliga koden i filen todoitem.js med följande:

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

    Detta skickar ett popup-meddelande med WNS som innehåller item.text när nya todo-objektet infogas.
3. Publicera om serverprojektet när du redigerar filen på den lokala datorn.

## <a id="update-app"></a>Lägg till push-meddelanden i appen
Appen måste registrera för push-meddelanden på Start. När du redan har aktiverat autentisering, se till att användaren loggar in innan du försöker registrera dig för push-meddelanden.

1. Öppna den **App.xaml.cs** projekt filen och Lägg till följande `using` instruktioner:

        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;
2. Lägg till följande i samma fil **InitNotificationsAsync** metoddefinitionen i **App** klass:

        private async Task InitNotificationsAsync()
        {
            // Get a channel URI from WNS.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register the channel URI with Notification Hubs.
            await App.MobileService.GetPush().RegisterAsync(channel.Uri);
        }

    Den här koden hämtar ChannelURI för appen från WNS och registrerar sedan denna ChannelURI med Mobilappen App Service.
3. Längst upp i den **OnLaunched** händelsehanteraren i **App.xaml.cs**, lägga till den **asynkrona** modifierare till metoddefinitionen och Lägg till följande anrop till den nya **InitNotificationsAsync** metod som i följande exempel:

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

            // ...
        }

    Detta garanterar att tillfällig ChannelURI registreras varje gång appen startas.
4. Återskapa projektet UWP-app. Appen är nu redo att ta emot popup-meddelanden.

## <a id="test"></a>Testa push-meddelanden i appen
[!INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

## <a id="more"></a>Nästa steg
Mer information om push-meddelanden:

* [Så här använder du den hanterade klienten för Azure Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md#pushnotifications)  
  Mallar ger dig möjlighet att skicka push-meddelanden för flera plattformar och lokaliserade push-meddelanden. Lär dig hur du registrerar mallar.
* [Diagnostisera problem för push-meddelande](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  Det finns olika orsaker till varför meddelanden kan hämta bort eller inte hamnar på enheter. Det här avsnittet visar hur du analyserar och ta reda på orsaken till fel för push-meddelande.

Tänk fortsätter in på något av följande kurser:

* [Lägg till autentisering i appen](app-service-mobile-windows-store-dotnet-get-started-users.md)  
  Läs om hur du autentiserar användare i appen med en identitetsleverantör.
* [Aktivera offlinesynkronisering av appen](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Läs om hur du lägger till offlinestöd i appen genom en mobilappsserverdel. Med offlinesynkronisering kan slutanvändarna interagera med mobilappen och &mdash;se, lägga till och ändra data&mdash; även när det inte finns någon nätverksanslutning.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->
