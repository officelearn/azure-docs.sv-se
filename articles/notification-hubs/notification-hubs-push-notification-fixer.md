---
title: "Azure Notification Hubs – diagnos riktlinjer"
description: "Riktlinjer för felsökning av vanliga problem med Azure Notification Hubs."
services: notification-hubs
documentationcenter: Mobile
author: ysxu
manager: erikre
editor: 
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: 32e3a2e6f840afd865375a622cfae0d33ba65090
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="azure-notification-hubs---diagnosis-guidelines"></a>Azure Notification Hub - diagnos riktlinjer
## <a name="overview"></a>Översikt
En av de vanligaste frågorna vi får höra från Azure Notification Hubs kunder är hur ta reda på varför de ser ett meddelande som skickas från sina serverdelens program visas på klientenheten - där och varför meddelanden har tagits bort och hur detta kan åtgärdas. I den här artikeln ska vi gå igenom olika anledningarna till varför meddelanden kan hämta bort eller inte hamnar på enheter. Vi kommer också titta igenom sätt som du kan analysera och ta reda på orsaken. 

Först och främst är det viktigt att förstå hur Azure Notification Hubs skickar meddelanden till enheter.
![][0]

I en typisk skicka meddelande flöde meddelandet har skickats från den **programmet backend** till **Azure Notification Hub (NH)** som i sin tur har vissa bearbetning på alla registreringar med hänsyn till den Konfigurera taggar & Tagguttryck att fastställa ”mål” d.v.s. alla registreringar som behöver ta emot push-meddelande. Dessa registreringar kan sträcka sig över allt eller något av våra plattformar som stöds – iOS, Google, Windows, Windows Phone Kindle och Baidu för Kina Android. När den är riktad mot upprättas NH sedan push-meddelanden ut meddelanden, dela upp på flera batch registreringar för den specifika enhetsplattformen **Push Notification Service (PNS)** -t ex APN för Apple, GCM för Google osv. NH autentiserar med respektive PNS baserat på de autentiseringsuppgifter som du anger i den klassiska Azure-portalen på sidan Konfigurera Meddelandehubben. Pns-systemet vidarebefordrar aviseringar till respektive **klientenheter**. Detta är den plattform som rekommenderat sätt att leverera push-meddelanden och Observera att den sista delen av Meddelandeleveransen sker mellan PNS-plattformen och enheten. Därför har vi fyra viktiga komponenter - *klienten*, *programmet backend*, *Azure Notification Hubs (NH)* och *Push Notification Services (PNS)*  och någon av dessa kan orsaka meddelanden avbryts. Mer information om den här arkitekturen finns på [översikt över Notification Hubs].

Det gick inte att leverera meddelanden kan inträffa under inledande testning fas som kan tyda på ett problem eller det kan hända i produktionen där alla eller vissa av meddelanden kan komma släppas som indikerar ett djupare program eller meddelanden mönster problemet. I avsnittet beskrivs nedan olika ignorerade meddelanden scenarier, från vanliga till sällsynta typ, vilket kan vara uppenbara och vissa andra inte så mycket. 

## <a name="azure-notifications-hub-mis-configuration"></a>Azure felkonfigurerad för Meddelandehubben
Azure Notification Hub måste autentisera sig själv i samband med utvecklarens program för att kunna skicka meddelanden till respektive pns-systemet. Detta är möjligt genom att utvecklare skapar ett utvecklarkonto med respektive plattforms (Google, Apple, Windows osv) och sedan registrera sina program där de få autentiseringsuppgifter som måste konfigureras i portalen under Notification Hubs konfigurationsavsnittet. Om inga meddelanden gör via, ska första steget att säkerställa att rätt autentiseringsuppgifter har konfigurerats på den Meddelandehubb som matchar dem med programmet skapas under deras specifika utvecklarkonto plattform. Du hittar vår [komma igång-självstudierna] användbar för att gå igenom den här processen på ett sätt som steg för steg. Här följer några vanliga konfigurationer för att:

1. **Allmänt**
   
    en) se till att namnet på din meddelandehubb (utan stavfel) är samma:
   
   * Om du registrerar från klienten, 
   * Om du skickar meddelanden från serverdelen,  
   * Om du har konfigurerat PNS-autentiseringsuppgifter och 
   * Vars SAS-autentiseringsuppgifter som du har konfigurerat på klienten och serverdelen. 
     
     b) se till att du använder rätt SAS configuration strängar på klienten och serverdelen för programmet. Som en tumregel måste du använda den **DefaultListenSharedAccessSignature** på klienten och **DefaultFullSharedAccessSignature** på serverdelen program (som ger behörighet för att kunna Skicka meddelande till NH)
2. **Konfiguration för Apple Push-aviseringstjänsten (APNS)**
   
    Du måste upprätthålla två olika hubs – en för produktion och en annan för att testa syfte. Det innebär att ladda upp det certifikat som du ska använda i begränsat läge till en separat NAV och det certifikat som du ska använda i produktionen till ett separat NAV. Försök inte att överföra olika typer av certifikat till samma hubb eftersom det kan orsaka fel för avisering ned raden. Om du hittar själv i ett läge där du av misstag har överfört olika typer av certifikat till samma hubb, rekommenderas det att ta bort hubben och börja om från början. Om du av någon anledning, det inte går att ta bort hubben sedan minst, måste du ta bort alla befintliga registreringar från hubben. 
3. **Google Cloud Messaging (GCM) konfiguration** 
   
    en) se till att du aktiverar ”Google Cloud Messaging för Android” under projektet molnet. 
   
    ![][2]
   
    b) se till att du skapar en ”servernyckel” medan hämta autentiseringsuppgifterna vilka NH använder för att autentisera med GCM. 
   
    ![][3]
   
    c) se till att du har konfigurerat ”projekt-ID” på klienten som är en helt numeriskt entitet som du kan hämta från instrumentpanelen:
   
    ![][1]

## <a name="application-issues"></a>Programproblem
1) **Taggar / tagg uttryck**

Om du använder taggar eller Tagguttryck för att segmentera målgruppen, är det alltid möjligt när du skickar meddelandet, det finns inget mål har hittats baserat på taggar/Tagguttryck som du anger i send-anrop. Det är bäst att granska din registreringar för att säkerställa att det inte finns några taggar som matchar när du skickar meddelanden och sedan kontrollera leveranskvitto enbart från klienter med dessa registreringar. T.ex. Om din registreringar med NH har återställts säger taggen ”politik” och du skickar ett meddelande med taggen ”sport”, skickas den inte till alla enheter. Komplexa fallet kan innebära Tagguttryck där du endast registrerade med ”tagg A” eller ”taggen B”, men vid sändning av meddelanden om du utvecklar ”taggen A & & taggen B”. I själv diagnosticera tips avsnittet nedan finns olika sätt som du kan granska din registreringar tillsammans med de taggar som de har. 

2) **Problem med mallar**

Om du använder mallar och sedan kontrollera att du följer riktlinjerna som beskrivs i [mall vägledning]. 

3) **Ogiltig registreringar**

Under förutsättning att Notification Hub har konfigurerats på rätt sätt och taggar/Tagguttryck användes korrekt ledde hitta giltig mål som meddelanden ska skickas, NH utlöses av flera bearbetning batchar parallellt - sändning av varje batch meddelanden till en uppsättning registreringar. 

> [!NOTE]
> Eftersom vi gör bearbetningen parallellt garanterar vi inte den ordning som meddelanden ska skickas. 
> 
> 

Nu har Azure Meddelandehubben optimerats för en ”en gång i de flesta” leveransmodell för meddelandet. Det innebär att vi försöka inaktiveringen duplicering så att inga meddelanden levereras mer än en gång till en enhet. Så här vi titta igenom registreringar och se till att endast en meddelandet skickas per enhetsidentifierare innan du faktiskt skickar meddelandet till pns-systemet. Varje grupp som skickas till pns-systemet, som i sin tur är att acceptera och validera registreringar, är det möjligt att pns-systemet upptäcker ett fel med en eller flera av registreringar i en batch returnerar felmeddelandet till Azure NH och avbryter bearbetningen vilket släppa den batch komple tely. Detta gäller särskilt med APNS som använder ett protokoll för TCP-ström. Även om vi är optimerade för i de flesta när leverans i detta fall vi ta bort arbetsbördan registreringen från vår databas och försök sedan Meddelandeleveransen för resten av enheter i den gruppen.

Du kan hämta information om fel för leverans av misslyckade försök mot en registrering med hjälp av de Azure Notification Hub REST API: er: [Per meddelande telemetri: hämta meddelanden meddelande telemetri](https://msdn.microsoft.com/library/azure/mt608135.aspx) och [PNS Feedback](https://msdn.microsoft.com/library/azure/mt705560.aspx). Finns det [SendRESTExample](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample) exempelkod.

## <a name="pns-issues"></a>PNS-problem
När meddelandet har tagits emot av respektive pns-systemet har sitt ansvar att leverera meddelandet till enheten. Azure Notification Hub ligger utanför den här bilden och har ingen kontroll när eller om meddelandet ska levereras till enheten. Eftersom platform notification services är ganska robust meddelanden tenderar att nå enheter inom några sekunder från pns-systemet. Om pns-systemet men begränsning sedan Azure Notification Hubs gäller en exponentiell tillbaka av strategi och om pns-systemet förblir kan nås för 30 minuter sedan har vi en princip för att gälla och släpp meddelandena permanent. 

Om en PNS-försök att leverera ett meddelande men enheten är offline, meddelandet lagras av pns-systemet under en begränsad tid, och levereras till enheten när den blir tillgänglig. Endast en senaste meddelande för en viss app lagras. Om flera meddelanden skickas när enheten är offline, gör varje nytt meddelande tidigare meddelandet ignoreras. Det här beteendet för att hålla senaste meddelandet kallas mottagarsidan meddelanden i APN och komprimera i GCM (som använder en komprimera nyckel). Om enheten är offline under en längre tid, ignoreras de meddelanden som lagrats för den. Käll - [APN vägledning] & [GCM vägledning]

Med Azure Notification Hubs – du kan skicka en buffertsammanslagning nyckel via ett HTTP-huvud med hjälp av allmänna `SendNotification` API (t.ex. för .NET SDK – `SendNotificationAsync`) som tar också HTTP-huvuden som skickas är att respektive pns-systemet. 

## <a name="self-diagnose-tips"></a>Själv diagnosticera tips
Här tittar vi på de olika vägar att diagnostisera och rot orsaka problemen Meddelandehubben:

### <a name="verify-credentials"></a>Verifiera autentiseringsuppgifter
1. **PNS developer-portalen**
   
    Kontrollera dem på respektive PNS developer portal (APN GCM, WNS osv) med vår [komma igång-självstudierna].
2. **Klassiska Azure-portalen**
   
    Gå till fliken Konfigurera för att granska och överensstämma med autentiseringsuppgifterna med de som erhålls från PNS developer-portalen. 
   
    ![][4]

### <a name="verify-registrations"></a>Kontrollera registreringar
1. **Visual Studio**
   
    Om du använder Visual Studio för utveckling kan du ansluta till Microsoft Azure och visa och hantera flera Azure-tjänster inklusive Meddelandehubben från ”Server Explorer”. Detta är främst användbart för din miljö för utveckling och testning. 
   
    ![][9]
   
    Du kan visa och hantera alla registreringar i din hubb som kategoriseras snyggt för plattformen, interna eller mallen registrering, alla taggar, PNS identifierare, registrerings-id och upphör att gälla. Du kan också redigera en registrering direkt - användbart säga om du vill redigera alla taggar. 
   
    ![][8]
   
   > [!NOTE]
   > Visual Studio-funktioner för att redigera registreringar bör endast användas under utveckling och testning med begränsat antal registreringar. Om det uppstår behöver åtgärda din registreringar i grupp, kan du använda Export/Import registrering funktioner som beskrivs här - [Export/Import-registreringar](https://msdn.microsoft.com/library/dn790624.aspx)
   > 
   > 
2. **Service Bus explorer**
   
    Många kunder använder ServiceBus explorer beskrivs här - [ServiceBus Explorer] för att visa och hantera sina meddelandehubben. Det är ett projekt med öppen källkod från code.microsoft.com - [ServiceBus Explorer-kod]

### <a name="verify-message-notifications"></a>Kontrollera meddelanden
1. **Klassisk Azure-portal**
   
    Du kan gå till fliken ”Debug” för att skicka testmeddelanden till dina klienter utan att behöva alla serverdelar service in och köra. 
   
    ![][7]
2. **Visual Studio**
   
    Du kan också skicka testmeddelanden från comforts av Visual Studio:
   
    ![][10]
   
    Du kan läsa mer om vilka funktioner Visual Studio Notification Hub Azure explorer här - 
   
   * [Översikt över VS Server Explorer]
   * [VS Server Explorer blogginlägget - 1]
   * [VS Server Explorer blogginlägget - 2]

### <a name="debug-failed-notifications-review-notification-outcome"></a>Felsöka misslyckade meddelanden / granska meddelanderesultat
**Egenskapen EnableTestSend**

När du skickar en avisering via Notification Hubs från början den bara hämtar i kö för NH sköta bearbetning för att ta reda på alla mål och slutligen NH skickar dem till pns-systemet. Det innebär att när du använder REST API eller någon av klient-SDK samtalet skicka lyckas returneras endast innebär att meddelandet har tagits har i kö med Notification Hub. En inblick i vad som hänt när NH fick så småningom att skicka meddelandet till PNS får inte. Om ditt meddelande inte inkommer på klientenheten, är det möjligt att när ett försök gjordes att leverera meddelandet till PNS NH, det uppstod ett fel som t.ex. nyttolastens storlek överskrider den tillåtna av pns-systemet eller de autentiseringsuppgifter som konfigurerats i NH är ogiltig osv. Om du vill få en inblick i PNS-fel har vi introducerades en egenskap som kallas [EnableTestSend funktionen]. Den här egenskapen aktiveras automatiskt när du skickar testmeddelanden från portalen eller Visual Studio-klienten och därför kan du visa detaljerad information för felsökning. Du kan använda den här via API: er som till exempel .NET SDK där den är tillgänglig nu och så småningom kommer att läggas till alla klient-SDK. Om du vill använda detta med REST-anrop, och Lägg bara till en querystring parameter med namnet ”test” i slutet av samtalet skicka t.ex. 

    https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test

*Exempel (.NET SDK)*

Anta att du använder .NET SDK för att skicka ett enhetligt popup-meddelande:

    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
    var result = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(result.State);

`result.State`kommer bara tillstånd `Enqueued` i slutet av körningen utan någon inblick i vad hände med din push. Nu kan du använda den `EnableTestSend` boolesk egenskap vid initiering av `NotificationHubClient` och kan få detaljerad statusinformation om PNS-fel uppstod vid sändning av meddelanden. Skicka anropet här tar extra tid att returnera eftersom det endast returnerar när NH har levereras meddelandet till PNS att fastställa resultatet. 

    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }

*Exempel på utdata*

    DetailedStateAvailable
    windows
    7619785862101227384-7840974832647865618-3
    The Token obtained from the Token Provider is wrong

Det här meddelandet innebär antingen ogiltiga autentiseringsuppgifter har konfigurerats i meddelandehubben eller ett problem med registreringen på hubben och rekommenderad skulle vara att ta bort denna registrering och låta klienten återskapa den innan du skickar meddelandet. 

> [!NOTE]
> Observera att användningen av den här egenskapen begränsas kraftigt och så du behöver bara använda detta i miljö för utveckling och testning med begränsad uppsättning registreringar. Vi kan bara skicka debug-meddelanden till 10 enheter. Vi har också en gräns för bearbetning av debug skickar vara 10 per minut. 
> 
> 

### <a name="review-telemetry"></a>Granska telemetri
1. **Använda klassiska Azure-portalen**
   
    Portalen kan du få en snabb överblick över all aktivitet på Meddelandehubben. 
   
    a) du kan visa en samlad vy över registreringar, aviseringar samt fel per plattform från fliken ”instrumentpanel”. 
   
    ![][5]
   
    b) du kan också lägga till många andra plattform specifika mått från fliken ”Övervakaren” för att granska djupare särskilt eventuella specifika PNS-fel som returneras när NH försöker skicka meddelandet till pns-systemet. 
   
    ![][6]
   
    c) du ska börja med att granska den **inkommande meddelanden**, **registrering Operations**, **lyckade meddelanden** och gå till per plattform fliken om du vill granska pns-systemet Vissa fel. 
   
    d) om du har meddelandehubben felkonfigurerat med inställningarna för autentisering och PNS-autentiseringsfel visas. Det här är en bra indikation på att kontrollera PNS-autentiseringsuppgifter. 

2) **Programmeringsåtkomst**

Mer information här- 

* [Programmässiga telemetri åtkomst]
* [Telemetri åtkomst via API: er exempel] 

> [!NOTE]
> Flera telemetri relaterade funktioner som **Export/Import registreringar**, **telemetri åtkomst via API: er** etc är bara tillgängliga i standardnivån. Om du försöker använda dessa funktioner i kostnadsfri eller grundläggande nivån ska du hämta Undantagsmeddelande detta när du använder SDK och en HTTP 403 (förbjuden) när du använder dem direkt från REST-API: er. Kontrollera att du har flyttat fram till Standard tjänstnivån via klassiska Azure-portalen.  
> 
> 

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/GCMConfigure.png
[2]: ./media/notification-hubs-diagnosing/GCMEnable.png
[3]: ./media/notification-hubs-diagnosing/GCMServerKey.png
[4]: ./media/notification-hubs-diagnosing/PortalConfigure.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalMonitoring.png
[7]: ./media/notification-hubs-diagnosing/PortalTestNotification.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png

<!-- LINKS -->
[översikt över Notification Hubs]: notification-hubs-push-notification-overview.md
[komma igång-självstudierna]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[mall vägledning]: https://msdn.microsoft.com/library/dn530748.aspx 
[APN vägledning]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW4
[GCM vägledning]: http://developer.android.com/google/gcm/adv.html
[Export/Import Registrations]: http://msdn.microsoft.com/library/dn790624.aspx
[ServiceBus Explorer]: http://msdn.microsoft.com/library/dn530751.aspx
[ServiceBus Explorer-kod]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[Översikt över VS Server Explorer]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx 
[VS Server Explorer blogginlägget - 1]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs 
[VS Server Explorer blogginlägget - 2]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/ 
[EnableTestSend funktionen]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx
[Programmässiga telemetri åtkomst]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[Telemetri åtkomst via API: er exempel]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel

