---
title: Mått i Azure Monitor Azure-Event Hubs | Microsoft Docs
description: Den här artikeln innehåller information om hur du använder Azure-övervakning för att övervaka Azure Event Hubs
services: event-hubs
documentationcenter: .NET
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 09/18/2019
ms.author: shvija
ms.openlocfilehash: 96c346f4359740fda5638dfdbe5735c5bdfce8c9
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162658"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Azure Event Hubs mått i Azure Monitor

Event Hubs mått ger dig tillstånd att Event Hubs resurser i din Azure-prenumeration. Med en omfattande uppsättning mätvärden, kan du utvärdera den övergripande hälsan för händelsehubbar inte bara på namnområdesnivå, utan även på enhetsnivå. Statistiken kan vara viktigt eftersom de hjälper dig att övervaka status för dina event hubs. Mått kan också hjälpa till att felsöka problem med grundorsaken utan att behöva kontakta Azure-supporten.

Azure Monitor innehåller enhetligt användargränssnitt för övervakning över olika Azure-tjänster. Mer information finns i [övervakning i Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) och [Hämta Azure Monitor mått med .net](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) -exempel på GitHub.

## <a name="access-metrics"></a>Åtkomst-mått

Azure Monitor innehåller flera sätt att åtkomst mått. Du kan antingen komma åt mått via [Azure Portal](https://portal.azure.com)eller använda Azure Monitor-API: er (rest och .net) och analys lösningar som Log Analytics och Event Hubs. Mer information finns i [övervaka data som samlas in av Azure Monitor](../azure-monitor/platform/data-platform.md).

Mått är aktiverade som standard och du kan komma åt de senaste 30 dagarna data. Om du vill behålla data under en längre tid kan du arkivera måttdata till ett Azure Storage-konto. Detta konfigureras i [diagnostikinställningar](../azure-monitor/platform/diagnostic-settings.md) i Azure Monitor.


## <a name="access-metrics-in-the-portal"></a>Åtkomst-mått i portal

Du kan övervaka mått över tid i [Azure Portal](https://portal.azure.com). I följande exempel visas hur du visar lyckade begäranden och inkommande begäranden på kontonivå:

![Visa lyckad mått][1]

Du kan också komma åt mått direkt via namnområdet. Det gör du genom att välja ditt namn område och sedan klicka på **mått**. Om du vill visa mått som filtrerats till händelse hubbens omfattning väljer du händelsehubben och klickar sedan på **mått**.

För mätvärden som stöd för dimensioner, måste du filtrera med önskad dimension-värde som visas i följande exempel:

![Filtrera med dimensionsvärde][2]

## <a name="billing"></a>Fakturering

Användning av mått i Azure Monitor är för närvarande kostnads fritt. Om du använder ytterligare lösningar som mata in måttdata kan debiteras du dock av dessa lösningar. Exempelvis debiteras du per Azure Storage om du arkiverar måttdata till ett Azure Storage-konto. Du debiteras också av Azure om du strömmar mått data till Azure Monitor loggar för avancerad analys.

Följande mått ger dig en översikt över hälsotillståndet för din tjänst. 

> [!NOTE]
> Vi avvecklar flera mått när de flyttas under ett annat namn. Detta kräver att du uppdaterar dina referenser. Mått som markerats med nyckelordet ”inaktuell” stöds inte framöver.

Alla mått-värden skickas till Azure Monitor varje minut. Tidskornighet definierar tidsintervallet som mått visas. Tidsintervallet som stöds för alla Event Hubs-mått är 1 minut.

## <a name="request-metrics"></a>Begäran-mått

Räknar antalet förfrågningar om data och hantering av åtgärder.

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| Inkommande begär Anden  | Antal begäranden som görs till Azure Event Hubs-tjänsten under en angiven period. <br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName |
| Lyckade förfrågningar    | Antal lyckade begäranden som görs till Azure Event Hubs-tjänsten under en angiven period. <br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName |
| Server fel  | Antal begäranden som inte bearbetas på grund av ett fel i tjänsten Azure Event Hubs under en angiven period. <br/><br/>Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName |
|Användar fel |Antal begäranden som inte bearbetas på grund av användarfel under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|Överskriden kvot fel |Antalet begäranden överskred tillgänglig kvot. I [den här artikeln](event-hubs-quotas.md) finns mer information om Event Hubs kvoter.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|

## <a name="throughput-metrics"></a>Mått för dataflöde

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|Begränsade begär Anden |Antal begäranden som har begränsats eftersom enhet datadataflödesanvändning överskreds.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|

## <a name="message-metrics"></a>Meddelande-mått

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|Inkommande meddelanden |Antal händelser eller meddelanden som skickas till Event Hubs under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|Utgående meddelanden |Antal händelser eller meddelanden hämtas från Event Hubs under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|Inkommande byte |Antal byte som skickas till Azure Event Hubs-tjänsten under en angiven period.<br/><br/> Enhet: byte <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|Utgående byte |Antalet byte som hämtas från Azure Event Hubs-tjänsten under en angiven period.<br/><br/> Enhet: byte <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|

## <a name="connection-metrics"></a>Anslutningsmått

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|ActiveConnections |Antal aktiva anslutningar för ett namnområde och på en entitet.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|Öppna anslutningar |Antal öppna anslutningar.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|Stängda anslutningar |Antal stängda anslutningar.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|

## <a name="event-hubs-capture-metrics"></a>Event Hubs Capture mått

Du kan övervaka Event Hubs Capture mått när du aktiverar avbildningsfunktionen för händelsehubbar. Följande mått beskrivs vad du kan övervaka med avbildningsfunktionen aktiverad.

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|Samla in efter släpning |Antal byte som ännu samlas in till det valda målet.<br/><br/> Enhet: byte <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|Fångade meddelanden |Antal meddelanden eller händelser som har hämtats till det valda målet under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|Hämtade byte |Antal byte som har hämtats till det valda målet under en angiven period.<br/><br/> Enhet: byte <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|

## <a name="metrics-dimensions"></a>Mått dimensioner

Azure Event Hubs stöder följande dimensioner för mått i Azure Monitor. Det är valfritt att lägga till dimensioner i dina mått. Om du inte lägger till dimensioner, har mått angetts på namnområdesnivå. 

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|EntityName| Händelsehubbar har stöd för event hub-enheter i namnområdet.|

## <a name="azure-monitor-integration-with-siem-tools"></a>Azure Monitor integrering med SIEM-verktyg
Routning av övervaknings data (aktivitets loggar, diagnostikloggar osv.) till en Event Hub med Azure Monitor gör att du enkelt kan integrera med verktyg för säkerhets informations-och händelse hantering (SIEM). Mer information finns i följande artiklar/blogg inlägg:

- [Strömma Azure övervaknings data till en Event Hub för användning av ett externt verktyg](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)
- [Introduktion till Azure Log Integration](../security/fundamentals/azure-log-integration-overview.md)
- [Använda Azure Monitor för att integrera med SIEM-verktyg](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

I scenariot där ett SIEM-verktyg använder loggdata från en Event Hub, om du inte ser några inkommande meddelanden eller om du ser inkommande meddelanden, men inte har några utgående meddelanden i mått diagrammet, följer du dessa steg:

- Om det inte finns **några inkommande meddelanden**innebär det att tjänsten Azure Monitor inte flyttar gransknings-/diagnostikloggar till händelsehubben. Öppna ett support ärende med Azure Monitor-teamet i det här scenariot. 
- om det finns inkommande meddelanden, men **inga utgående meddelanden**, innebär det att Siem-programmet inte läser meddelandena. Kontakta SIEM-providern för att avgöra om konfigurationen av Event Hub som programmen är korrekt.


## <a name="next-steps"></a>Nästa steg

* Se [Översikt över Azure Monitoring](../monitoring-and-diagnostics/monitoring-overview.md).
* [Hämta Azure Monitor mått med .net](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) -exempel på GitHub. 

Besök följande länkar för mer utförlig information om Event Hubs:

- Kom igång med en Event Hubs själv studie kurs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
* [Exempelprogram som använder Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png



