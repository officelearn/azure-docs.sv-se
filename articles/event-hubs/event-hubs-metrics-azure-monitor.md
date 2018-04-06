---
title: Azure Event Hubs mått i Azure-Monitor (förhandsversion) | Microsoft Docs
description: Använda Azure-övervakning för att övervaka Händelsehubbar
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
ms.date: 10/19/2017
ms.author: sethm
ms.openlocfilehash: 8ca00b234c00bfeb52a5b601e8780d56a0732dd9
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="azure-event-hubs-metrics-in-azure-monitor-preview"></a>Azure Event Hubs mått i Azure-Monitor (förhandsgranskning)

Event Hubs mått får tillståndet för Händelsehubbar resurser i din Azure-prenumeration. Med ett stort utbud av mätvärdesdata kan du utvärdera den övergripande hälsan för dina händelsehubbar inte bara på namnområdesnivån, utan också på nivån entitet. Statistiken kan vara viktigt eftersom de hjälper dig att övervaka status för din händelsehubbar. Mått kan också hjälpa till att felsöka problem med grundorsaken utan att behöva kontakta Azure-supporten.

Azure-Monitor ger enhetlig användargränssnitt för övervakning över olika Azure-tjänster. Mer information finns i [övervakning i Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) och [hämta Azure-Monitor mått med .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) på GitHub.

## <a name="access-metrics"></a>Åtkomst-mått

Azure-Monitor finns flera sätt att åtkomst mått. Du kan antingen åtkomst mätvärden via den [Azure-portalen](https://portal.azure.com), eller Använd Azure övervakaren API: er (REST och .NET) och lösningar för analys, till exempel åtgärden Management Suite och Händelsehubbar. Mer information finns i [Azure-Monitor mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

Mått är aktiverade som standard och du kan komma åt de senaste 30 dagarna av data. Du kan arkivera mått data till ett Azure Storage-konto om du vill behålla data under en längre tidsperiod. Detta är konfigurerat i [diagnostikinställningar](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) i Azure-Monitor.

## <a name="access-metrics-in-the-portal"></a>Åtkomst mått i portalen

Du kan övervaka mått över tid i den [Azure-portalen](https://portal.azure.com). I följande exempel visas hur du visar lyckade begäranden och inkommande begäranden på kontonivå:

![][1]

Du kan också komma åt mått direkt via namnområdet. Om du vill göra det, Välj namnområdet och klicka sedan på **mått (Peview)**. Om du vill visa mått som har filtrerats till händelsehubben omfattning, Välj händelsehubben och klicka sedan på **mått (förhandsgranskning)**.

För statistik som stöd för dimensioner, måste du filtrera med värdet för önskade dimensionen som visas i följande exempel:

![][2]

## <a name="billing"></a>Fakturering

Med hjälp av mätvärden i Azure-Monitor är för närvarande gratis när i förhandsgranskningen. Om du använder ytterligare lösningar som infognings-mätvärdesdata kan debiteras du dock av dessa lösningar. Till exempel debiteras du av Azure Storage om du arkiverar mått data till ett Azure Storage-konto. Du debiteras också av Azure om du strömma mätvärdesdata till logganalys för avancerad analys.

Följande mått ger dig en översikt över hälsotillståndet för din tjänst. 

> [!NOTE]
> Vi sluta flera mått som de flyttas under ett annat namn. Detta kräver att du uppdaterar din referenser. Mått som markerats med nyckelordet ”föråldrade” kommer inte att stödjas framöver.

Alla mätvärden-värden skickas till Azure-Monitor varje minut. Tidskornighet definierar det tidsintervall som mått värden anges. Tidsintervallet som stöds för alla Händelsehubbar mått är 1 minut.

## <a name="request-metrics"></a>Begäran mått

Räknar antalet begäranden om data och hantering av åtgärder.

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| Inkommande begäranden (förhandsgranskning) | Antalet begäranden som görs till Händelsehubbar i Azure-tjänsten under en angiven period. <br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName |
| Lyckade begäranden (förhandsgranskning)   | Antal slutförda förfrågningar som görs till Händelsehubbar i Azure-tjänsten under en angiven period. <br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName |
| Fel (förhandsgranskning) | Antalet begäranden som inte bearbetas på grund av ett fel i Händelsehubbar i Azure-tjänsten under en angiven period. <br/><br/>Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName |
|Fel (förhandsgranskning)|Antalet begäranden som inte bearbetas på grund av fel under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|
|Begränsad begäranden (förhandsgranskning)|Antalet begäranden som har begränsats eftersom genomströmning enhet användning har överskridits.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|
|Kvoten överskreds fel (förhandsgranskning)|Antalet begäranden överskrider tillgänglig kvot. Se [i den här artikeln](event-hubs-quotas.md) för mer information om Händelsehubbar kvoter.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|

## <a name="throughput-metrics"></a>Genomflödesmått

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|Begränsad begäranden (förhandsgranskning)|Antalet begäranden som har begränsats eftersom genomströmning enhet användning har överskridits.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|

## <a name="message-metrics"></a>Meddelande-mått

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|Inkommande meddelanden (förhandsgranskning)|Antal händelser eller meddelanden som skickas till Händelsehubbar under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|
|Utgående meddelanden (förhandsgranskning)|Antal händelser eller meddelanden som har hämtats från Event Hubs under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|
|Inkommande byte (förhandsgranskning)|Antal byte som skickas till tjänsten Azure Event Hubs under en angiven period.<br/><br/> Enhet: byte <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|
|Utgående byte (förhandsgranskning)|Antalet byte som hämtas från tjänsten Azure Event Hubs under en angiven period.<br/><br/> Enhet: byte <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|

## <a name="connection-metrics"></a>Anslutningen mått

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|ActiveConnections (förhandsgranskning)|Antal aktiva anslutningar på ett namnområde eller en entitet.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|
|Anslutningar Opened (förhandsgranskning)|Antalet öppna anslutningar.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|
|Anslutningar stängd (förhandsgranskning)|Antal avslutade anslutningar.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|

## <a name="event-hubs-capture-metrics"></a>Event Hubs avbilda mått

När du aktiverar funktionen avbildning för event hubs kan du övervaka Event Hubs avbilda mått. Följande mått beskrivs vad du kan övervaka med avbilda aktiverad.

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|Avbilda eftersläpning (förhandsgranskning)|Antal byte som ännu sparas till det valda målet.<br/><br/> Enhet: byte <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|
|Fångade meddelanden (förhandsgranskning)|Antalet meddelanden och händelser som har hämtats till det valda målet under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|
|Hämtade byte (förhandsgranskning)|Antal byte som har hämtats till det valda målet under en angiven period.<br/><br/> Enhet: byte <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|

## <a name="metrics-dimensions"></a>Mått dimensioner

Händelsehubbar i Azure stöder följande dimensioner för mått i Azure-Monitor. Det är valfritt att lägga till dimensioner till din statistik. Om du inte lägger till dimensioner, har mått angetts på namnområdesnivån. 

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|EntityName| Händelsehubbar stöder event hub entiteter i namnområdet.|

## <a name="next-steps"></a>Nästa steg

* Finns det [Azure övervakning-översikt](../monitoring-and-diagnostics/monitoring-overview.md).
* [Hämta Azure-Monitor mått med .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) på GitHub. 

Besök följande länkar för mer utförlig information om Event Hubs:

* Kom igång med en [kurs om händelsehubbar](event-hubs-dotnet-standard-getstarted-send.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
* [Exempelprogram som använder Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png



