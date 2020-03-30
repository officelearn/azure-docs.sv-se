---
title: Använd blob-lagring för IIS och tabelllagring för händelser i Azure Monitor | Microsoft-dokument
description: Azure Monitor kan läsa loggarna för Azure-tjänster som skriver diagnostik till tabelllagring eller IIS-loggar skrivna till blob-lagring.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 44368ab90abd189c6a8a0792494828c87142eb20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672403"
---
# <a name="collect-data-from-azure-diagnostics-extension-to-azure-monitor-logs"></a>Samla in data från Azure Diagnostics-tillägget till Azure Monitor Logs
Azure Diagnostics-tillägget är en [agent i Azure Monitor](agents-overview.md) som samlar in övervakningsdata från gästoperativsystemet för Azure-beräkningsresurser inklusive virtuella datorer. I den här artikeln beskrivs hur du samlar in data som samlas in av diagnostiktillägget från Azure Storage till Azure Monitor Logs.

> [!NOTE]
> Log Analytics-agenten i Azure Monitor är vanligtvis den metod som föredras för att samla in data från gästoperativsystemet i Azure Monitor Logs. Se [Översikt över Azure Monitor-agenter](agents-overview.md) för en detaljerad jämförelse av agenterna.

## <a name="supported-data-types"></a>Datatyper som stöds
Azure Diagnostics-tillägget lagrar data i ett Azure Storage-konto. För att Azure Monitor Logs ska kunna samla in dessa data måste de finnas på följande platser:

| Typ av logg | Resurstyp | Location |
| --- | --- | --- |
| IIS-loggar |Virtuella datorer <br> Webbroller <br> Arbetarroller |wad-iis-loggfiler (Blob Storage) |
| Syslog |Virtuella datorer |LinuxsyslogVer2v0 (Tabell Lagring) |
| Operativa händelser för serviceinfrastruktur |Service Fabric-noder |WADServiceFabricSystemEventTable |
| Tillförlitliga skådespelarehändelser för service fabric |Service Fabric-noder |WADServiceFabricReliableActorEventTable |
| Tillförlitliga servicehändelser för serviceinfrastruktur |Service Fabric-noder |WADServiceFabricReliableServiceEventTable |
| Windows-händelseloggar |Service Fabric-noder <br> Virtuella datorer <br> Webbroller <br> Arbetarroller |WADWindowsEventLogsTable (Tabelllagring) |
| Windows ETW-loggar |Service Fabric-noder <br> Virtuella datorer <br> Webbroller <br> Arbetarroller |WADETWEventTable (tabelllagring) |

## <a name="data-types-not-supported"></a>Datatyper stöds inte

- Prestandadata från gästoperativsystemet
- IIS-loggar från Azure-webbplatser


## <a name="enable-azure-diagnostics-extension"></a>Aktivera Azure-diagnostiktillägg
Se [Installera och konfigurera Windows Azure diagnostics extension (WAD)](diagnostics-extension-windows-install.md) eller Använd Linux Diagnostic Extension för att övervaka mått och [loggar](../../virtual-machines/extensions/diagnostics-linux.md) för information om hur du installerar och konfigurerar diagnostiktillägget. Detta kommer att alow dig att ange lagringskontot och att konfigurera insamling av de data som du vill vidarebefordra till Azure Monitor Loggar.


## <a name="collect-logs-from-azure-storage"></a>Samla in loggar från Azure Storage
Använd följande procedur för att aktivera insamling av diagnostiktilläggsdata från ett Azure Storage-konto:

1. Gå till **Log Analytics-arbetsytor** i Azure-portalen och välj arbetsyta.
1. Klicka på **Lagringskonton loggar** i avsnittet **Datakällor på** arbetsytan på menyn.
2. Klicka på **Lägg till**.
3. Välj det **lagringskonto** som innehåller de data som ska samlas in.
4. Välj den **datatyp** som du vill samla in.
5. Värdet för Källa fylls i automatiskt baserat på datatypen.
6. Spara ändringarna genom att klicka på **OK**.
7. Upprepa för ytterligare datatyper.

På cirka 30 minuter kan du se data från lagringskontot på logganalysarbetsytan. Du ser bara data som skrivs till lagring när konfigurationen har tillämpats. Arbetsytan läser inte befintliga data från lagringskontot.

> [!NOTE]
> Portalen verifierar inte att källan finns i lagringskontot eller om nya data skrivs.



## <a name="next-steps"></a>Nästa steg

* [Samla in loggar och mått för Azure-tjänster](collect-azure-metrics-logs.md) för Azure-tjänster som stöds.
* [Aktivera Lösningar](../../azure-monitor/insights/solutions.md) för att ge insikt i data.
* [Använd sökfrågor](../../azure-monitor/log-query/log-query-overview.md) för att analysera data.
