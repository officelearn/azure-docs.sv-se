---
title: Azure Service Bus-mått i Azure Monitor (förhandsversion) | Microsoft Docs
description: Använda Azure-övervakning för att övervaka Service Bus-entiteter
services: service-bus-messaging
documentationcenter: .NET
author: sethmanheim
manager: timlt
ms.service: service-bus-messaging
ms.topic: article
ms.date: 05/31/2018
ms.author: sethm
ms.openlocfilehash: d27cb7a870a1a03e89ea35aa3ebe3a777483cf67
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39579467"
---
# <a name="azure-service-bus-metrics-in-azure-monitor-preview"></a>Azure Service Bus-mått i Azure Monitor (förhandsversion)

Service Bus mått ger dig tillståndet för resurser i din Azure-prenumeration. Med en omfattande uppsättning mätvärden, kan du utvärdera den övergripande hälsan för din Service Bus-resurser, inte bara på namnområdesnivå, utan även på enhetsnivå. Statistiken kan vara viktigt eftersom de hjälper dig att övervaka status för Service Bus. Mått kan också hjälpa till att felsöka problem med grundorsaken utan att behöva kontakta Azure-supporten.

Azure Monitor innehåller enhetligt användargränssnitt för övervakning över olika Azure-tjänster. Mer information finns i [övervakning i Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) och [hämta Azure Monitor-mått med .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) i GitHub.

> [!IMPORTANT]
> När det inte har en interaktion med en entitet i 2 timmar, startas mått som visar ”0” som ett värde tills enheten inte längre är inaktiv.

## <a name="access-metrics"></a>Åtkomst-mått

Azure Monitor innehåller flera sätt att åtkomst mått. Du kan antingen åtkomst mätvärden via den [Azure-portalen](https://portal.azure.com), eller använda Azure Monitor-API: er (REST och .NET) och lösningar för dataanalys, till exempel Log Analytics och Event Hubs. Mer information finns i [Azure Monitor metrics](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

Mått är aktiverade som standard och du kan komma åt de senaste 30 dagarna data. Om du vill behålla data under en längre tid kan du arkivera måttdata till ett Azure Storage-konto. Detta är konfigurerat i [diagnostikinställningar](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#diagnostic-settings) i Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Åtkomst-mått i portal

Du kan övervaka mått med tiden i den [Azure-portalen](https://portal.azure.com). I följande exempel visas hur du visar lyckade begäranden och inkommande begäranden på kontonivå:

![][1]

Du kan också komma åt mått direkt via namnområdet. Ditt namnområde för att göra det, och klicka sedan på **mått (Peview)**. Om du vill visa mått som filtrerats till i omfånget för entiteten, väljer du entitet och klicka sedan på **mått (förhandsgranskning)**.

![][2]

För mätvärden som stöd för dimensioner, måste du filtrera med önskad dimension-värde.

## <a name="billing"></a>Fakturering

Använda mått i Azure Monitor är kostnadsfritt i förhandsversionen. Om du använder ytterligare lösningar som mata in måttdata kan debiteras du dock av dessa lösningar. Exempelvis debiteras du per Azure Storage om du arkiverar måttdata till ett Azure Storage-konto. Du debiteras också av Log Analytics om du strömma mätvärden till Log Analytics för avancerad analys.

Följande mått ger dig en översikt över hälsotillståndet för din tjänst. 

> [!NOTE]
> Vi avvecklar flera mått när de flyttas under ett annat namn. Detta kräver att du uppdaterar dina referenser. Mått som markerats med nyckelordet ”inaktuell” stöds inte framöver.

Alla mått-värden skickas till Azure Monitor varje minut. Tidskornighet definierar tidsintervallet som mått visas. Tidsintervallet som stöds för alla Service Bus-mått är 1 minut.

## <a name="request-metrics"></a>Begäran-mått

Räknar antalet förfrågningar om data och hantering av åtgärder.

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| Inkommande förfrågningar (förhandsversion) | Antalet begäranden som görs till Service Bus-tjänsten under en angiven period. <br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|Lyckade begäranden (förhandsversion)|Antal lyckade begäranden som görs till Service Bus-tjänsten under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|Fel (förhandsversion)|Antal begäranden som inte bearbetas på grund av ett fel i Service Bus-tjänsten under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|Användarfel (förhandsversion, se följande avsnitt)|Antal begäranden som inte bearbetas på grund av användarfel under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|Begränsade begäranden (förhandsversion)|Antal begäranden som har begränsats eftersom användningen har överskridits.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|

### <a name="user-errors"></a>Användarfel

Följande två typer av fel är klassificerade som användarfel:

1. Sida klientfel (i HTTP som skulle vara 400 fel).
2. Fel som uppstår vid bearbetning av meddelanden, till exempel [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Meddelande-mått

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|Inkommande meddelanden (förhandsversion)|Antal händelser eller meddelanden som skickas till Service Bus under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|Utgående meddelanden (förhandsversion)|Antal händelser eller meddelanden som tas emot från Service Bus under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|

## <a name="connection-metrics"></a>Anslutningsmått

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|ActiveConnections (förhandsversion)|Antal aktiva anslutningar för ett namnområde och på en entitet.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|Öppnade anslutningar (förhandsversion)|Antal öppna anslutningar.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|Anslutningar stängd (förhandsversion)|Antal stängda anslutningar.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName |

## <a name="resource-usage-metrics"></a>Användningsstatistik för resursen

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|Processoranvändning per namnområde (förhandsversion)|Procent CPU-användning av namnområdet.<br/><br/> Enhet: procent <br/> Sammansättningstyp: högsta <br/> Dimensionen: EntityName|
|Minnesstorleksanvändning per namnområde (förhandsversion)|Procentandel minnesanvändningen för namnområdet.<br/><br/> Enhet: procent <br/> Sammansättningstyp: högsta <br/> Dimensionen: EntityName|

## <a name="metrics-dimensions"></a>Mått dimensioner

Azure Service Bus stöder följande dimensioner för mått i Azure Monitor. Det är valfritt att lägga till dimensioner i dina mått. Om du inte lägger till dimensioner, har mått angetts på namnområdesnivå. 

|Dimensionsnamn|Beskrivning|
| ------------------- | ----------------- |
|EntityName| Service Bus stöder meddelandeentiteter under namnområdet.|

## <a name="next-steps"></a>Nästa steg

Se den [Azure övervakning-översikt](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


