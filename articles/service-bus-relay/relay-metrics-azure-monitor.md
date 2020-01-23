---
title: Azure Relay mått i Azure Monitor (förhands granskning) | Microsoft Docs
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
ms.openlocfilehash: 5c548186ec51cf86f34942cb15d8f984afa60268
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514943"
---
# <a name="azure-relay-metrics-in-azure-monitor-preview"></a>Azure Relay mått i Azure Monitor (förhands granskning)
Azure Relay mått ger dig tillstånd för resurser i din Azure-prenumeration. Med en omfattande uppsättning Mät data kan du utvärdera övergripande hälso tillstånd för dina relä resurser, inte bara på namn områdes nivå, utan även på enhets nivå. Den här statistiken kan vara viktig när de hjälper dig att övervaka status för Azure Relay. Mått kan också hjälpa till att felsöka problem med grundorsaken utan att behöva kontakta Azure-supporten.

Azure Monitor innehåller enhetligt användargränssnitt för övervakning över olika Azure-tjänster. Mer information finns i [övervakning i Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) och [hämta Azure Monitor-mått med .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) i GitHub.

> [!IMPORTANT]
> Den här artikeln gäller bara för den Hybridanslutningar funktionen i Azure Relay, inte på WCF Relay. 

## <a name="access-metrics"></a>Åtkomst-mått

Azure Monitor innehåller flera sätt att åtkomst mått. Du kan antingen åtkomst mätvärden via den [Azure-portalen](https://portal.azure.com), eller använda Azure Monitor-API: er (REST och .NET) och lösningar för dataanalys, till exempel Operations Management Suite och Event Hubs. Mer information finns i [övervakningsdata som samlas in av Azure Monitor](../azure-monitor/platform/data-platform.md).

Mått är aktiverade som standard och du kan komma åt de senaste 30 dagarna data. Om du vill behålla data under en längre tid kan du arkivera måttdata till ett Azure Storage-konto. Detta är konfigurerat i [diagnostikinställningar](../azure-monitor/platform/diagnostic-settings.md) i Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Åtkomst-mått i portal

Du kan övervaka mått med tiden i den [Azure-portalen](https://portal.azure.com). I följande exempel visas hur du visar lyckade begäranden och inkommande begäranden på kontonivå:

![][1]

Du kan också komma åt mått direkt via namnområdet. Det gör du genom att välja ditt namn område och sedan klicka på **mått (för hands version)** . 

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
| ListenerConnections – lyckades (för hands version) | Antalet lyckade lyssnar anslutningar som har gjorts till Azure Relay under en angiven period. <br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|ListenerConnections-ClientError (för hands version)|Antalet klient fel i lyssnar anslutningar under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|ListenerConnections-ServerError (för hands version)|Antalet Server fel på lyssnar anslutningar under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|SenderConnections – lyckades (för hands version)|Antalet lyckade avsändare som gjorts under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|SenderConnections-ClientError (för hands version)|Antalet klient fel på avsändarens anslutningar under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|SenderConnections-ServerError (för hands version)|Antalet Server fel på avsändarens anslutningar under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|ListenerConnections-TotalRequests (för hands version)|Det totala antalet lyssnar anslutningar under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|SenderConnections-TotalRequests (för hands version)|Anslutnings förfrågningar som skickats av avsändarna under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|ActiveConnections (förhandsversion)|Antalet aktiva anslutningar under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|ActiveListeners (för hands version)|Antalet aktiva lyssnare under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|ListenerDisconnects (för hands version)|Antalet frånkopplade lyssnare under en angiven period.<br/><br/> Enhet: byte <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|
|SenderDisconnects (för hands version)|Antalet frånkopplade avsändare under en angiven period.<br/><br/> Enhet: byte <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|

## <a name="memory-usage-metrics"></a>Minnes användnings mått

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|BytesTransferred (för hands version)|Antalet byte som överförts under en angiven period.<br/><br/> Enhet: byte <br/> Sammansättningstyp: Totalt antal <br/> Dimensionen: EntityName|

## <a name="metrics-dimensions"></a>Mått dimensioner

Azure Relay stöder följande dimensioner för mått i Azure Monitor. Det är valfritt att lägga till dimensioner i dina mått. Om du inte lägger till dimensioner, har mått angetts på namnområdesnivå. 

|Dimensions namn|Beskrivning|
| ------------------- | ----------------- |
|EntityName| Azure Relay stöder meddelande enheter under namn området.|

## <a name="next-steps"></a>Nästa steg

Se den [Azure övervakning-översikt](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




