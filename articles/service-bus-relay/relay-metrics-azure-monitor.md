---
title: Azure Relay mått i Azure Monitor | Microsoft Docs
description: Den här artikeln innehåller information om hur du kan använda Azure Monitor för att övervaka till Azure Relays tillstånd.
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
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273121"
---
# <a name="azure-relay-metrics-in-azure-monitor"></a>Azure Relay mått i Azure Monitor 
Azure Relay mått ger dig tillstånd för resurser i din Azure-prenumeration. Med en omfattande uppsättning Mät data kan du utvärdera övergripande hälso tillstånd för dina relä resurser, inte bara på namn områdes nivå, utan även på enhets nivå. Den här statistiken kan vara viktig när de hjälper dig att övervaka status för Azure Relay. Mått kan också hjälpa till att felsöka problem med grundorsaken utan att behöva kontakta Azure-supporten.

Azure Monitor innehåller enhetligt användargränssnitt för övervakning över olika Azure-tjänster. Mer information finns i [övervakning i Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) och [Hämta Azure Monitor mått med .net](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) -exempel på GitHub.

> [!IMPORTANT]
> Den här artikeln gäller bara för den Hybridanslutningar funktionen i Azure Relay, inte på WCF Relay. 

## <a name="access-metrics"></a>Åtkomst-mått

Azure Monitor innehåller flera sätt att åtkomst mått. Du kan antingen komma åt mått via [Azure Portal](https://portal.azure.com)eller använda Azure Monitor-API: er (rest och .net) och analys lösningar som operation Management Suite och Event Hubs. Mer information finns i [övervaka data som samlas in av Azure Monitor](../azure-monitor/platform/data-platform.md).

Mått är aktiverade som standard och du kan komma åt de senaste 30 dagarna data. Om du vill behålla data under en längre tid kan du arkivera måttdata till ett Azure Storage-konto. Detta konfigureras i [diagnostikinställningar](../azure-monitor/platform/diagnostic-settings.md) i Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Åtkomst-mått i portal

Du kan övervaka mått över tid i [Azure Portal](https://portal.azure.com). I följande exempel visas hur du visar lyckade begäranden och inkommande begäranden på kontonivå:

![][1]

Du kan också komma åt mått direkt via namnområdet. Om du vill göra det väljer du namn området och klickar sedan på * * mått * *. 

För mått som stöder dimensioner måste du filtrera med det önskade dimension svärdet.

## <a name="billing"></a>Fakturering

Använda mått i Azure Monitor är för närvarande kostnadsfritt i förhandsversionen. Om du använder ytterligare lösningar som mata in måttdata kan debiteras du dock av dessa lösningar. Exempelvis debiteras du per Azure Storage om du arkiverar måttdata till ett Azure Storage-konto. Du debiteras också med Azure Monitor loggar om du strömmar mått data till Azure Monitor loggar för avancerad analys.

Följande mått ger dig en översikt över hälsotillståndet för din tjänst. 

> [!NOTE]
> Vi avvecklar flera mått när de flyttas under ett annat namn. Detta kräver att du uppdaterar dina referenser. Mått som markerats med nyckelordet ”inaktuell” stöds inte framöver.

Alla mått-värden skickas till Azure Monitor varje minut. Tidskornighet definierar tidsintervallet som mått visas. Det tidsintervall som stöds för alla Azure Relay mått är 1 minut.

## <a name="connection-metrics"></a>Anslutningsmått

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| ListenerConnections – lyckad  | Antalet lyckade lyssnar anslutningar som har gjorts till Azure Relay under en angiven period. <br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|ListenerConnections – ClientError |Antalet klient fel i lyssnar anslutningar under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|ListenerConnections – ServerError |Antalet Server fel på lyssnar anslutningar under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|SenderConnections-Success |Antalet lyckade avsändare som gjorts under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|SenderConnections – ClientError |Antalet klient fel på avsändarens anslutningar under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|SenderConnections – ServerError |Antalet Server fel på avsändarens anslutningar under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|ListenerConnections-TotalRequests |Det totala antalet lyssnar anslutningar under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|SenderConnections-TotalRequests |Anslutnings förfrågningar som skickats av avsändarna under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|ActiveConnections |Antalet aktiva anslutningar. Det här värdet är ett värde för tidpunkt.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|ActiveListeners |Antalet aktiva lyssnare. Det här värdet är ett värde för tidpunkt.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|ListenerDisconnects |Antalet frånkopplade lyssnare under en angiven period.<br/><br/> Enhet: byte <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|SenderDisconnects |Antalet frånkopplade avsändare under en angiven period.<br/><br/> Enhet: byte <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|

## <a name="memory-usage-metrics"></a>Minnes användnings mått

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|BytesTransferred |Antalet byte som överförts under en angiven period.<br/><br/> Enhet: byte <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|

## <a name="metrics-dimensions"></a>Mått dimensioner

Azure Relay stöder följande dimensioner för mått i Azure Monitor. Det är valfritt att lägga till dimensioner i dina mått. Om du inte lägger till dimensioner, har mått angetts på namnområdesnivå. 

|Dimensions namn|Beskrivning|
| ------------------- | ----------------- |
|EntityName| Azure Relay stöder meddelande enheter under namn området.|

## <a name="next-steps"></a>Nästa steg

Se [Översikt över Azure Monitoring](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




