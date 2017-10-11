---
title: "Komma igång med Azure Mobile Engagement för universella Windows-appar"
description: "Lär dig hur du använder Azure Mobile Engagement med analyser och push-meddelanden för universella Windows-appar."
services: mobile-engagement
documentationcenter: windows
author: piyushjo
manager: erikre
editor: 
ms.assetid: 48103867-7f64-4646-b019-42bd797d38e2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 40db7e4dd151ec391c754dc6d4145aeeb8058eca
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-windows-universal-apps"></a>Kom igång med Azure Mobile Engagement för universella Windows-appar
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

I den här artikeln beskrivs hur du använder Azure Mobile Engagement för att förstå appanvändningen, och hur du skickar push-meddelanden till segmenterade användare i ett universellt Windows-program.
I den här kursen går vi igenom ett enkelt scenario för sändning med Mobile Engagement. Du skapar en tom universell Windows-app som samlar in grundläggande appanvändningsdata och tar emot push-meddelanden med Windows Notification Service (WNS).

> [!NOTE]
> Tjänsten Azure Mobile Engagement kommer att dras tillbaka i mars 2018 och är för närvarande endast tillgänglig för befintliga kunder. Mer information finns i [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/).

## <a name="prerequisites"></a>Krav
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

## <a name="set-up-mobile-engagement-for-your-windows-universal-app"></a>Konfigurera Mobile Engagement för din universella Windows-app
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Anslut appen till Mobile Engagement-serverdelen
I den här kursen behandlas en ”grundläggande integration”, vilket är den minsta uppsättningen som krävs för att samla in data och skicka ett push-meddelande. Den fullständiga integrationsdokumentationen finns i [Mobile Engagement Windows Universal SDK integration](mobile-engagement-windows-store-sdk-overview.md).

Du skapar en grundläggande app i Visual Studio för att demonstrera integrationen.

### <a name="create-a-windows-universal-app-project"></a>Skapa ett universellt Windows-approjekt
I följande steg används Visual Studio 2015, men stegen är ganska lika i tidigare versioner av Visual Studio.

1. Starta Visual Studio och välj **New Project** (Nytt projekt) på **startskärmen**.
2. Välj **Windows** -> **Universal** -> **Blank App (Universal Windows)** (Tom app [Universellt Windows]) i popup-fönstret. Ange appens **namn** och **lösningens namn**, och klicka sedan på **OK**.

    ![][1]

Du har nu skapat ett universellt Windows-app-projekt där du sedan integrerar Azure Mobile Engagement SDK.

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Ansluta appen till Mobile Engagement-serverdelen
1. Installera Nuget-paketet [MicrosoftAzure.MobileEngagement] i projektet. Om du utvecklar för både Windows- och Windows Phone-plattformen måste du göra det här för båda projekten. Samma Nuget-paket placerar rätt plattformsspecifika binärfiler i varje projekt för Windows 8.x och Windows Phone 8.1.
2. Öppna **Package.appxmanifest** och kontrollera att följande funktion har lagts till:

        Internet (Client)

    ![][2]
3. Ta sedan anslutningssträngen som du kopierade tidigare för Mobile Engagement-appen och klistra in den i filen `Resources\EngagementConfiguration.xml`, mellan taggarna `<connectionString>` och `</connectionString>`:

    ![][3]

    > [!TIP]
    > Om appen används på både Windows- och Windows Phone-plattformen ska du fortfarande skapa två Mobile Engagement-program – ett för varje plattform som stöds. Genom att ha två appar säkerställer du att du kan skapa rätt segmentering av målgruppen och skicka lämpliga målanpassade meddelanden för varje plattform.

    > [!IMPORTANT]
    > NuGet kopierar inte SDK-resurserna automatiskt i ditt program för Windows 10 UWP. Du måste göra det manuellt via de steg som visas (readme.txt) när NuGet-paketet installeras.  

1. I filen `App.xaml.cs`:

    a. Lägg till instruktionen `using`:

            using Microsoft.Azure.Engagement;

    b. Lägg till en metod som initierar Engagement:

           private void InitEngagement(IActivatedEventArgs e)
           {
             EngagementAgent.Instance.Init(e);

             //... rest of the code
           }

    c. Initiera SDK:n i metoden **OnLaunched**:

            protected override void OnLaunched(LaunchActivatedEventArgs e)
            {
              InitEngagement(e);

              //... rest of the code
            }

    c. Infoga följande i metoden **OnActivated** och lägg till metoden om den inte redan finns:

            protected override void OnActivated(IActivatedEventArgs e)
            {
              InitEngagement(e);

              //... rest of the code
            }

## <a id="monitor"></a>Aktivera realtidsövervakning
För att kunna börja skicka data och försäkra dig om att användarna är aktiva måste du skicka minst en skärm (aktivitet) till Mobile Engagement-serverdelen.

1. Lägg till följande `using`-instruktion i **MainPage.xaml.cs**:

    using Microsoft.Azure.Engagement.Overlay;
2. Byt ut grundklassen för **MainPage** från **Page** mot **EngagementPageOverlay**:

        class MainPage : EngagementPageOverlay
3. I filen `MainPage.xaml`:

    a. Lägg till följande i namnområdesdeklarationerna:

        xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

    b. Ersätt **Page** i XML-taggnamnet med **engagement:EngagementPageOverlay**

> [!IMPORTANT]
> Om sidan åsidosätter metoden `OnNavigatedTo` ska du anropa `base.OnNavigatedTo(e)`. Annars rapporteras inte aktiviteten (`EngagementPage` anropar `StartActivity` i tillhörande `OnNavigatedTo`-metod). Detta är i synnerhet viktigt i ett Windows Phone-projekt där standardmallen har en `OnNavigatedTo`-metod.
>
> För **Windows 10 Universal-appar** använder du den metod som rekommenderas i avsnittet ”Recommended method: overload your Page classes” (Rekommenderad metod: överbelasta dina sidklasser) i [Advanced Reporting with the Windows Universal Apps Engagement SDK](mobile-engagement-windows-store-advanced-reporting.md) (Avancerad rapportering med Engagement-SDK för Windows Universal-appar) i stället för den som nämns ovan.

## <a id="monitor"></a>Anslut appen med realtidsövervakning
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Aktivera push-meddelanden och meddelanden i appen
Med Mobile Engagement kan du samverka med användarna, och köra kampanjer med push-meddelanden och meddelanden i appen. Modulen som används för det heter REACH och finns i Mobile Engagement-portalen.
I följande avsnitt konfigurerar du appen för att ta emot dem.

### <a name="enable-your-app-to-receive-wns-push-notifications"></a>Konfigurera appen för att ta emot push-meddelanden med WNS
1. Ställ in **Toast capable** (Popup-kompatibel) på **Yes** (Ja) i filen `Package.appxmanifest` på fliken **Application** (Program) under **Notifications** (Meddelanden).

    ![][5]

### <a name="initialize-the-reach-sdk"></a>Initiera REACH SDK
I `App.xaml.cs` anropar du **EngagementReach.Instance.Init(e);** i funktionen **InitEngagement** direkt efter agentinitieringen:

        private void InitEngagement(IActivatedEventArgs e)
        {
           EngagementAgent.Instance.Init(e);
           EngagementReach.Instance.Init(e);
        }

Du är redo att skicka ett popup-meddelande. Sedan kontrollerar vi att den grundläggande integrationen har genomförts.

### <a name="grant-access-to-mobile-engagement-to-send-notifications"></a>Tillåt Mobile Engagement att skicka meddelanden
1. Öppna [Windows Store Dev Center] i webbläsaren, logga in och skapa ett konto om det behövs.
2. Klicka på **instrumentpanelen** längst upp till höger och klicka sedan på **Create a new app** (Skapa en ny app) på menyn i den vänstra panelen.

    ![][9]
3. Skapa din app genom att reservera dess namn.

    ![][10]
4. Navigera till **Services -> Push notifications** (Tjänster -> Push-meddelanden) på den vänstra menyn när appen har skapats.

    ![][11]
5. Klicka på länken **Live Services site** (Plats med livetjänster) i push-meddelandeavsnittet.

    ![][12]
6. Då kommer du till avsnittet för push-autentiseringsuppgifter. Kontrollera att du befinner dig i avsnittet **App Settings** (Appinställningar) och kopiera sedan **Package SID** (Paket-SID) och **Client secret** (Klienthemlighet).

    ![][13]
7. Navigera till **Settings** (Inställningar) i Mobile Engagement-portalen, och klicka på avsnittet **Native Push** (Systemspecifik push-avisering) till vänster. Klicka sedan på knappen **Edit** (Redigera) för att ange **Package security identifier (SID)** (Säkerhetsidentifierare för paket [SID]) och **Secret Key** (Hemlig nyckel) enligt bilden:

    ![][6]
8. Kontrollera slutligen att du har associerat Visual Studio-appen med den här skapade appen i appbutiken. Klicka på**Associate App with Store** (Associera appen med Store) i Visual Studio.

    ![][7]

## <a id="send"></a>Skicka ett meddelande till din app
[!INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

Om appen körs visas en avisering via appen. Om appen är stängd visas istället ett popup-meddelande.
Om du ser en avisering via app men inte något popup-meddelande och du kör appen i felsökningsläge i Visual Studio ska du prova **Lifecycle events -> Suspend** (Livscykelhändelser -> Gör uppehåll) i verktygsfältet för att säkerställa att appen har gjort uppehåll. Om du klickade på startsideknappen när du felsökte programmet i Visual Studio gör det inte alltid uppehåll och även om du ser aviseringen via appen visas den inte som ett popup-meddelande.  

![][8]

<!-- URLs. -->
[Mobile Engagement Windows Universal SDK documentation]: ../mobile-engagement-windows-store-integrate-engagement/
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9864592
[Windows Store Dev Center]: https://dev.windows.com
[Windows Universal Apps - Overlay integration]: ../mobile-engagement-windows-store-integrate-engagement-reach/#overlay-integration

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-store-dotnet-get-started/universal-app-creation.png
[2]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-capabilities.png
[3]: ./media/mobile-engagement-windows-store-dotnet-get-started/add-connection-info.png
[5]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-toast.png
[6]: ./media/mobile-engagement-windows-store-dotnet-get-started/enter-credentials.png
[7]: ./media/mobile-engagement-windows-store-dotnet-get-started/associate-app-store.png
[8]: ./media/mobile-engagement-windows-store-dotnet-get-started/vs-suspend.png
[9]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_create_app.png
[10]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_app_name.png
[11]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push.png
[12]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_1.png
[13]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_creds.png
