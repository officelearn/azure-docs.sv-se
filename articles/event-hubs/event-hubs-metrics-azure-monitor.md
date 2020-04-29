---
title: Mått i Azure Monitor Azure-Event Hubs | Microsoft Docs
description: Den här artikeln innehåller information om hur du använder Azure Monitor för att övervaka Azure Event Hubs
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77162658"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Azure Event Hubs-mått i Azure Monitor

Event Hubs mått ger dig tillstånd att Event Hubs resurser i din Azure-prenumeration. Med en omfattande uppsättning Mät data kan du utvärdera den övergripande hälsan för dina händelse nav inte bara på namn områdes nivån, utan även på enhets nivå. Den här statistiken kan vara viktig när de hjälper dig att övervaka status för dina Event Hub. Mått kan också hjälpa till att felsöka rotor Saks problem utan att behöva kontakta Azure-supporten.

Azure Monitor tillhandahåller enhetliga användar gränssnitt för övervakning i olika Azure-tjänster. Mer information finns i [övervakning i Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) och [Hämta Azure Monitor mått med .net](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) -exempel på GitHub.

## <a name="access-metrics"></a>Åtkomst mått

Azure Monitor ger till gång till mått på flera sätt. Du kan antingen komma åt mått via [Azure Portal](https://portal.azure.com)eller använda Azure Monitor-API: er (rest och .net) och analys lösningar som Log Analytics och Event Hubs. Mer information finns i [övervaka data som samlas in av Azure Monitor](../azure-monitor/platform/data-platform.md).

Mått är aktiverade som standard och du kan komma åt de senaste 30 dagarna med data. Om du behöver spara data under en längre tids period kan du arkivera mått data till ett Azure Storage konto. Detta konfigureras i [diagnostikinställningar](../azure-monitor/platform/diagnostic-settings.md) i Azure Monitor.


## <a name="access-metrics-in-the-portal"></a>Åtkomst mått i portalen

Du kan övervaka mått över tid i [Azure Portal](https://portal.azure.com). I följande exempel visas hur du visar lyckade förfrågningar och inkommande begär Anden på konto nivå:

![Visa lyckade mått][1]

Du kan också komma åt mått direkt via namn området. Det gör du genom att välja ditt namn område och sedan klicka på **mått**. Om du vill visa mått som filtrerats till händelse hubbens omfattning väljer du händelsehubben och klickar sedan på **mått**.

För mått som stöder dimensioner måste du filtrera med det önskade dimension svärdet som visas i följande exempel:

![Filtrera med dimensions värde][2]

## <a name="billing"></a>Fakturering

Användning av mått i Azure Monitor är för närvarande kostnads fritt. Men om du använder ytterligare lösningar som inhämtar mått data kan du debiteras av dessa lösningar. Till exempel debiteras du per Azure Storage om du arkiverar mått data till ett Azure Storage konto. Du debiteras också av Azure om du strömmar mått data till Azure Monitor loggar för avancerad analys.

Följande mått ger en översikt över hälso tillståndet för din tjänst. 

> [!NOTE]
> Vi har föråldrat flera mått när de flyttas under ett annat namn. Detta kan kräva att du uppdaterar dina referenser. Mått som har marker ATS med nyckelordet "inaktuell" kommer inte att stödjas framåt.

Alla mått värden skickas till Azure Monitor varje minut. Tids kornig het definierar tidsintervallet för vilka mått värden presenteras. Det tidsintervall som stöds för alla Event Hubs mått är 1 minut.

## <a name="request-metrics"></a>Begär mått

Räknar antalet data-och hanterings åtgärder som begärs.

| Mått namn | Beskrivning |
| ------------------- | ----------------- |
| Inkommande begär Anden  | Antalet förfrågningar som gjorts till Azure Event Hubs-tjänsten under en angiven period. <br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Dimension: EntityName |
| Lyckade förfrågningar    | Antalet lyckade förfrågningar som gjorts till Azure Event Hubs-tjänsten under en angiven period. <br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Dimension: EntityName |
| Server fel  | Antalet begär Anden som inte behandlats på grund av ett fel i Azure Event Hubs-tjänsten under en angiven period. <br/><br/>Enhet: antal <br/> Sammansättnings typ: totalt <br/> Dimension: EntityName |
|Användar fel |Antalet begär Anden som inte behandlats på grund av användar fel under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Dimension: EntityName|
|Överskriden kvot fel |Antalet begär Anden överskred den tillgängliga kvoten. I [den här artikeln](event-hubs-quotas.md) finns mer information om Event Hubs kvoter.<br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Dimension: EntityName|

## <a name="throughput-metrics"></a>Data flödes mått

| Mått namn | Beskrivning |
| ------------------- | ----------------- |
|Begränsade begär Anden |antalet begäranden som har begränsats på grund av att användningen av dataflödesenheten överskreds.<br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Dimension: EntityName|

## <a name="message-metrics"></a>Meddelande mått

| Mått namn | Beskrivning |
| ------------------- | ----------------- |
|Inkommande meddelanden |Antalet händelser eller meddelanden som skickats till Event Hubs under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Dimension: EntityName|
|Utgående meddelanden |Antalet händelser eller meddelanden som hämtats från Event Hubs under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Dimension: EntityName|
|Inkommande byte |Antalet byte som skickats till Azure Event Hubs-tjänsten under en angiven period.<br/><br/> Enhet: byte <br/> Sammansättnings typ: totalt <br/> Dimension: EntityName|
|Utgående byte |Antalet byte som hämtats från Azure Event Hubs-tjänsten under en angiven period.<br/><br/> Enhet: byte <br/> Sammansättnings typ: totalt <br/> Dimension: EntityName|

## <a name="connection-metrics"></a>Anslutnings mått

| Mått namn | Beskrivning |
| ------------------- | ----------------- |
|ActiveConnections |Antalet aktiva anslutningar i ett namn område samt på en entitet.<br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Dimension: EntityName|
|Öppna anslutningar |Antalet öppna anslutningar.<br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Dimension: EntityName|
|Stängda anslutningar |Antalet stängda anslutningar.<br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Dimension: EntityName|

## <a name="event-hubs-capture-metrics"></a>Event Hubs Infångnings mått

Du kan övervaka Event Hubs Infångnings mått när du aktiverar insamlings funktionen för dina Event Hub. Följande mått beskriver vad du kan övervaka med Capture aktiverat.

| Mått namn | Beskrivning |
| ------------------- | ----------------- |
|Samla in efter släpning |Antalet byte som ännu inte har registrerats till det valda målet.<br/><br/> Enhet: byte <br/> Sammansättnings typ: totalt <br/> Dimension: EntityName|
|Fångade meddelanden |Antalet meddelanden eller händelser som har fångats till det valda målet under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Dimension: EntityName|
|Hämtade byte |Antalet byte som har fångats till det valda målet under en angiven period.<br/><br/> Enhet: byte <br/> Sammansättnings typ: totalt <br/> Dimension: EntityName|

## <a name="metrics-dimensions"></a>Mått dimensioner

Azure Event Hubs stöder följande dimensioner för mått i Azure Monitor. Det är valfritt att lägga till dimensioner i måtten. Om du inte lägger till dimensioner anges måtten på namn områdes nivå. 

| Mått namn | Beskrivning |
| ------------------- | ----------------- |
|Entitetsnamnet| Event Hubs stöder Event Hub-entiteter under namn området.|

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

- Kom igång med en kurs om händelsehubbar
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
* [Exempelprogram som använder Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png



