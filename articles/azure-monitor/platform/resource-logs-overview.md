---
title: Översikt över Azures resurs loggar | Microsoft Docs
description: Förstå tjänster och händelse schema som stöds för Azures resurs loggar.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
author: rboucher
ms.author: robb
ms.date: 10/22/2019
ms.openlocfilehash: e1bf6a55568671ddb8f6999356cc9be43ce6a728
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72804023"
---
# <a name="azure-resource-logs-overview"></a>Översikt över Azure resurs loggar
Azures resurs loggar är [plattforms loggar](platform-logs-overview.md) som genereras av Azure-resurser som beskriver deras interna åtgärd. Alla resurs loggar delar ett gemensamt schema på högsta nivå med flexibiliteten för varje tjänst för att generera unika egenskaper för sina egna händelser.

> [!NOTE]
> Resurs loggar kallades tidigare för diagnostikloggar.

## <a name="collecting-resource-logs"></a>Samlar in resurs loggar
Resurs loggar genereras automatiskt av Azure-resurser som stöds, men de samlas inte in om du inte konfigurerar dem med hjälp av en [diagnostisk inställning](diagnostic-settings.md). Skapa en diagnostisk inställning för varje Azure-resurs för att vidarebefordra loggarna till följande destinationer:

| Mål | Scenario |
|:---|:---|:---|
| [Log Analytics-arbetsyta](resource-logs-collect-storage.md) | Analysera loggarna med andra övervaknings data och utnyttja Azure Monitor funktioner som logg frågor och logg aviseringar. |
| [Azure Storage](archive-diagnostic-logs.md) | Arkivera loggarna för granskning eller säkerhets kopiering. |
| [Händelsehubb](resource-logs-stream-event-hubs.md) | Strömma loggarna till loggnings-och telemetri system från tredje part.  |

## <a name="compute-resources"></a>Beräknings resurser
Resurs loggar skiljer sig från gäst loggar på operativ system nivå i Azure Compute-resurser. Beräknings resurser kräver en agent för att samla in loggar och mått från sitt gäst operativ system, inklusive sådana data som händelse loggar, syslog och prestanda räknare. Använd [Diagnostic-tillägget](agents-overview.md#azure-diagnostic-extension) för att dirigera loggdata från virtuella Azure-datorer och [Log Analytics agenten](agents-overview.md#log-analytics-agent) för att samla in loggar och mått från virtuella datorer i Azure, i andra moln och lokalt i en Log Analytics arbets yta. Mer information finns i [källor för övervaknings data för Azure Monitor](data-sources.md) .

## <a name="resource-logs-schema"></a>Schema för resurs loggar
Mer information om schema och kategorier för resurs loggar finns i [resurs logg schema](diagnostic-logs-schema.md). 

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om andra Azure Platform-loggar](platform-logs-overview.md) som du kan använda för att övervaka Azure.
