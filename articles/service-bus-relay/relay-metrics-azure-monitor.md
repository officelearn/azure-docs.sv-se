---
title: Azure Relay-mått i Azure Monitor | Microsoft-dokument
description: Den här artikeln innehåller information om hur du kan använda Azure Monitor för att övervaka till tillståndet för Azure Relay.
services: service-bus-relay
documentationcenter: .NET
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 159249e2c997e4c414127992b08a83b488281e46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273121"
---
# <a name="azure-relay-metrics-in-azure-monitor"></a>Azure Relay-mått i Azure Monitor 
Azure Relay-mått ger dig tillståndet för resurser i din Azure-prenumeration. Med en omfattande uppsättning måttdata kan du bedöma den övergripande hälsan för relay-resurser, inte bara på namnområdesnivå utan även på entitetsnivå. Den här statistiken kan vara viktig eftersom de hjälper dig att övervaka tillståndet för Azure Relay. Mått kan också hjälpa till att felsöka problem med rotorsaken utan att behöva kontakta Azure-supporten.

Azure Monitor tillhandahåller enhetliga användargränssnitt för övervakning av olika Azure-tjänster. Mer information finns [i Övervakning i Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) och hämta Azure [Monitor-mått med .NET-exempel](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) på GitHub.

> [!IMPORTANT]
> Den här artikeln gäller endast hybridanslutningsfunktionen i Azure Relay, inte wcf-reläet. 

## <a name="access-metrics"></a>Åtkomstmått

Azure Monitor innehåller flera sätt att komma åt mått. Du kan antingen komma åt mått via [Azure-portalen](https://portal.azure.com)eller använda AZURE Monitor API:er (REST och .NET) och analyslösningar som Operation Management Suite och Event Hubs. Mer information finns i [Övervaka data som samlas in av Azure Monitor](../azure-monitor/platform/data-platform.md).

Mått är aktiverade som standard och du kan komma åt de senaste 30 dagarnas data. Om du behöver behålla data under en längre tid kan du arkivera måttdata till ett Azure Storage-konto. Detta är konfigurerat i [diagnostikinställningar](../azure-monitor/platform/diagnostic-settings.md) i Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Åtkomstmått i portalen

Du kan övervaka mått över tid i [Azure-portalen](https://portal.azure.com). I följande exempel visas hur du visar lyckade begäranden och inkommande begäranden på kontonivå:

![][1]

Du kan också komma åt mått direkt via namnområdet. Om du vill göra det markerar du ditt namnområde och klickar sedan på **Mått **. 

För mått som stöder dimensioner måste du filtrera med önskat dimensionsvärde.

## <a name="billing"></a>Fakturering

Att använda mått i Azure Monitor är för närvarande gratis i förhandsversion. Men om du använder ytterligare lösningar som ger svarmetridata kan du faktureras av dessa lösningar. Du debiteras till exempel av Azure Storage om du arkiverar måttdata till ett Azure Storage-konto. Du faktureras också av Azure Monitor-loggar om du strömmar måttdata till Azure Monitor-loggar för avancerad analys.

Följande mått ger dig en översikt över hälsotillståndet för din tjänst. 

> [!NOTE]
> Vi inaktuella flera mått när de flyttas under ett annat namn. Detta kan kräva att du uppdaterar dina referenser. Mått som markerats med nyckelordet "föråldrad" stöds inte framöver.

Alla måttvärden skickas till Azure Monitor varje minut. Tidsgranulariteten definierar det tidsintervall för vilket måttvärden presenteras. Tidsintervallet som stöds för alla Azure Relay-mått är 1 minut.

## <a name="connection-metrics"></a>Anslutningsmått

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| ListenerConnections-Framgång  | Antalet lyckade lyssnaranslutningar som gjorts till Azure Relay under en angiven period. <br/><br/> Enhet: Antal <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|
|ListenerConnections-ClientError |Antalet klientfel på lyssnaranslutningar under en angiven period.<br/><br/> Enhet: Antal <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|
|ListenerConnections-ServerError |Antalet serverfel på lyssnaranslutningarna under en angiven period.<br/><br/> Enhet: Antal <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|
|SenderConnections-Framgång |Antalet lyckade avsändnad anslutningar som gjorts under en angiven period.<br/><br/> Enhet: Antal <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|
|SenderConnections-ClientError |Antalet klientfel på avsändareanslutningar under en angiven period.<br/><br/> Enhet: Antal <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|
|SenderConnections-ServerError |Antalet serverfel på avsändareanslutningar under en angiven period.<br/><br/> Enhet: Antal <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|
|LyssnarenAnslutningar-TotaltRequests |Det totala antalet lyssnaranslutningar under en angiven period.<br/><br/> Enhet: Antal <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|
|SenderConnections-TotalRequests |Anslutningsbegäranden som gjorts av avsändarna under en angiven period.<br/><br/> Enhet: Antal <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|
|ActiveConnections (Aktiva Anslutning) |Antalet aktiva anslutningar. Det här värdet är ett punkt-i-tid-värde.<br/><br/> Enhet: Antal <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|
|ActiveListeners |Antalet aktiva lyssnare. Det här värdet är ett punkt-i-tid-värde.<br/><br/> Enhet: Antal <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|
|LyssnareAnsluter |Antalet frånkopplade lyssnare under en angiven period.<br/><br/> Enhet: Byte <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|
|SenderDisconnects |Antalet frånkopplade avsändare under en angiven period.<br/><br/> Enhet: Byte <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|

## <a name="memory-usage-metrics"></a>Mått för minnesanvändning

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|ByteÖverläst |Antalet byte som överförts under en angiven period.<br/><br/> Enhet: Byte <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|

## <a name="metrics-dimensions"></a>Måttdimensioner

Azure Relay stöder följande dimensioner för mått i Azure Monitor. Det är valfritt att lägga till dimensioner i måtten. Om du inte lägger till dimensioner anges mått på namnområdesnivå. 

|Dimensionsnamn|Beskrivning|
| ------------------- | ----------------- |
|Entityname| Azure Relay stöder meddelandeenheter under namnområdet.|

## <a name="next-steps"></a>Nästa steg

Se [översikten över Azure-övervakning](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




