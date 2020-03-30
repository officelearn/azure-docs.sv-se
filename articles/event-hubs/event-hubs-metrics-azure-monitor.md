---
title: Mått i Azure Monitor – Azure-händelsehubbar | Microsoft-dokument
description: Den här artikeln innehåller information om hur du använder Azure Monitoring för att övervaka Azure Event Hubs
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162658"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Azure Event Hubs-mått i Azure Monitor

Mått på eventhubbar ger dig tillståndet för eventhubbar resurser i din Azure-prenumeration. Med en omfattande uppsättning måttdata kan du bedöma den övergripande hälsan för dina händelsehubbar inte bara på namnområdesnivå utan även på entitetsnivå. Den här statistiken kan vara viktig eftersom de hjälper dig att övervaka tillståndet för dina händelsehubbar. Mått kan också hjälpa till att felsöka problem med rotorsaken utan att behöva kontakta Azure-supporten.

Azure Monitor tillhandahåller enhetliga användargränssnitt för övervakning av olika Azure-tjänster. Mer information finns [i Övervakning i Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) och hämta Azure [Monitor-mått med .NET-exempel](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) på GitHub.

## <a name="access-metrics"></a>Åtkomstmått

Azure Monitor innehåller flera sätt att komma åt mått. Du kan antingen komma åt mått via [Azure-portalen](https://portal.azure.com)eller använda AZURE Monitor API:er (REST och .NET) och analyslösningar som Logganalys och eventhubbar. Mer information finns i [Övervaka data som samlas in av Azure Monitor](../azure-monitor/platform/data-platform.md).

Mått är aktiverade som standard och du kan komma åt de senaste 30 dagarnas data. Om du behöver behålla data under en längre tid kan du arkivera måttdata till ett Azure Storage-konto. Detta är konfigurerat i [diagnostikinställningar](../azure-monitor/platform/diagnostic-settings.md) i Azure Monitor.


## <a name="access-metrics-in-the-portal"></a>Åtkomstmått i portalen

Du kan övervaka mått över tid i [Azure-portalen](https://portal.azure.com). I följande exempel visas hur du visar lyckade begäranden och inkommande begäranden på kontonivå:

![Visa lyckade mått][1]

Du kan också komma åt mått direkt via namnområdet. Om du vill göra det markerar du namnområdet och klickar sedan på **Mått**. Om du vill visa mått som filtrerats till händelsehubbens omfattning markerar du händelsehubben och klickar sedan på **Mått**.

För mått som stöder dimensioner måste du filtrera med önskat dimensionsvärde som visas i följande exempel:

![Filtrera med dimensionsvärde][2]

## <a name="billing"></a>Fakturering

Det är för närvarande gratis att använda mått i Azure Monitor. Men om du använder ytterligare lösningar som ger svarmetridata kan du faktureras av dessa lösningar. Du debiteras till exempel av Azure Storage om du arkiverar måttdata till ett Azure Storage-konto. Du debiteras också av Azure om du strömmar måttdata till Azure Monitor-loggar för avancerad analys.

Följande mått ger dig en översikt över hälsotillståndet för din tjänst. 

> [!NOTE]
> Vi inaktuella flera mått när de flyttas under ett annat namn. Detta kan kräva att du uppdaterar dina referenser. Mått som markerats med nyckelordet "föråldrad" stöds inte framöver.

Alla måttvärden skickas till Azure Monitor varje minut. Tidsgranulariteten definierar det tidsintervall för vilket måttvärden presenteras. Tidsintervallet som stöds för alla mått för händelsehubbar är 1 minut.

## <a name="request-metrics"></a>Begär mått

Räknar antalet begäranden om data- och hanteringsåtgärder.

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| Inkommande begäranden  | Antalet begäranden som görs till Azure Event Hubs-tjänsten under en angiven period. <br/><br/> Enhet: Antal <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName |
| Lyckade förfrågningar    | Antalet lyckade begäranden som gjorts till Azure Event Hubs-tjänsten under en angiven period. <br/><br/> Enhet: Antal <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName |
| Serverfel  | Antalet begäranden som inte bearbetas på grund av ett fel i Azure Event Hubs-tjänsten under en angiven period. <br/><br/>Enhet: Antal <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName |
|Användarfel |Antalet begäranden som inte har bearbetats på grund av användarfel under en angiven period.<br/><br/> Enhet: Antal <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|
|Kvot överskred fel |Antalet begäranden överskred den tillgängliga kvoten. Mer information om kvoter för eventhubbar finns i [den här artikeln.](event-hubs-quotas.md)<br/><br/> Enhet: Antal <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|

## <a name="throughput-metrics"></a>Mätvärden för dataflöde

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|Begränsade begäranden |antalet begäranden som har begränsats på grund av att användningen av dataflödesenheten överskreds.<br/><br/> Enhet: Antal <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|

## <a name="message-metrics"></a>Mått på meddelanden

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|Inkommande meddelanden |Antalet händelser eller meddelanden som skickas till eventhubbar under en angiven period.<br/><br/> Enhet: Antal <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|
|Utgående meddelanden |Antalet händelser eller meddelanden som hämtats från eventhubbar under en angiven period.<br/><br/> Enhet: Antal <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|
|Inkommande byte |Antalet byte som skickas till Azure Event Hubs-tjänsten under en angiven period.<br/><br/> Enhet: Byte <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|
|Utgående byte |Antalet byte som hämtats från Azure Event Hubs-tjänsten under en angiven period.<br/><br/> Enhet: Byte <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|

## <a name="connection-metrics"></a>Anslutningsmått

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|ActiveConnections (Aktiva Anslutning) |Antalet aktiva anslutningar på ett namnområde och på en entitet.<br/><br/> Enhet: Antal <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|
|Anslutningar öppnade |Antalet öppna anslutningar.<br/><br/> Enhet: Antal <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|
|Anslutningar stängda |Antalet stängda anslutningar.<br/><br/> Enhet: Antal <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|

## <a name="event-hubs-capture-metrics"></a>Mätvärden för infångstmått för händelsehubbar

Du kan övervaka mätvärden för insamling av händelsehubbar när du aktiverar hämtningsfunktionen för dina händelsehubbar. Följande mått beskriver vad du kan övervaka med Capture aktiverat.

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|Fånga eftersläpning |Antalet byte som ännu inte har hämtats till det valda målet.<br/><br/> Enhet: Byte <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|
|Fångade meddelanden |Antalet meddelanden eller händelser som fångas in till det valda målet under en angiven period.<br/><br/> Enhet: Antal <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|
|Fångade byte |Antalet byte som fångas in till det valda målet under en angiven period.<br/><br/> Enhet: Byte <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|

## <a name="metrics-dimensions"></a>Måttdimensioner

Azure Event Hubs stöder följande dimensioner för mått i Azure Monitor. Det är valfritt att lägga till dimensioner i måtten. Om du inte lägger till dimensioner anges mått på namnområdesnivå. 

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|Entityname| Event Hubs stöder händelsenaventiteterna under namnområdet.|

## <a name="azure-monitor-integration-with-siem-tools"></a>Azure Monitor-integrering med SIEM-verktyg
Routning av dina övervakningsdata (aktivitetsloggar, diagnostikloggar osv.) till en händelsehubb med Azure Monitor gör att du enkelt kan integrera med SIEM-verktyg (Security Information and Event Management). Mer information finns i följande artiklar/blogginlägg:

- [Strömma Azure-övervakningsdata till en händelsehubb för förbrukning av ett externt verktyg](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)
- [Introduktion till Azure Log Integration](../security/fundamentals/azure-log-integration-overview.md)
- [Använda Azure Monitor för att integrera med SIEM-verktyg](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

I det scenario där ett SIEM-verktyg använder loggdata från en händelsenav, om du inte ser några inkommande meddelanden eller om du ser inkommande meddelanden men inga utgående meddelanden i måttdiagrammet, gör du så här:

- Om det inte finns **några inkommande meddelanden**betyder det att Azure Monitor-tjänsten inte flyttar gransknings-/diagnostikloggar till händelsehubben. Öppna en supportbiljett med Azure Monitor-teamet i det här scenariot. 
- Om det finns inkommande meddelanden, men **inga utgående meddelanden,** betyder det att SIEM-programmet inte läser meddelandena. Kontakta SIEM-providern för att ta reda på om konfigurationen av händelsehubben dessa program är korrekta.


## <a name="next-steps"></a>Nästa steg

* Se [översikten över Azure-övervakning](../monitoring-and-diagnostics/monitoring-overview.md).
* [Hämta Azure Monitor-mått med .NET-exempel](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) på GitHub. 

Besök följande länkar för mer utförlig information om Event Hubs:

- Kom igång med en kurs om händelsehubbar
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [Javascript](get-started-java-send-v2.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
* [Exempelprogram som använder Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png



