---
title: Azure Relay mätvärden i Azure-Monitor (förhandsversion) | Microsoft Docs
description: Använda Azure-övervakning för att övervaka Azure Relay
services: service-bus-relay
documentationcenter: .NET
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: b919f1fb0799ec497742115b2611f34872af2027
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="azure-relay-metrics-in-azure-monitor-preview"></a>Azure Relay mätvärden i Azure-Monitor (förhandsgranskning)

Azure Relay mått får tillståndet för resurser i din Azure-prenumeration. Med ett stort utbud av mätvärdesdata kan du utvärdera den övergripande hälsan för dina Relay-resurser, inte bara på namnområdesnivån, utan också på nivån entitet. Statistiken kan vara viktigt eftersom de hjälper dig att övervaka status för Azure Relay. Mått kan också hjälpa till att felsöka problem med grundorsaken utan att behöva kontakta Azure-supporten.

Azure-Monitor ger enhetlig användargränssnitt för övervakning över olika Azure-tjänster. Mer information finns i [övervakning i Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) och [hämta Azure-Monitor mått med .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) på GitHub.

## <a name="access-metrics"></a>Åtkomst-mått

Azure-Monitor finns flera sätt att åtkomst mått. Du kan antingen åtkomst mätvärden via den [Azure-portalen](https://portal.azure.com), eller Använd Azure övervakaren API: er (REST och .NET) och lösningar för analys, till exempel åtgärden Management Suite och Händelsehubbar. Mer information finns i [Azure-Monitor mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

Mått är aktiverade som standard och du kan komma åt de senaste 30 dagarna av data. Du kan arkivera mått data till ett Azure Storage-konto om du vill behålla data under en längre tidsperiod. Detta är konfigurerat i [diagnostikinställningar](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) i Azure-Monitor.

## <a name="access-metrics-in-the-portal"></a>Åtkomst mått i portalen

Du kan övervaka mått över tid i den [Azure-portalen](https://portal.azure.com). I följande exempel visas hur du visar lyckade begäranden och inkommande begäranden på kontonivå:

![][1]

Du kan också komma åt mått direkt via namnområdet. Om du vill göra det, Välj namnområdet och klicka sedan på **mått (Peview)**. 

För statistik som stöd för dimensioner, måste du filtrera med värdet för önskade dimensionen.

## <a name="billing"></a>Fakturering

Med hjälp av mätvärden i Azure-Monitor är för närvarande gratis när i förhandsgranskningen. Om du använder ytterligare lösningar som infognings-mätvärdesdata kan debiteras du dock av dessa lösningar. Till exempel debiteras du av Azure Storage om du arkiverar mått data till ett Azure Storage-konto. Du debiteras också av logganalys om direktuppspelning av mätvärdesdata till logganalys för avancerad analys.

Följande mått ger dig en översikt över hälsotillståndet för din tjänst. 

> [!NOTE]
> Vi sluta flera mått som de flyttas under ett annat namn. Detta kräver att du uppdaterar din referenser. Mått som markerats med nyckelordet ”föråldrade” kommer inte att stödjas framöver.

Alla mätvärden-värden skickas till Azure-Monitor varje minut. Tidskornighet definierar det tidsintervall som mått värden anges. Stöds tidsintervall för alla Azure Relay mått är 1 minut.

## <a name="connection-metrics"></a>Anslutningen mått

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| ListenerConnections lyckades (förhandsgranskning) | Antal lyckade lyssnare anslutningar till Azure-relä under en angiven period. <br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|
|ListenerConnections ClientError (förhandsgranskning)|Antalet klientfel i på lyssnaren anslutningar under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|
|ListenerConnections ServerError (förhandsgranskning)|Antalet fel på lyssnaren anslutningar under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|
|SenderConnections lyckades (förhandsgranskning)|Antal lyckade avsändaren anslutningar som görs under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|
|SenderConnections ClientError (förhandsgranskning)|Antalet klientfel i på avsändaren anslutningar under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|
|SenderConnections ServerError (förhandsgranskning)|Antalet fel på avsändaren anslutningar under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|
|ListenerConnections-TotalRequests (preview)|Det totala antalet lyssnare anslutningar under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|
|SenderConnections-TotalRequests (preview)|Vilka begäranden som gjorts av avsändare under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|
|ActiveConnections (förhandsgranskning)|Antal aktiva anslutningar under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|
|ActiveListeners (förhandsgranskning)|Antal aktiva lyssnare under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|
|ListenerDisconnects (förhandsgranskning)|Antal frånkopplade lyssnare under en angiven period.<br/><br/> Enhet: byte <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|
|SenderDisconnects (förhandsgranskning)|Antal frånkopplade avsändare under en angiven period.<br/><br/> Enhet: byte <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|

## <a name="memory-usage-metrics"></a>Användningsstatistik för minne

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|BytesTransferred (förhandsgranskning)|Antalet byte som överförs under en angiven period.<br/><br/> Enhet: byte <br/> Sammansättningstyp: Totalt <br/> Dimensionen: EntityName|

## <a name="metrics-dimensions"></a>Mått dimensioner

Azure Relay stöder följande dimensioner för mått i Azure-Monitor. Det är valfritt att lägga till dimensioner till din statistik. Om du inte lägger till dimensioner, har mått angetts på namnområdesnivån. 

|Dimensionsnamnet|Beskrivning|
| ------------------- | ----------------- |
|EntityName| Azure Relay stöder meddelandeentiteter i namnområdet.|

## <a name="next-steps"></a>Nästa steg

Finns det [Azure övervakning-översikt](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




