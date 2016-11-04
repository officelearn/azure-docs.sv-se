---
title: Kom igång med Azure Mobile Engagement för Windows Phone Silverlight-appar
description: Lär dig hur du använder Azure Mobile Engagement med analyser och push-meddelanden för Windows Phone Silverlight-appar.
services: mobile-engagement
documentationcenter: windows
author: piyushjo
manager: dwrede
editor: ''

ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo

---
# Kom igång med Azure Mobile Engagement för Windows Phone Silverlight-appar
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

I den här artikeln beskrivs hur du använder Azure Mobile Engagement för att förstå appanvändningen, och hur du skickar push-meddelanden till segmenterade användare i ett Windows Phone Silverlight-program.
I den här kursen visas ett enkelt scenario för sändning med Mobile Engagement. Du får skapa en tom Windows Phone Silverlight-app som samlar in grundläggande data och tar emot push-meddelanden med Microsoft Push Notification Service (MPNS).

> [!NOTE]
> Om du utvecklar för Windows Phone 8.1 (icke-Silverlight), finns det mer info i den [universella Windows-kursen](mobile-engagement-windows-store-dotnet-get-started.md).
> 
> 

För den här kursen behöver du följande:

* Visual Studio 2013
* [MicrosoftAzure.MobileEngagement] NuGet-paket

> [!NOTE]
> Du måste ha ett aktivt Azure-konto för att slutföra den här kursen. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer info om den kostnadsfria utvärderingsversionen av Azure finns [här](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-windows-phone-get-started).
> 
> 

## <a id="setup-azme"></a>Konfigurera Mobile Engagement för din Windows Phone-app
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Ansluta appen till Mobile Engagement-serverdelen
I den här kursen behandlas en ”grundläggande integration”, vilket är den minsta uppsättningen som krävs för att samla in data och skicka ett push-meddelande. Den fullständiga integrationsdokumentationen finns i [Mobile Engagement Windows Phone SDK integration](mobile-engagement-windows-phone-sdk-overview.md)

Vi skapar en grundläggande app i Visual Studio för att demonstrera integrationen.

### Skapa ett nytt Windows Phone Silverlight-projekt
I följande steg används Visual Studio 2015, men stegen är ganska lika i tidigare versioner av Visual Studio. 

1. Starta Visual Studio och välj **New Project** (Nytt projekt) på **startskärmen**.
2. Välj **Windows 8** -> **Windows Phone** -> **Blank App (Windows Phone Silverlight)** (Tom app (Windows Phone Silverlight)) i popup-fönstret. Ange appens **namn** och **lösningens namn**, och klicka sedan på **OK**.
   
    ![][1]
3. Du kan välja antingen **Windows Phone 8.0** eller **Windows Phone 8.1**.

Nu har du skapat en ny Windows Phone Silverlight-app där Azure Mobile Engagement SDK kan integreras.

### Ansluta appen till Mobile Engagement-serverdelen
1. Installera NuGet-paketet [MicrosoftAzure.MobileEngagement] i projektet.
2. Öppna `WMAppManifest.xml` (under egenskapsmappen) och kontrollera att följande har angetts (lägg till dem om de saknas) i taggen `<Capabilities />`:
   
        <Capability Name="ID_CAP_NETWORKING" />
        <Capability Name="ID_CAP_IDENTITY_DEVICE" />
   
    ![][2]
3. Klistra sedan in anslutningssträngen som du kopierade tidigare för Mobile Engagement-appen och klistra in den i filen `Resources\EngagementConfiguration.xml`, mellan taggarna `<connectionString>` och `</connectionString>`:
   
    ![][3]
4. I filen `App.xaml.cs`:
   
    a. Lägg till instruktionen `using`:
   
            using Microsoft.Azure.Engagement;
   
    b. Initiera SDK i metoden `Application_Launching`:
   
            private void Application_Launching(object sender, LaunchingEventArgs e)
            {
              EngagementAgent.Instance.Init();
            }
   
    c. Infoga följande i `Application_Activated`:
   
            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
               EngagementAgent.Instance.OnActivated(e);
            }

## <a id="monitor"></a>Aktivera realtidsövervakning
För att kunna börja skicka data och försäkra dig om att användarna är aktiva måste du skicka minst en skärm (aktivitet) till Mobile Engagement-serverdelen.

1. Lägg till instruktionen `using` i MainPage.xaml.cs:
   
        using Microsoft.Azure.Engagement;
2. Ersätt grundklassen i **MainPage**, som kommer före **PhoneApplicationPage**, med **EngagementPage**.
   
        class MainPage : EngagementPage 
3. I filen `MainPage.xml`:
   
    a. Lägg till följande i namnområdesdeklarationerna:
   
         xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
   
    b. Ersätt `phone:PhoneApplicationPage` i XML-taggnamnet med `engagement:EngagementPage`.

## <a id="monitor"></a>Anslut appen med realtidsövervakning
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Aktivera push-meddelanden och meddelanden i appen
Med Mobile Engagement kan du samverka med användarna, och köra kampanjer med push-meddelanden och meddelanden i appen. Modulen som används för det heter REACH och finns i Mobile Engagement-portalen.
I följande avsnitt konfigurerar du appen för att ta emot dem.

### Konfigurera appen för att ta emot push-meddelanden med MPNS
Lägg till nya funktioner i filen `WMAppManifest.xml`:

        ID_CAP_PUSH_NOTIFICATION
        ID_CAP_WEBBROWSERCOMPONENT

   ![][5]

### Initiera REACH SDK
1. I `App.xaml.cs` anropar du `EngagementReach.Instance.Init();` i funktionen **Application_Launching**, direkt efter agentinitieringen:
   
        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
           EngagementAgent.Instance.Init();
           EngagementReach.Instance.Init();
        }
2. I `App.xaml.cs` anropar du `EngagementReach.Instance.OnActivated(e);` i funktionen **Application_Activated**, direkt efter agentinitieringen:
   
        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
           EngagementReach.Instance.OnActivated(e);
        }

Då var allt klart. Nu är det dags att kontrollera att den grundläggande integrationen har genomförts.

## <a id="send"></a>Skicka ett meddelande till din app
[!INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

Du bör nu se ett meddelande på enheten som visas som en avisering via app om appen är öppen, annars visas det i form av ett popup-meddelande som liknar följande: 

![][6]

<!-- URLs. -->
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9874664
[Mobile Engagement Windows Phone SDK-dokumentation]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[2]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[3]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[5]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[6]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png



<!--HONumber=Oct16_HO3-->


