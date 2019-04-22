---
title: Azure Notification Hubs – diagnostisera utelämnade meddelanden
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
ms.openlocfilehash: 4af86025e714c65d0ae225b271a2d0970bb96ee8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59281649"
---
# <a name="azure-notification-hubs---diagnose-dropped-notifications"></a>Azure Notification Hubs – diagnostisera utelämnade meddelanden

En av de vanligaste frågorna från Azure Notification Hubs-kunder är så här felsöker när meddelanden som skickas från ett program inte visas på klientenheter. De vill veta var och varför meddelanden har tagits bort och hur du löser problemet. Den här artikeln beskrivs varför meddelanden kan tas bort eller inte tas emot av enheter. Lär dig hur du analyserar och avgöra den bakomliggande orsaken.

Det är viktigt att först förstå hur tjänsten Meddelandehubbar skickar meddelanden till en enhet.

![Notification Hubs-arkitektur][0]

I en typisk skicka meddelande flow meddelandet har skickats från den *serverdelen program* till Notification Hubs. Meddelandehubbar utför viss bearbetning på alla registreringar. Bearbetningen tar hänsyn till de konfigurerade taggar och Tagguttryck att fastställa ”mål”. Mål är alla registreringar som behöver ta emot push-meddelande. Dessa registreringar kan spänna över några eller alla våra plattformar som stöds: iOS, Google, Windows, Windows Phone, Kindle och Baidu för Android Kina.

Med de mål som upprättats tjänsten Meddelandehubbar skickar meddelanden till den *push-meddelandetjänst* för enhetsplattformen. Exempel är Apple Push Notification service (APNs) för Apple och Firebase Cloud Messaging (FCM) för Google. Notification Hubs push-meddelanden dela upp på flera batchar av registreringar. Meddelandehubbar autentiserar med respektive push meddelandetjänsten baserat på de autentiseringsuppgifter som du anger i Azure-portalen under **konfigurera Meddelandehubben**. Push-meddelande tjänsten sedan vidarebefordrar meddelanden till respektive *klientenheter*.

Den sista delen av leverans av meddelanden äger rum mellan push-meddelandetjänst för plattformen och enheten. Någon av de fyra viktiga komponenterna i processen för push-meddelande (klient, serverdelen program, Notification Hubs och plattformen push notification service) kan orsaka meddelanden tas bort. Mer information om Notification Hubs-arkitekturen finns i [översikt över Notification Hubs].

Det gick inte att leverera meddelanden kan uppstå under inledande testning/mellanlagring fas. Utelämnade meddelanden i det här skedet kan tyda på ett konfigurationsproblem. Om det uppstår fel att leverera meddelanden i produktion, kan vissa eller alla meddelanden släppas. I det här fallet anges ett djupare program eller messaging mönstret problemet.

Nästa avsnitt tittar på scenarier där meddelanden kan släppas, sträcker sig från vanliga till mer sällsynta.

## <a name="notification-hubs-misconfiguration"></a>Notification Hubs misconfiguration

För att kunna skicka meddelanden till respektive push-meddelandetjänst, behöver Notification Hubs-tjänsten autentisera sig själv i samband med utvecklarens program. Utvecklaren skapar ett utvecklarkonto med respektive plattforms (Google, Apple, Windows och så vidare). Sedan registrerar utvecklare sina program med plattformen där de får autentiseringsuppgifter.

Du måste lägga till plattformen autentiseringsuppgifter till Azure-portalen. Om inga meddelanden ansluter till enheten, vara det första steget för att säkerställa att rätt autentiseringsuppgifter har konfigurerats i Notification Hubs. Autentiseringsuppgifterna måste matcha det program som har skapats under ett plattformsspecifika developer-konto.

Stegvisa instruktioner för att slutföra den här processen, se [Kom igång med Azure Notification Hubs].

Här följer några vanliga felkonfigureringar att söka efter:

**Allmänt**

Se till att ditt meddelandehubbsnamn (utan skrivfel) är samma i var och en av de här platserna:
   * Registrerar från klienten.
   * Där kan du skicka meddelanden från backend-servern.
   * Där du konfigurerade push notification service-autentiseringsuppgifter.

Kontrollera att du använder strängar för signaturer konfiguration för rätt delad åtkomst på klienten och serverdelen program. I allmänhet måste du använda **DefaultListenSharedAccessSignature** på klienten och **DefaultFullSharedAccessSignature** på programmet serverdel (ger behörighet att skicka meddelanden till Meddelandehubbar).

**APN-konfiguration**

Du måste ha två olika hubs: en hubb för produktion och en annan hub för att testa. Det innebär att du måste överföra det certifikat som du använder i en testmiljö till en separat hubb än certifikatet och den hubben som du ska använda i produktion. Försök inte att överföra olika typer av certifikat till samma hubb. Detta kan orsaka fel i meddelande.

Om du av misstag överföra olika typer av certifikat till samma hubb, rekommenderar vi att du ta bort hubben och starta på nytt med en ny hubb. Om du inte kan radera hubben, som ett minimum av någon anledning måste du ta bort alla befintliga registreringar från hubben.

**FCM-konfiguration**

1. Se till att den *servernyckel* som du har skaffat från Firebase matchar den servernyckel som du registrerade i Azure-portalen.

   ![Firebase servernyckel][3]

2. Kontrollera att du har konfigurerat **projekt-ID** på klienten. Du kan hämta värdet för **projekt-ID** från Firebase-instrumentpanelen.

   ![Firebase-projekt-ID][1]

## <a name="application-issues"></a>Programfel

**Taggar och Tagguttryck**

Om du använder taggar eller Tagguttryck för att segmentera målgruppen, är det möjligt att när du skickar meddelandet, inget mål hittades baserat på taggar eller Tagguttryck som du anger i Skicka anrop.

Granska dina registreringar för att säkerställa att det inte finns matchande taggar när du skickar ett meddelande. Kontrollera leveranskvitto endast från klienter som har dessa registreringar.

Till exempel om alla registreringar med Notification Hubs gjordes med taggen ”politik” och du kan skicka ett meddelande med taggen ”sport” skickas meddelandet inte till alla enheter. Komplexa fallet kan handla om Tagguttryck där du har registrerat med hjälp av ”tagg A” eller ”tagg B”, men när du skickar meddelanden kan du rikta ”tagg A & & taggen B.” I avsnittet självdiagnos tips senare i artikeln visar vi hur du granskar din registreringar och deras taggar.

**Problem med mallar**

Om du använder mallar kan du se till att du följer de riktlinjer som beskrivs i [mallar].

**Ogiltig registreringar**

Om meddelandehubben har konfigurerats korrekt, och om alla taggar eller Tagguttryck har använts på rätt sätt, finns giltigt mål. Meddelanden ska skickas till dessa mål. Tjänsten Meddelandehubbar utlöses sedan av flera bearbetning batchar parallellt. Varje grupp som skickar meddelanden till en uppsättning registreringar.

> [!NOTE]
> Eftersom bearbetning utförs parallellt, garanteras inte den ordning i vilken meddelanden levereras.

Meddelandehubbar är optimerad för en leveransmodell för ”en gång i de flesta”-meddelande. Vi försöka deduplicering, så att inga meddelanden levereras mer än en gång till en enhet. För att säkerställa att detta vi Kontrollera registreringar och se till att endast en meddelandet skickas per enhetsidentifierare innan meddelandet skickas till push-meddelandetjänst.

Eftersom varje batch som skickas till tjänsten push-meddelande, som i sin tur är att acceptera och validera registreringarna, är det möjligt att push-meddelandetjänst identifierar ett fel med en eller flera av registreringarna i en batch. I det här fallet push-meddelandetjänst returnerar ett fel till Notification Hubs och processen stoppas. Push-meddelandetjänst utelämnar den batch helt. Detta gäller särskilt med APNS som använder en stream TCP-protokollet.

Vi är optimerade för i de flesta en leverans. Men i det här fallet Åtkomstöverträdelse registreringen tas bort från databasen. Sedan gör vi om leverans av meddelanden för resten av enheter i den batch.

Om du vill ha mer felinformation om den misslyckade leveransförsök mot en registrering kan du använda Notification Hubs REST-API: er [telemetri Per meddelande: Hämta telemetri för meddelande meddelande](https://msdn.microsoft.com/library/azure/mt608135.aspx) och [PNS feedback](https://msdn.microsoft.com/library/azure/mt705560.aspx). Exempelkoden finns på [skicka REST-exempel](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample).

## <a name="push-notification-service-issues"></a>Push-meddelande tjänstproblem

När meddelandet har tagits emot av push-meddelandetjänst för plattformen, är det ansvar för push-meddelandetjänst för att leverera meddelandet till enheten. Nu kan tjänsten Meddelandehubbar ligger utanför bilden och har ingen kontroll över när eller om meddelandet skickas till enheten.

Eftersom plattformarnas meddelandetjänster är robust, brukar meddelanden att nå enheter från push-meddelandetjänst på några sekunder. Om push-meddelandetjänst begränsar gäller tjänsten Meddelandehubbar en exponentiell backoff-strategi. Om push-meddelandetjänst är inte kan nås under 30 minuter, har vi en princip för att upphöra att gälla och släpp meddelanden permanent.

Om en push-meddelandetjänst försöker leverera ett meddelande, men enheten är offline, lagras meddelandet av push-meddelandetjänst för en begränsad tidsperiod. Meddelandet skickas till enheten när enheten blir tillgänglig.

Endast en senaste meddelande lagras för varje app. Om flera meddelanden skickas när en enhet är offline, gör varje nytt meddelande tidigare meddelandet om du vill att tas bort. Att hålla endast senaste meddelandet kallas *buffertsammanslagning meddelanden* i APN, och *komprimera* i FCM (som använder en komprimera nyckel). Om enheten är offline under en längre tid, ignoreras alla meddelanden som sparades för enheten. Mer information finns i [APN översikt] och [Om FCM-meddelanden].

Med Azure Notification Hubs skickar du en buffertsammanslagning nyckel via ett HTTP-huvud med hjälp av generiska SendNotification-API. Till exempel för .NET-SDK använder du `SendNotificationAsync`. API: et SendNotification tar också HTTP-huvuden som skickas som – är att respektive push-meddelandetjänst.

## <a name="self-diagnosis-tips"></a>Tips för självdiagnos

Här följer sökvägar för att diagnostisera grundorsaken till utelämnade meddelanden i Meddelandehubbar.

### <a name="verify-credentials"></a>Verifiera autentiseringsuppgifter

**Push notification service developer-portalen**

Kontrollera autentiseringsuppgifterna i respektive push notification service developer-portalen (APNs, FCM, Windows-meddelandetjänsten och så vidare). Mer information finns i [Kom igång med Azure Notification Hubs].

**Azure Portal**

Om du vill granska och överensstämma med autentiseringsuppgifterna med de som du fick från push notification service developer-portalen, Azure-portalen går du till den **åtkomstprinciper** fliken.

![Azure-portalen åtkomstprinciper][4]

### <a name="verify-registrations"></a>Kontrollera registreringarna

**Visual Studio**

Om du använder Visual Studio för utveckling, kan du ansluta till Azure via Server Explorer för att visa och hantera flera Azure-tjänster, inklusive Notification Hubs. Detta är främst användbart för din miljö för utveckling och testning.

![Visual Studio Server Explorer][9]

Du kan visa och hantera alla registreringar i din hubb, kategoriserade efter plattform, intern eller registrering av mallen, några taggar, push notification service identifierare, registrerings-ID och upphör att gälla. Du kan också redigera en registrering på den här sidan. Det är särskilt användbart för att redigera taggar.

Högerklicka på din **meddelandehubb** i den **Server Explorer**, och välj **diagnostisera**. 

![Visual Studio - Server Explorer - diagnostisera menyn](./media/notification-hubs-diagnosing/diagnose-menu.png)

Du ser följande sida: 

![Visual Studio – diagnostisera sidan](./media/notification-hubs-diagnosing/diagnose-page.png)

Växla till den **Enhetsregistreringar** sidan: 

![Visual Studio Device Registrations](./media/notification-hubs-diagnosing/VSRegistrations.png)

Du kan använda **Testsänd** sidan för att skicka ett test-meddelande:

![Visual Studio – prova att skicka](./media/notification-hubs-diagnosing/test-send-vs.png)

> [!NOTE]
> Använd Visual Studio om du vill redigera registreringar endast under utveckling och testning, och med ett begränsat antal registreringar. Om du vill redigera din många registreringar samtidigt kan du överväga att använda export och importera registrering-funktioner som beskrivs i [exportera och ändra många registreringar samtidigt](https://msdn.microsoft.com/library/dn790624.aspx).

**Service Bus Explorer**

Många kunder använder [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) att visa och hantera sina meddelandehubben. Service Bus Explorer är ett projekt med öppen källkod. 

### <a name="verify-message-notifications"></a>Kontrollera meddelanden

**Azure Portal**

Att skicka ett testmeddelande till dina klienter utan att ha en tjänst serverdel och drift, under **stöd + felsökning**väljer **Testsänd**.

![Testa funktionen för Skicka i Azure][7]

**Visual Studio**

Du kan också skicka testmeddelanden från Visual Studio.

![Testa funktionen för Skicka i Visual Studio][10]

Mer information om att använda Meddelandehubbar med Visual Studio Server Explorer finns i följande artiklar:

* [Visa enhetsregistreringar för meddelandehubbar]
* [Djupdykning: Visual Studio 2013 Update 2 RC och Azure SDK 2.3]
* [Vi presenterar versionen av Visual Studio 2013 Update 3 och Azure SDK 2.4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Felsöka misslyckade meddelanden och granska meddelanderesultat

**EnableTestSend egenskap**

När du skickar ett meddelande via Meddelandehubbar, inledningsvis är meddelandet i kö för bearbetning i Notification Hubs. Meddelandehubbar avgör rätt mål och skickar meddelandet till push-meddelandetjänst. Om du använder REST API eller någon av klient-SDK: er innebär skicka anropet lyckas returneras endast att meddelandet har placerats i kö med Notification Hubs. Du har inte någon inblick i vad hände med Notification Hubs så småningom skickas meddelandet till push-meddelandetjänst.

Om din meddelandet inte kommer fram på klientenheten, är det möjligt att ett fel uppstod vid ett försök gjordes att leverera meddelandet till push-meddelandetjänst Meddelandehubbar. Till exempel nyttolast-storleken kanske överskrider den maximalt tillåtna av push-meddelandetjänsten eller de autentiseringsuppgifter som konfigurerats i Notification Hubs kan vara ogiltig.

Om du vill få information om push notification service fel, kan du använda den [EnableTestSend] egenskapen. Den här egenskapen aktiveras automatiskt när du skickar testmeddelanden från portalen eller Visual Studio-klienten. Du kan använda den här egenskapen om du vill se detaljerad information för felsökning. Du kan också använda egenskapen via API: er. För närvarande kan du använda den i .NET SDK. Slutligen läggs den till alla klient-SDK.

Du använder den `EnableTestSend` egenskap med ett REST-anrop, lägga till en frågesträngsparameter som kallas *testa* i slutet av skicka anrop. Exempel:

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

**Exempel (.NET SDK)**

Här är ett exempel på hur du använder .NET SDK kan skicka ett meddelande med inbyggda popup-fönster (popup):

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

I slutet av körningen `result.State` bara säger `Enqueued`. Resultatet ger inte någon information om vad hände med push-meddelande.

Därefter kan du använda den `EnableTestSend` boolesk egenskap. Använd den `EnableTestSend` egenskapen när du initiera `NotificationHubClient` att hämta detaljerad statusinformation om push notification service fel som uppstår när meddelandet skickas. Skicka anropet tar extra tid att returnera eftersom förrän Meddelandehubbar har levereras meddelandet till push-meddelandetjänst för att fastställa resultatet returneras.

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

**Exempel på utdata**

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

Det här meddelandet innebär att ogiltiga autentiseringsuppgifter har konfigurerats i Notification Hubs eller att det finns ett problem med registreringen i hubben. Vi rekommenderar att du tar bort denna registrering och låta klienten återskapa registreringen innan du skickar meddelandet.

> [!NOTE]
> Användning av den `EnableTestSend` egenskapen är kraftigt begränsad. Använd det här alternativet endast i en miljö för utveckling/testning, och med en begränsad uppsättning registreringar. Vi kan skicka felsökningsaviseringar till endast 10 enheter. Vi har också en gräns för bearbetning av debug skickar till 10 per minut.

### <a name="review-telemetry"></a>Granska telemetri

**Azure Portal**

I portalen får du en snabb överblick över alla aktiviteter i din meddelandehubb.

1. På den **översikt** fliken visas en sammansatt vy av registreringar, meddelanden och fel efter plattform.

   ![Instrumentpanel med Notification Hubs][5]

2. På den **övervakaren** fliken kan du lägga till många andra plattformsspecifika mått för en djupare inblick. Du kan titta närmare på eventuella fel som rör push-meddelandetjänst som returneras när Notification Hubs-tjänsten försöker skicka meddelandet till push-meddelandetjänst.

   ![Azure-portalen aktivitetsloggen][6]

3. Börja genom att granska **inkommande meddelanden**, **registreringsåtgärder**, och **lyckade meddelanden**. Gå sedan till fliken per plattform för att granska fel som är specifika för push-meddelandetjänst.

4. Om autentiseringsinställningarna för din meddelandehubb är felaktiga, meddelandet **PNS-autentiseringsfel** visas. Det är en bra indikation på att kontrollera autentiseringsuppgifterna för push notification-tjänsten.

**Programmässig åtkomst**

Läs mer om Programmeringsåtkomst [Telemetri för programmässig åtkomst].

> [!NOTE]
> Flera telemetri-relaterade funktioner, som exporterar och importerar registreringar och telemetri åtkomst via API: er, är endast tillgängliga på Standard-tjänstnivå. Om du försöker använda dessa funktioner från kostnadsfritt eller Basic-tjänstnivån, meddelande om ett undantag om du använder SDK: N och felmeddelandet HTTP 403 (förbjudet) om du använder funktionerna direkt från REST-API: er.
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
[APNs overview]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Om FCM-meddelanden]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: https://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[Visa enhetsregistreringar för meddelandehubbar]: https://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[Djupdykning: Visual Studio 2013 Update 2 RC och Azure SDK 2.3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Vi presenterar versionen av Visual Studio 2013 Update 3 och Azure SDK 2.4]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Telemetri för programmässig åtkomst]: https://msdn.microsoft.com/library/azure/dn458823.aspx
