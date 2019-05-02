---
title: Diagnostisera utelämnade meddelanden i Azure Notification Hubs
description: Lär dig mer om att felsöka vanliga problem med utelämnade meddelanden i Azure Notification Hubs.
services: notification-hubs
documentationcenter: Mobile
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 04/04/2019
ms.author: jowargo
ms.openlocfilehash: eebf9ef63a8622c4cc431322b786fdf30f6352fe
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925826"
---
# <a name="diagnose-dropped-notifications-in-azure-notification-hubs"></a>Diagnostisera utelämnade meddelanden i Azure Notification Hubs

Vanliga frågor om Azure Notification Hubs är så här felsöker när meddelanden från ett program inte visas på klientenheter. Kunder vill veta var och varför meddelanden har tagits bort och hur du löser problemet. Den här artikeln beskrivs varför meddelanden kan tas bort eller inte tas emot av enheter. Den förklarar också hur du avgöra den bakomliggande orsaken.

Det är viktigt att först förstå hur Meddelandehubbar skickar meddelanden till en enhet.

![Notification Hubs-arkitektur][0]

I en typisk skicka meddelande flow meddelandet har skickats från den *serverdelen program* till Notification Hubs. Notification Hubs bearbetar alla registreringar. Det tar hänsyn till de konfigurerade taggar och Tagguttryck fastställa mål. Riktar sig registreringar som behöver ta emot push-meddelande. Dessa registreringar kan sträcka sig över någon av våra plattformar som stöds: Android, Baidu (Android-enheter i Kina), Fire OS (Amazon) iOS, Windows och Windows Phone.

Med de mål som upprättats Meddelandehubbar skickar meddelanden till den *push-meddelandetjänst* för enhetsplattformen. Exempel är Apple Push Notification service (APNs) för Apple och Firebase Cloud Messaging (FCM) för Google. Notification Hubs push-meddelanden dela upp på flera batchar av registreringar. Den autentiserar med respektive push notification service, baserat på de autentiseringsuppgifter som du anger i Azure-portalen under **konfigurera Meddelandehubben**. Push-meddelande tjänsten sedan vidarebefordrar meddelanden till respektive *klientenheter*.

Den sista delen av leverans av meddelanden är mellan plattformens push-meddelandetjänst och enheten. Leverans av meddelanden kan misslyckas på någon av fyra steg i processen för push-meddelande (klient, serverdelen program, Notification Hubs och plattformens push notification service). Mer information om Notification Hubs-arkitekturen finns i [översikt över Notification Hubs].

Ett fel att leverera meddelanden kan uppstå under inledande testning/mellanlagring fas. Utelämnade meddelanden i det här skedet kan tyda på ett konfigurationsproblem. Om det uppstår ett fel att leverera meddelanden i produktion, kan vissa eller alla meddelanden släppas. Ett djupare program eller messaging mönstret problemet anges i det här fallet.

Nästa avsnitt tittar på scenarier där meddelanden kan släppas, mellan vanliga och ovanliga.

## <a name="notification-hubs-misconfiguration"></a>Notification Hubs misconfiguration ##

Om du vill skicka meddelanden till respektive push-meddelandetjänst, måste Notification Hubs autentisera sig själv i samband med ditt program. Du måste skapa ett utvecklarkonto med den målplattform notification-tjänsten (Microsoft, Apple, Google osv.). Sedan måste du registrera ditt program med Operativsystemet där du får en token eller nyckel som används för att arbeta med PNS-Måltjänsten.

Du måste lägga till plattformen autentiseringsuppgifter till Azure-portalen. Om inga meddelanden ansluter till enheten, är det första steget för att säkerställa att rätt autentiseringsuppgifter har konfigurerats i Notification Hubs. Autentiseringsuppgifterna måste matcha det program som har skapats under ett plattformsspecifika developer-konto.

Stegvisa instruktioner för att slutföra den här processen, se [Kom igång med Azure Notification Hubs].

Här följer några vanliga felkonfigureringar att söka efter:

### <a name="notification-hub-name-location"></a>Notification hub namn plats

Se till att ditt meddelandehubbsnamn (utan skrivfel) är samma i var och en av de här platserna:
   * Registrerar från klienten
   * Där du skickar meddelanden från serverdelen
   * Där du konfigurerade push notification service-autentiseringsuppgifter

Se till att du använder strängar för signaturer konfiguration för rätt delad åtkomst på klienten och programmet tillbaka avslutas. I allmänhet måste du använda **DefaultListenSharedAccessSignature** på klienten och **DefaultFullSharedAccessSignature** på programmet serverdel. Detta ger behörighet att skicka meddelanden till Notification Hubs.

### <a name="apn-configuration"></a>APN-konfiguration ###

Du måste ha två olika hubs: en för produktion och en annan för testning. Du måste överföra certifikatet som du använder i en testmiljö till en separat hubb än certifikat/nav som du ska använda i produktion. Försök inte att överföra olika typer av certifikat till samma hubb. Det leder till fel i meddelande.

Om du av misstag överföra olika typer av certifikat till samma hubb, bör du ta bort hubben och starta på nytt med en ny hubb. Om du inte ta bort hubben av någon anledning, måste du minst ta bort alla befintliga registreringar från hubben.

### <a name="fcm-configuration"></a>FCM-konfiguration ###

1. Se till att den *servernyckel* du fick från Firebase matchar den servernyckel som du registrerade i Azure-portalen.

   ![Firebase servernyckel][3]

2. Kontrollera att du har konfigurerat **projekt-ID** på klienten. Du kan hämta värdet för **projekt-ID** från Firebase-instrumentpanelen.

   ![Firebase-projekt-ID][1]

## <a name="application-issues"></a>Programfel ##

### <a name="tags-and-tag-expressions"></a>Taggar och Tagguttryck ###

Om du använder taggar eller Tagguttryck för att segmentera målgruppen, är det möjligt att när du skickar meddelandet finns inget mål. Det här felet är baserad på den angivna taggar eller Tagguttryck i samtalet skicka.

Granska dina registreringar för att säkerställa att taggarna matchar när du skickar ett meddelande. Kontrollera leveranskvitto från endast klienter som har dessa registreringar.

Anta exempelvis att alla registreringar med Notification Hubs använder taggen ”politik”. Om du skickar ett meddelande med taggen ”sport”, skickas inte meddelandet till alla enheter. Komplexa fallet kan handla om Tagguttryck där du registrerat med hjälp av ”tagg A” *eller* ”tagg B”, men du mål ”tagg A & & taggen B.” Avsnittet självdiagnos tips senare i artikeln visar hur du granskar dina registreringar och deras taggar.

### <a name="template-issues"></a>Problem med mallar ###

Om du använder mallar kan du se till att du följer de riktlinjer som beskrivs i [mallar].

### <a name="invalid-registrations"></a>Ogiltig registreringar ###

Om meddelandehubben har konfigurerats korrekt och taggar eller Tagguttryck har använts på rätt sätt, finns giltigt mål. Meddelanden ska skickas till dessa mål. Notification Hubs och sedan utlöses av flera bearbetning batchar parallellt. Varje grupp som skickar meddelanden till en uppsättning registreringar.

> [!NOTE]
> Eftersom Notification Hubs bearbetar batchar parallellt, garanteras inte den ordning i vilken meddelanden levereras.

Meddelandehubbar är optimerad för en modell för leverans av ”i de flesta – en gång”-meddelande. Vi försöka deduplicering, så att inga meddelanden levereras mer än en gång till en enhet. Registreringar kontrolleras för att se till att endast en meddelandet skickas per enhetsidentifierare innan det skickas till push-meddelandetjänst.

Varje batch skickas till tjänsten push-meddelande, som i sin tur accepterar och validerar registreringarna. Under den här processen är det möjligt att push-meddelandetjänst identifierar ett fel med en eller flera registreringar i en batch. Push-meddelandetjänst returnerar ett fel till Notification Hubs och processen stoppas. Push-meddelandetjänst utelämnar den batch helt. Detta gäller särskilt med APNs, som använder en stream TCP-protokollet.

I det här fallet tas Åtkomstöverträdelse registreringen bort från databasen. Sedan gör vi om leverans av meddelanden för resten av enheter i den batch.

Om du vill ha mer felinformation om den misslyckade leveransförsök mot en registrering kan du använda Notification Hubs REST-API: er [telemetri Per meddelande: Hämta telemetri för meddelande meddelande](https://msdn.microsoft.com/library/azure/mt608135.aspx) och [PNS feedback](https://msdn.microsoft.com/library/azure/mt705560.aspx). Exempelkoden finns på [skicka REST-exempel](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/SendRestExample/).

## <a name="push-notification-service-issues"></a>Push-meddelande tjänstproblem

När push-meddelandetjänst tar emot meddelandet, du får meddelandet till enheten. Nu har Meddelandehubbar ingen kontroll över leverans av meddelanden till enheten.

Eftersom plattformarnas meddelandetjänster är robust, brukar meddelanden att nå enheter på några sekunder. Om push-meddelandetjänst begränsningar, gäller Meddelandehubbar en exponentiell backoff-strategi. Om push-meddelandetjänst är inte kan nås under 30 minuter, är det en princip för att upphöra att gälla och släpp meddelanden permanent.

Om en push-meddelandetjänst försöker leverera ett meddelande, men enheten är offline, lagras meddelandet av push-meddelandetjänst. Det sparas endast en begränsad tidsperiod. Meddelandet skickas till enheten när enheten blir tillgänglig.

Varje app lagrar bara en senaste meddelande. Om flera meddelanden skickas när en enhet är offline, gör varje nytt meddelande den sista som tas bort. Att hålla endast senaste meddelandet kallas *, RSC* i APNs och *komprimera* i FCM. (FCM används en komprimera nyckel.) När enheten fortfarande är offline under en längre tid, ignoreras meddelanden som har lagrats för enheten. Mer information finns i [APNs-översikt] och [Om FCM-meddelanden].

Med Meddelandehubbar kan skicka du en buffertsammanslagning nyckel via ett HTTP-huvud med hjälp av generiska SendNotification-API. Till exempel för .NET-SDK använder du `SendNotificationAsync`. API: et SendNotification tar också HTTP-huvuden som skickas som är att respektive push-meddelandetjänst.

## <a name="self-diagnosis-tips"></a>Tips för självdiagnos

Här följer sökvägar för att diagnostisera grundorsaken till utelämnade meddelanden i Meddelandehubbar.

### <a name="verify-credentials"></a>Verifiera autentiseringsuppgifter ###

#### <a name="push-notification-service-developer-portal"></a>Push notification service developer-portalen ####

Kontrollera autentiseringsuppgifterna i respektive push notification service developer-portalen (APNs, FCM, Windows-meddelandetjänsten och så vidare). Mer information finns i [Självstudie: Skicka push-meddelanden till Universal Windows Platform-appar med hjälp av Azure Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification).

#### <a name="azure-portal"></a>Azure Portal ####

Om du vill granska och överensstämma med autentiseringsuppgifterna med de som du fick från push notification service developer-portalen, gå till den **åtkomstprinciper** fliken i Azure-portalen.

![Azure-portalen åtkomstprinciper][4]

### <a name="verify-registrations"></a>Kontrollera registreringarna

#### <a name="visual-studio"></a>Visual Studio ####

Du kan ansluta till Azure via Server Explorer för att visa och hantera flera Azure-tjänster, inklusive Meddelandehubbar i Visual Studio. Den här genvägen är främst användbart för din miljö för utveckling/testning.

![Visual Studio Server Explorer][9]

Du kan visa och hantera alla registreringar i din hubb. Registreringarna kan kategoriseras av plattform, intern eller mall för registrering, tagg, push notification service identifierare, registrerings-ID och upphör att gälla. Du kan också redigera en registrering på den här sidan. Det är särskilt användbart för att redigera taggar.

Högerklicka på din meddelandehubb i **Server Explorer**, och välj **diagnostisera**. 

![Visual Studio Server Explorer: Diagnostisera menyn](./media/notification-hubs-diagnosing/diagnose-menu.png)

Du ser följande sida:

![Visual Studio: Diagnostisera sidan](./media/notification-hubs-diagnosing/diagnose-page.png)

Växla till den **Enhetsregistreringar** sidan:

![Visual Studio: Registreringar för enhet](./media/notification-hubs-diagnosing/VSRegistrations.png)

Du kan använda **Testsänd** sidan för att skicka ett test-meddelande:

![Visual Studio: Skicka test](./media/notification-hubs-diagnosing/test-send-vs.png)

> [!NOTE]
> Använd Visual Studio om du vill redigera registreringar endast under utveckling och testning, och med ett begränsat antal registreringar. Om du vill redigera din många registreringar samtidigt kan du överväga att använda export och importera registrering-funktioner som beskrivs i [How To: Exportera och ändra många registreringar samtidigt](https://msdn.microsoft.com/library/dn790624.aspx).

#### <a name="service-bus-explorer"></a>Service Bus Explorer ####

Många kunder använder [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) att visa och hantera sina meddelandehubbar. Service Bus Explorer är ett projekt med öppen källkod. 

### <a name="verify-message-notifications"></a>Kontrollera meddelanden

#### <a name="azure-portal"></a>Azure Portal ####

Att skicka ett testmeddelande till dina klienter utan att ha en tjänst serverdel och drift, under **stöd + felsökning**väljer **Testsänd**.

![Testa funktionen för Skicka i Azure][7]

#### <a name="visual-studio"></a>Visual Studio ####

Du kan också skicka testmeddelanden från Visual Studio.

![Testa funktionen för Skicka i Visual Studio][10]

Mer information om att använda Meddelandehubbar med Visual Studio Server Explorer finns i följande artiklar:

* [Så här visar du enhetsregistreringar för meddelandehubbar](https://docs.microsoft.com/previous-versions/windows/apps/dn792122(v=win.10))
* [Djupdykning: Visual Studio 2013 Update 2 RC och Azure SDK 2.3]
* [Vi presenterar versionen av Visual Studio 2013 Update 3 och Azure SDK 2.4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Felsöka misslyckade meddelanden och granska meddelanderesultat

#### <a name="enabletestsend-property"></a>EnableTestSend egenskap ####

När du skickar ett meddelande via Meddelandehubbar i meddelandet först kö. Meddelandehubbar avgör rätt mål och skickar meddelandet till push-meddelandetjänst. Om du använder REST API eller någon av klient-SDK: er, avkastningen på Skicka anropet innebär att endast att meddelandet i kö med Notification Hubs. Det ger inte inblick i vad hände med Notification Hubs så småningom skickas meddelandet till push-meddelandetjänst.

Om ditt meddelande inte har kommit på klientenheten, kan ha inträffat ett fel när Meddelandehubbar försökte förse push-meddelandetjänst. Till exempel nyttolast-storleken kanske överskrider den maximalt tillåtna av push-meddelandetjänsten eller de autentiseringsuppgifter som konfigurerats i Notification Hubs kan vara ogiltig.

Om du vill få information om push notification service fel, kan du använda den [EnableTestSend] egenskapen. Den här egenskapen aktiveras automatiskt när du skickar testmeddelanden från portalen eller Visual Studio-klienten. Du kan använda den här egenskapen om du vill se detaljerad information för felsökning och även via API: er. För närvarande kan du använda den i .NET SDK. Det läggs till alla klient-SDK till slut.

Du använder den `EnableTestSend` egenskap med ett REST-anrop, lägga till en frågesträngsparameter som kallas *testa* i slutet av skicka anrop. Exempel:

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

#### <a name="net-sdk-example"></a>.NET SDK-exempel ####

Här är ett exempel på hur du använder .NET SDK kan skicka ett meddelande med inbyggda popup-fönster (popup):

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

I slutet av körningen `result.State` bara säger `Enqueued`. Resultatet ger inte någon information om vad hände med push-meddelande.

Därefter kan du använda den `EnableTestSend` boolesk egenskap. Använd den `EnableTestSend` egenskapen när du initiera `NotificationHubClient` att hämta detaljerad statusinformation om push notification service fel som uppstår när meddelandet skickas. Skicka anropet tar extra tid att returnera eftersom måste den först Notification Hubs för att leverera meddelandet till push-meddelandetjänst.

```csharp
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }
```

#### <a name="sample-output"></a>Exempel på utdata ####

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

Det här meddelandet anger att de autentiseringsuppgifter som konfigurerats i Notification Hubs är ogiltig eller att det finns ett problem med registreringen i hubben. Ta bort denna registrering och låta klienten återskapa registreringen innan du skickar meddelandet.

> [!NOTE]
> Användning av den `EnableTestSend` egenskapen är kraftigt begränsad. Använd det här alternativet endast i en miljö för utveckling/test och med en begränsad uppsättning registreringar. Felsöka meddelanden skickas till endast 10 enheter. Det finns också en gräns för bearbetning av debug skickar vid 10 per minut.

### <a name="review-telemetry"></a>Granska telemetri ###

#### <a name="azure-portal"></a>Azure Portal ####

I portalen får du en snabb överblick över alla aktiviteter i din meddelandehubb.

1. På den **översikt** fliken visas en sammansatt vy av registreringar, meddelanden och fel efter plattform.

   ![Instrumentpanel med Notification Hubs][5]

2. På den **övervakaren** fliken kan du lägga till många andra plattformsspecifika mått för en djupare inblick. Du kan titta närmare på fel som returneras när Meddelandehubbar försöker skicka meddelandet till push-meddelandetjänst.

   ![Azure-portalen aktivitetsloggen][6]

3. Börja genom att granska **inkommande meddelanden**, **registreringsåtgärder**, och **lyckade meddelanden**. Gå sedan till fliken per plattform för att granska fel som är specifika för push-meddelandetjänst.

4. Om autentiseringsinställningarna för din meddelandehubb är felaktiga, meddelandet **PNS-autentiseringsfel** visas. Det är en bra indikation på att kontrollera autentiseringsuppgifterna för push notification-tjänsten.

#### <a name="programmatic-access"></a>Programmässig åtkomst ####

Läs mer om Programmeringsåtkomst [Programmeringsåtkomst](https://docs.microsoft.com/previous-versions/azure/azure-services/dn458823(v=azure.100)).

> [!NOTE]
> Flera telemetri-relaterade funktioner, som exporterar och importerar registreringar och telemetri åtkomst via API: er, är endast tillgängliga på Standard-tjänstnivå. Om du försöker använda dessa funktioner från kostnadsfritt eller grundläggande tjänstnivån, får du ett Undantagsmeddelande om du använder SDK: N. Du får felmeddelandet HTTP 403 (förbjudet) om du använder funktionerna direkt från REST-API: er.
>
> Om du vill använda telemetri-relaterade funktioner, kontrollera först i Azure portal att du använder Standard-tjänstnivå.  

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/FCMConfigure.png
[3]: ./media/notification-hubs-diagnosing/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png

<!-- LINKS -->
[Översikt över Notification Hubs]: notification-hubs-push-notification-overview.md
[Kom igång med Azure Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Mallar]: https://msdn.microsoft.com/library/dn530748.aspx
[APNs-översikt]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Om FCM-meddelanden]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: https://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[View device registrations for notification hubs]: https://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[Djupdykning: Visual Studio 2013 Update 2 RC och Azure SDK 2.3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Vi presenterar versionen av Visual Studio 2013 Update 3 och Azure SDK 2.4]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Programmatic telemetry access]: https://msdn.microsoft.com/library/azure/dn458823.aspx
