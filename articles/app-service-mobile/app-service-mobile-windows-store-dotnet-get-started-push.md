---
title: Lägga till push-meddelanden i UWP-appen
description: Lär dig hur du använder Azure App Service Mobile Apps och Azure Notification Hubs för att skicka push-meddelanden till din Universell Windows-plattform (UWP)-app.
ms.assetid: 6de1b9d4-bd28-43e4-8db4-94cd3b187aa3
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: e2178322c7006a6ddd2ca6daffca066f04fff68c
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668732"
---
# <a name="add-push-notifications-to-your-windows-app"></a>Lägga till push-meddelanden i Windows-appen

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center stöder utveckling av slutpunkt till slutpunkt-tjänster och integrerade tjänster som är centrala för utveckling av mobilappar. Utvecklare kan använda tjänsterna för att **bygga**, **testa** och **distribuera** för att skapa en pipeline för kontinuerlig integrering och leverans. När appen har distribuerats kan utvecklarna övervaka status och användning av appen med hjälp av tjänsterna **Analys** och **Diagnostik**, och kommunicera med användarna via **Push**-tjänsten. Utvecklare kan också dra nytta av **Auth** för att autentisera sina användare och tjänsten **Data** för att spara och synkronisera appdata i molnet.
>
> Om du vill integrera molntjänster i ditt mobilprogram kan du registrera dig med [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) i dag.

## <a name="overview"></a>Översikt

I den här självstudien lägger du till push-meddelanden till [snabb starts](app-service-mobile-windows-store-dotnet-get-started.md) projektet i Windows så att ett push-meddelande skickas till enheten varje gång en post infogas.

Om du inte använder det nedladdade snabb start Server-projektet behöver du ett tilläggs paket för push-meddelanden. Mer information finns i [arbeta med .NET-Server del Server SDK för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

## <a name="configure-hub"></a>Konfigurera en Notification Hub

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-your-app-for-push-notifications"></a>Registrera din app för push-meddelanden

Du måste skicka in din app till Microsoft Store och sedan konfigurera ditt Server projekt att integrera med [Windows Notification Services (WNS)](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) för att skicka push.

1. I Visual Studio Solution Explorer högerklickar du på projektet UWP, klickar på **lagra** > **associera appen med butiken...** .

    ![Associera appen med Microsoft Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)

2. I guiden klickar du på **Nästa**, loggar in med ditt Microsoft-konto, skriver ett namn för din app i **reservera ett nytt**namn på appen och klickar sedan på **reservera**.
3. När appens registrering har skapats väljer du det nya namnet på appen, klickar på **Nästa**och sedan på **associera**. Detta lägger till nödvändig Microsoft Store registrerings information till applikations manifestet.
4. Gå till [program registrerings portalen](https://apps.dev.microsoft.com/) och logga in med din Microsoft-konto. Klicka på den Windows Store-app som du kopplade till i föregående steg.
5. På sidan registrering noterar du värdet under **program hemligheter** och **paket-sid**, som du kommer att använda för att konfigurera server delen för mobilappar.

    ![Associera appen med Microsoft Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

   > [!IMPORTANT]
   > Klienthemligheten och paket-SID:et är viktiga säkerhetsuppgifter. Lämna aldrig ut dessa uppgifter till någon och distribuera dem inte tillsammans med din app. **Program-ID: t** används med hemligheten för att konfigurera autentisering med Microsoft-konto.

[App Center](https://docs.microsoft.com/appcenter/sdk/push/uwp#prerequisite---register-your-app-for-windows-notification-services-wns) också innehåller instruktioner för att konfigurera UWP-appar för push-meddelanden.

## <a name="configure-the-backend-to-send-push-notifications"></a>Konfigurera Server delen för att skicka push-meddelanden

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

## <a id="update-service"></a>Uppdatera servern för att skicka push-meddelanden

Använd proceduren nedan som matchar din server dels projekt typ&mdash;antingen [.NET-Server](#dotnet) del eller [Node. js-backend](#nodejs).

### <a name="dotnet"></a>.NET-Server dels projekt

1. Högerklicka på Server projektet i Visual Studio och klicka på **Hantera NuGet-paket**, Sök efter Microsoft. Azure. NotificationHubs och klicka sedan på **Installera**. Detta installerar klient biblioteket för Notification Hubs.
2. Expandera **kontrollanter**, öppna TodoItemController.CS och Lägg till följande using-satser:

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

    Den här koden talar om för meddelande hubben att skicka ett push-meddelande efter att ett nytt objekt har infogats.

4. Publicera om Server projektet.

### <a name="nodejs"></a>Node. js-backend-projekt
1. Konfigurera ditt Server dels projekt.
2. Ersätt den befintliga koden i filen todoitem. js med följande:

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

    Detta skickar ett WNS popup-meddelande som innehåller objektet. text när ett nytt att göra-objekt infogas.

3. Publicera om Server projektet när du redigerar filen på den lokala datorn.

## <a id="update-app"></a>Lägg till push-meddelanden i din app
Sedan måste appen registreras för push-meddelanden vid start. När du redan har aktiverat autentisering kontrollerar du att användaren loggar in innan du försöker registrera dig för push-meddelanden.

1. Öppna projekt filen **app.XAML.cs** och Lägg till följande `using`-satser:

    ```csharp
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    ```

2. I samma fil lägger du till följande **InitNotificationsAsync** -Metod definition i klassen **app** :

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

    Den här koden hämtar ChannelURI för appen från WNS och registrerar sedan ChannelURI med din App Service mobilapp.

3. Längst upp i händelse hanteraren **OnLaunched** i **app.XAML.cs**lägger du till den **asynkrona** modifieraren i metod definitionen och lägger till följande anrop till den nya **InitNotificationsAsync** -metoden, som i följande exempel:

    ```csharp
    protected async override void OnLaunched(LaunchActivatedEventArgs e)
    {
        await InitNotificationsAsync();

        // ...
    }
    ```

    Detta garanterar att ChannelURI för kort levde registreras varje gång programmet startas.

4. Återskapa ditt UWP app-projekt. Appen är nu redo att ta emot popup-meddelanden.

## <a id="test"></a>Testa push-meddelanden i din app

[!INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

## <a id="more"></a>Nästa steg

Läs mer om push-meddelanden:

* [Så här använder du den hanterade klienten för Azure Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md#pushnotifications) Mallar ger dig flexibilitet att skicka plattforms oberoende push-meddelanden och lokaliserade push-meddelanden. Lär dig hur du registrerar mallar.
* [Diagnostisera problem med push-meddelanden](../notification-hubs/notification-hubs-push-notification-fixer.md) Det finns olika orsaker till varför meddelanden kan släppas eller inte avslutas på enheterna. Det här avsnittet visar hur du analyserar och avbildar rotor saken till problem med push-meddelanden.

Överväg att fortsätta till någon av följande Självstudier:

* [Lägga till autentisering i appen](app-service-mobile-windows-store-dotnet-get-started-users.md) Läs om hur du autentiserar användare i din app med en identitetsprovider.
* [Aktivera synkronisering offline för din app](app-service-mobile-windows-store-dotnet-get-started-offline-data.md) Lär dig hur du lägger till offline-stöd för din app med hjälp av en server del för mobila appar. Med offlinesynkronisering kan slutanvändarna interagera med mobilappen och &mdash;se, lägga till och ändra data&mdash; även när det inte finns någon nätverksanslutning.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->
