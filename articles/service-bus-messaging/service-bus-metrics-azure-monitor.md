---
title: "Azure Service Bus-mätvärden i Azure-Monitor (förhandsversion) | Microsoft Docs"
description: "Använda Azure-övervakning för att övervaka Service Bus-entiteter"
services: service-bus-messaging
documentationcenter: .NET
author: christianwolf42
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/05/2018
ms.author: sethm
ms.openlocfilehash: 20115897bb5ae2638588e79d80700fa8ece06104
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="azure-service-bus-metrics-in-azure-monitor-preview"></a>Azure Service Bus-mätvärden i Azure-Monitor (förhandsgranskning)

Service Bus mått får tillståndet för resurser i din Azure-prenumeration. Med ett stort utbud av mätvärdesdata kan du utvärdera den övergripande hälsan för dina Service Bus-resurser, inte bara på namnområdesnivån, utan också på nivån entitet. Statistiken kan vara viktigt eftersom de hjälper dig att övervaka status för Service Bus. Mått kan också hjälpa till att felsöka problem med grundorsaken utan att behöva kontakta Azure-supporten.

Azure-Monitor ger enhetlig användargränssnitt för övervakning över olika Azure-tjänster. Mer information finns i [övervakning i Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) och [hämta Azure-Monitor mått med .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) på GitHub.

## <a name="access-metrics"></a>Åtkomst-mått

Azure-Monitor finns flera sätt att åtkomst mått. Du kan antingen åtkomst mätvärden via den [Azure-portalen](https://portal.azure.com), eller Använd Azure övervakaren API: er (REST och .NET) och lösningar för analys, till exempel åtgärden Management Suite (OMS) och Händelsehubbar. Mer information finns i [Azure-Monitor mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

Mått är aktiverade som standard och du kan komma åt de senaste 30 dagarna av data. Du kan arkivera mått data till ett Azure Storage-konto om du vill behålla data under en längre tidsperiod. Detta är konfigurerat i [diagnostikinställningar](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) i Azure-Monitor.

## <a name="access-metrics-in-the-portal"></a>Åtkomst mått i portalen

Du kan övervaka mått över tid i den [Azure-portalen](https://portal.azure.com). I följande exempel visas hur du visar lyckade begäranden och inkommande begäranden på kontonivå:

![][1]

Du kan också komma åt mått direkt via namnområdet. Om du vill göra det, Välj namnområdet och klicka sedan på **mått (Peview)**. Välj enheten om du vill visa mått som har filtrerats till omfånget för entiteten och klicka sedan på **mått (förhandsgranskning)**.

![][2]

För statistik som stöd för dimensioner, måste du filtrera med värdet för önskade dimensionen.

## <a name="billing"></a>Fakturering

Med hjälp av mätvärden i Azure-Monitor är gratis när i förhandsgranskningen. Om du använder ytterligare lösningar som infognings-mätvärdesdata kan debiteras du dock av dessa lösningar. Till exempel debiteras du av Azure Storage om du arkiverar mått data till ett Azure Storage-konto. Du debiteras också av åtgärden Management Suite (OMS) om du strömma mätvärdesdata till OMS för avancerad analys.

Följande mått ger dig en översikt över hälsotillståndet för din tjänst. 

> [!NOTE]
> Vi sluta flera mått som de flyttas under ett annat namn. Detta kräver att du uppdaterar din referenser. Mått som markerats med nyckelordet ”föråldrade” kommer inte att stödjas framöver.

Alla mätvärden-värden skickas till Azure-Monitor varje minut. Tidskornighet definierar det tidsintervall som mått värden anges. Alla Service Bus-mått stöds tidsintervallet är 1 minut.

## <a name="request-metrics"></a>Begäran mått

Räknar antalet begäranden om data och hantering av åtgärder.

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| Inkommande begäranden (förhandsgranskning) | Antalet begäranden som görs till Service Bus-tjänsten under en angiven period. <br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|
|Lyckade begäranden (förhandsgranskning)|Antal slutförda förfrågningar som görs till Service Bus-tjänsten under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|
|Fel (förhandsgranskning)|Antalet begäranden som inte bearbetas på grund av ett fel i Service Bus-tjänsten under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|
|Fel (förhandsgranskning)|Antalet begäranden som inte bearbetas på grund av fel under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|
|Begränsad begäranden (förhandsgranskning)|Antalet begäranden som har begränsats eftersom användningen har överskridits.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|

## <a name="message-metrics"></a>Meddelande-mått

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|Inkommande meddelanden (förhandsgranskning)|Antal händelser eller meddelanden som skickas till Service Bus under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|
|Utgående meddelanden (förhandsgranskning)|Antal händelser eller meddelanden som tagits emot från Service Bus under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|

## <a name="connection-metrics"></a>Anslutningen mått

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|ActiveConnections (förhandsgranskning)|Antal aktiva anslutningar på ett namnområde eller en entitet.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|
|Anslutningar Opened (förhandsgranskning)|Antalet öppna anslutningar.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|
|Anslutningar stängd (förhandsgranskning)|Antal avslutade anslutningar.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName |

## <a name="resource-usage-metrics"></a>Användningsstatistik för resurs

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|CPU-användning per namnområde (förhandsgranskning)|Procentandelen CPU-användning av namnområdet.<br/><br/> Enhet: procent <br/> Sammansättningstyp: högsta <br/> Dimensionen: EntityName|
|Minnesanvändning storleken per namnområde (förhandsgranskning)|Procentandel minnesanvändning för namnområdet.<br/><br/> Enhet: procent <br/> Sammansättningstyp: högsta <br/> Dimensionen: EntityName|

## <a name="metrics-dimensions"></a>Mått dimensioner

Azure Service Bus stöder följande dimensioner för mått i Azure-Monitor. Det är valfritt att lägga till dimensioner till din statistik. Om du inte lägger till dimensioner, har mått angetts på namnområdesnivån. 

|Dimensionsnamnet|Beskrivning|
| ------------------- | ----------------- |
|EntityName| Service Bus stöder meddelandeentiteter i namnområdet.|

## <a name="next-steps"></a>Nästa steg

Finns det [Azure övervakning-översikt](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


