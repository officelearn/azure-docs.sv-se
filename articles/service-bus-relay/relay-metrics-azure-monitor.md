---
title: Azure Relay-mått i Azure Monitor (förhandsversion) | Microsoft Docs
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
ms.openlocfilehash: 3bcea7ee15bea137fecc55f1e641b84e2e72d3ff
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247446"
---
# <a name="azure-relay-metrics-in-azure-monitor-preview"></a>Azure Relay-mått i Azure Monitor (förhandsversion)

Azure Relay-mått ger dig tillståndet för resurser i din Azure-prenumeration. Med en omfattande uppsättning mätvärden, kan du utvärdera den övergripande hälsan för dina Relay-resurser, inte bara på namnområdesnivå, utan även på enhetsnivå. Statistiken kan vara viktigt eftersom de hjälper dig att övervaka status för Azure Relay. Mått kan också hjälpa till att felsöka problem med grundorsaken utan att behöva kontakta Azure-supporten.

Azure Monitor innehåller enhetligt användargränssnitt för övervakning över olika Azure-tjänster. Mer information finns i [övervakning i Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) och [hämta Azure Monitor-mått med .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) i GitHub.

## <a name="access-metrics"></a>Åtkomst-mått

Azure Monitor innehåller flera sätt att åtkomst mått. Du kan antingen åtkomst mätvärden via den [Azure-portalen](https://portal.azure.com), eller använda Azure Monitor-API: er (REST och .NET) och lösningar för dataanalys, till exempel Operations Management Suite och Event Hubs. Mer information finns i [Azure Monitor metrics](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

Mått är aktiverade som standard och du kan komma åt de senaste 30 dagarna data. Om du vill behålla data under en längre tid kan du arkivera måttdata till ett Azure Storage-konto. Detta är konfigurerat i [diagnostikinställningar](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#diagnostic-settings) i Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Åtkomst-mått i portal

Du kan övervaka mått med tiden i den [Azure-portalen](https://portal.azure.com). I följande exempel visas hur du visar lyckade begäranden och inkommande begäranden på kontonivå:

![][1]

Du kan också komma åt mått direkt via namnområdet. Ditt namnområde för att göra det, och klicka sedan på **mått (Peview)**. 

För mätvärden som stöd för dimensioner, måste du filtrera med önskad dimension-värde.

## <a name="billing"></a>Fakturering

Använda mått i Azure Monitor är för närvarande kostnadsfritt i förhandsversionen. Om du använder ytterligare lösningar som mata in måttdata kan debiteras du dock av dessa lösningar. Exempelvis debiteras du per Azure Storage om du arkiverar måttdata till ett Azure Storage-konto. Du debiteras också av Log Analytics om du strömma mätvärden till Log Analytics för avancerad analys.

Följande mått ger dig en översikt över hälsotillståndet för din tjänst. 

> [!NOTE]
> Vi avvecklar flera mått när de flyttas under ett annat namn. Detta kräver att du uppdaterar dina referenser. Mått som markerats med nyckelordet ”inaktuell” stöds inte framöver.

Alla mått-värden skickas till Azure Monitor varje minut. Tidskornighet definierar tidsintervallet som mått visas. Tidsintervallet som stöds för alla Azure Relay-mått är 1 minut.

## <a name="connection-metrics"></a>Anslutningsmått

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| ListenerConnections lyckades (förhandsversion) | Antal lyckade lyssnare som har anslutningar till Azure Relay under en angiven period. <br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|ListenerConnections ClientError (förhandsversion)|Antalet klientfel på lyssnaren anslutningar under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|ListenerConnections ServerError (förhandsversion)|Antal fel på lyssnaren anslutningar under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|SenderConnections lyckades (förhandsversion)|Antal lyckade avsändaren anslutningar som görs under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|SenderConnections-ClientError (förhandsversion)|Antalet klientfel på avsändaren anslutningar under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|SenderConnections-ServerError (förhandsversion)|Antal fel på avsändaren anslutningar under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|ListenerConnections TotalRequests (förhandsversion)|Det totala antalet lyssnare anslutningar under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|SenderConnections-TotalRequests (förhandsversion)|Vilka begäranden som görs av avsändare under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|ActiveConnections (förhandsversion)|Antal aktiva anslutningar under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|ActiveListeners (förhandsversion)|Antal aktiva lyssnare under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|ListenerDisconnects (förhandsversion)|Antal frånkopplade lyssnare under en angiven period.<br/><br/> Enhet: byte <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|SenderDisconnects (förhandsversion)|Antal frånkopplade avsändare under en angiven period.<br/><br/> Enhet: byte <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|

## <a name="memory-usage-metrics"></a>Användningsstatistik för minne

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|BytesTransferred (förhandsversion)|Antalet byte som överförs under en angiven period.<br/><br/> Enhet: byte <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|

## <a name="metrics-dimensions"></a>Mått dimensioner

Azure Relay stöder följande dimensioner för mått i Azure Monitor. Det är valfritt att lägga till dimensioner i dina mått. Om du inte lägger till dimensioner, har mått angetts på namnområdesnivå. 

|Dimensionsnamn|Beskrivning|
| ------------------- | ----------------- |
|EntityName| Azure Relay stöder meddelandeentiteter under namnområdet.|

## <a name="next-steps"></a>Nästa steg

Se den [Azure övervakning-översikt](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




