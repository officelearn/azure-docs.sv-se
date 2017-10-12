---
title: "Komma igång med Azure Notification Hubs för Universal Windows Platform-appar | Microsoft Docs"
description: "I de här självstudierna kommer du att få lära dig hur du använder Azure Notification Hubs för att skicka push-meddelanden till en Windows Universal-plattformsapp."
services: notification-hubs
documentationcenter: windows
author: ysxu
manager: erikre
editor: erikre
ms.assetid: cf307cf3-8c58-4628-9c63-8751e6a0ef43
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: e18a810bcdbd97c79418f53c647df8723ecb6076
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-notification-hubs-for-universal-windows-platform-apps"></a>Kom igång med Notification Hubs för Universal Windows Platform-appar

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Översikt
I den här artikeln beskrivs hur du använder Azure Notification Hubs för att skicka push-meddelanden till en Windows Universal Platform-app.

I den här artikeln skapar du en tom Windows Store-app som tar emot push-meddelanden med hjälp av Windows Push Notification Service (WNS). När du är klar kan du använda meddelandehubben för att sända push-meddelanden till alla enheter som kör appen.

## <a name="before-you-begin"></a>Innan du börjar
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Du hittar den färdiga koden för den här självstudiekursen på [GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/GetStartedWindowsUniversal).

## <a name="prerequisites"></a>Krav
För den här kursen behöver du följande:

* [Microsoft Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs) eller senare
* [Installerade verktyg för UWP-apputveckling](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up)
* Ett aktivt Azure-konto  
    Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information finns i [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-store-dotnet-get-started%2F).
* Ett aktivt Windows Store-konto

Du måste slutföra den här självstudiekursen innan du börjar någon annan kurs om Notification Hubs för UWP-appar.

## <a name="register-your-app-for-the-windows-store"></a>Registrera din app för Windows Store
Om du vill skicka push-meddelanden till UWP-appar, associerar du din app med Windows Store. Konfigurera sedan meddelandehubben för att integrera den med WNS.

1. Om du inte redan har registrerat appen navigerar du till [Windows Dev Center](https://dev.windows.com/overview), loggar in med ditt Microsoft-konto och väljer sedan **Skapa en ny app**.

2. Ange ett namn på appen och markera sedan **Reservera appnamn**. På så sätt skapas en ny Windows Store-registrering för din app.

3. I Visual Studio skapar du ett nytt Visual C# Store Apps-projekt med hjälp av UWP-mallen **Tom app** och väljer sedan **OK**.

4. Acceptera standardinställningarna för mål- och minsta plattformsversioner.

5. I Solution Explorer högerklickar du på Windows Store-approjektet, väljer **Store** och väljer sedan **Associate App with the Store** (associera app med Store).  
    Guiden **Associera din app med Windows Store** visas.

6. I guiden loggar du in med ditt Microsoft-konto.

7. Välj den app som du registrerade i steg 2, väljer **Nästa** och välj sedan **Associera**. På så sätt läggs den registreringsinformation som krävs för Windows Store till i programmanifestet.

8. Tillbaka på sidan [Windows Dev Center](http://dev.windows.com/overview) för din nya app väljer du **Tjänster**, väljer **Push-meddelanden** och väljer sedan **WNS/MPNS**.

9. Välj **Nytt meddelande**.

10. Välj mallen **Tomt (popup)** och välj sedan **OK**.

11. Ange ett **namn** på meddelandet och ett visuellt **kontextmeddelande** och välj sedan **Spara som utkast**.

12. Gå till [programregistreringsportalen](http://apps.dev.microsoft.com) och logga in.

13. Välj namnet på appen. Anteckna lösenordet för **Application Secret** (programhemlighet) och **Package security identifier (SID)** (paketsäkerhets-ID) som du hittar i plattformsinställningarna för **Windows Store**.

    >[!WARNING]
    >Programhemligheten och paket-SID:et är viktiga säkerhetsuppgifter. Lämna aldrig ut dessa uppgifter till någon och distribuera dem inte tillsammans med din app.

## <a name="configure-your-notification-hub"></a>Konfigurera meddelandehubben
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="5">
<li><p>Välj <b>Meddelandetjänster</b> > <b>Windows (WNS)</b>, och ange sedan lösenordet för programhemligheten i rutan <b>Säkerhetsnyckel</b>. I rutan <b>Paket-SID</b> anger du värdet som du fick från WNS i föregående avsnitt och väljer sedan <b>Spara</b>.</p>
</li>
</ol>

![Rutorna Paket-SID och Säkerhetsnyckel](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Din meddelandehubb har nu konfigurerats för att fungera med WNS. Du har anslutningssträngarna för att registrera din app och skicka meddelanden.

## <a name="connect-your-app-to-the-notification-hub"></a>Anslut appen till meddelandehubben
1. Högerklicka på lösningen i Vision Studio och välj sedan **Hantera NuGet-paket**.  
    Fönstret **Hantera NuGet-paket** öppnas.

2. I sökrutan anger du **WindowsAzure.Messaging.Managed**, väljer **Installera** och godkänner användningsvillkoren.
   
    ![Fönstret Hantera NuGet-paket][20]
   
    Den här åtgärden hämtar, installerar och lägger till en referens i Azure-meddelandebiblioteket för Windows med hjälp av [NuGet-paketet WindowsAzure.Messaging.Managed](http://nuget.org/packages/WindowsAzure.Messaging).

3. Öppna projektfilen App.xaml.cs och lägg till följande `using`-uttryck: 
   
        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.UI.Popups;

4. I filen App.xaml.cs ska du även lägga till följande **InitNotificationsAsync**-metoddefinitionen i **App**-klassen:
   
        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
   
            var hub = new NotificationHub("<your hub name>", "<Your DefaultListenSharedAccessSignature connection string>");
            var result = await hub.RegisterNativeAsync(channel.Uri);
   
            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
   
        }
   
    Den här koden hämtar URI-kanalen för appen från WNS och registrerar sedan denna med din meddelandehubb.
   
    >[!NOTE]
    >* Ersätt platshållaren **hub name** (hubbnamn) med namnet på meddelandehubben som visas i Azure-portalen. 
    >* Byt även ut platshållaren för anslutningssträngen mot anslutningssträngen **DefaultListenSharedAccessSignature** som du fick från sidan **Åtkomstpolicyer** i din meddelandehubb i ett föregående avsnittet.
   > 
   > 
5. Längst upp i händelsehanteraren **OnLaunched** i App.xaml.cs lägger du till följande anrop till den nya **InitNotificationsAsync**-metoden:
   
        InitNotificationsAsync();
   
    Den här åtgärden garanterar även att URI-kanalen registreras i meddelandehubben varje gång appen startas.

6. Kör appen genom att trycka på **F5**-tangenten. En dialogruta som innehåller registreringsnyckeln visas.

Appen är nu redo att ta emot popup-meddelanden.

## <a name="send-notifications"></a>Skicka meddelanden
Du kan snabbt testa att ta emot meddelanden i appen genom att skicka meddelanden från [Azure-portalen](https://portal.azure.com/). Använd knappen **Skicka test** i meddelandehubben så som visas på följande bild:

![Fönstret Skicka test](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)

Push-meddelanden skickas vanligtvis i en serverdelstjänst som Mobile Services eller ASP.NET med hjälp av ett kompatibelt bibliotek. Du kan också skicka meddelanden genom att använda REST-API:et direkt om ett bibliotek inte är tillgängligt för din serverdel. 

Den här självstudiekursen visar hur du kan enkelt testa klientappen genom att skicka meddelanden som använder .NET SDK för meddelandehubbar från ett konsolprogram i stället för en servertjänst. Vi rekommenderar att du går vidare med självstudiekursen [Använda Notification Hubs för att skicka push-meddelanden till användare] som nästa steg i att skicka meddelanden från en ASP.NET-serverdel. Du kan dock skicka meddelanden med hjälp av följande metoder:

* **REST-gränssnitt**: Du kan använda meddelanden på valfri serverdelsplattform med hjälp av [REST-gränssnittet](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).

* **Microsoft Azure Notification Hubs .NET SDK**: I NuGet Package Manager för Visual Studio kör du [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

* **Node.js**: Mer information finns i [Använda Notification Hubs från Node.js](notification-hubs-nodejs-push-notification-tutorial.md).
* **Azure Mobile Apps**: För ett exempel på hur man skickar meddelanden från en Azure-mobilapp som är integrerad med Notification Hubs kan du gå till [Lägg till Push-meddelanden för Mobile Apps](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md).

* **Java eller PHP**: Exempel på hur du skickar meddelanden med hjälp av REST-API:er finns i:
    * [Java](notification-hubs-java-push-notification-tutorial.md)
    * [PHP](notification-hubs-php-push-notification-tutorial.md)

## <a name="optional-send-notifications-from-a-console-app"></a>(Valfritt) Skicka meddelanden från en konsolapp
Följ anvisningarna nedan om du vill skicka meddelanden med hjälp av ett .NET-konsolprogram: 

1. Högerklicka på lösningen, välj **Lägg till** > **Nytt projekt**, välj **Windows** och **Konsolprogram** under **Visual C#** och välj sedan **OK**.
   
    Ett nytt Visual C#-konsolprogram läggs till i lösningen. Du kan också lägga till projektet i en separat lösning.

2. I Visual Studio väljer du **Tools** (verktyg), väljer **NuGet Package Manager** (NuGet-pakethanterare) och väljer sedan **Package Manager Console** (pakethanterarkonsolen).
   
    Pakethanterarkonsolen öppnas i Visual Studio.

3. I fönstret Package Manager-konsol ställer du in **standardprojektet** till det nya projektet för konsolprogrammet. Sedan kör du följande kommando i konsolfönstret:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
    Den här åtgärden lägger till en referens i Azure Notification Hubs SDK med hjälp av [Microsoft.Azure.Notification Hubs-NuGet-paketet](http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).
   
    ![Namnet Standardprojekt](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

4. Öppna filen Program.cs och lägg sedan till följande `using`-uttryck:
   
        using Microsoft.Azure.NotificationHubs;

5. Lägg till följande metod i **Program**-klassen:
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
            await hub.SendWindowsNativeNotificationAsync(toast);
        }
   
    >[!NOTE]
    >* Ersätt platshållaren **hub name** (hubbnamn) med namnet på meddelandehubben som visas i Azure-portalen. 
    >* Byt ut platshållaren för anslutningssträngen mot anslutningssträngen **DefaultFullSharedAccessSignature** som du fick från sidan **Åtkomstpolicyer** i meddelandehubben i avsnittet Konfigurera meddelandehubben.
    >* Använd den anslutningssträng som har *fullständig* åtkomst, inte enbart åtkomst för att *lyssna*. Strängen med lyssna-åtkomst har inte behörighet att skicka meddelanden.
   > 
   > 
6. Lägg till följande rader i **Main**-metoden:
   
         SendNotificationAsync();
         Console.ReadLine();

7. Högerklicka på konsolprogramprojektet i Visual Studio och välj sedan **Konfigurera som startprojekt** för att ställa in det som startprojekt. Tryck på **F5**-tangenten för att köra appen.
   
    Du får ett popup-meddelande på alla registrerade enheter. Appen läses in när du väljer eller knackar på popup-banderollen.

Du hittar alla nyttolaster som stöds under ämnena [toast catalog] (katalog över popup-meddelanden), [tile catalog] (katalog över paneler) och [badge overview] (översikt över aktivitetsikoner) på MSDN.

## <a name="next-steps"></a>Nästa steg
I det här enkla exemplet skickar du broadcast-meddelanden till alla dina Windows-enheter med hjälp av portalen eller ett konsolprogram. Vi rekommenderar att du går vidare med självstudiekursen [Använda Notification Hubs för att skicka push-meddelanden till användare] som nästa steg. Den visar hur du skickar meddelanden från en ASP.NET-serverdel genom att använda taggar för specifika användare.

Om du vill dela in användarna efter intressegrupper, kan du gå till [Använda Notification Hubs för att skicka de senaste nyheterna]. 

Mer allmän information om Notification Hubs finns i [Riktlinjer om Notification Hubs](notification-hubs-push-notification-overview.md).

<!-- Images. -->
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->

[Använda Notification Hubs för att skicka push-meddelanden till användare]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Använda Notification Hubs för att skicka de senaste nyheterna]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md

[toast catalog]: http://msdn.microsoft.com/library/windows/apps/hh761494.aspx
[tile catalog]: http://msdn.microsoft.com/library/windows/apps/hh761491.aspx
[badge overview]: http://msdn.microsoft.com/library/windows/apps/hh779719.aspx
 
