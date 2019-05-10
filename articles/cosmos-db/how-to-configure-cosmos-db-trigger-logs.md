---
title: Azure Cosmos DB-utlösare-loggar
description: Lär dig hur du kan exponera Azure Cosmos DB-utlösare-loggar till din Azure-Functions loggning pipeline
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 5/2/2019
ms.author: maquaran
ms.openlocfilehash: a598842ecde9508a6c2185a6097f689252fda60c
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65515083"
---
# <a name="how-to-configure-and-read-the-azure-cosmos-db-trigger-logs"></a>Hur du konfigurerar och läsa loggar för Azure Cosmos DB-utlösare

Den här artikeln beskrivs hur du kan konfigurera din miljö för Azure Functions för att skicka loggar med Azure Cosmos DB-utlösare till konfigurerade [övervakningslösning](../azure-functions/functions-monitoring.md).

## <a name="included-logs"></a>Inkluderade loggar

Azure Cosmos DB-utlösaren använder den [ändra Feed Processor-biblioteket](./change-feed-processor.md) internt och biblioteket genererar en uppsättning hälsotillstånd loggar som kan användas för att övervaka interna åtgärder för [felsökningssyfte](./troubleshoot-changefeed-functions.md).

Hälsotillstånd loggarna beskrivs hur Azure Cosmos DB-utlösare beter sig vid åtgärder vid scenarier för belastningsutjämning eller initieringen.

## <a name="enabling-logging"></a>Aktivera loggning

För att aktivera loggning för Azure Cosmos DB-utlösare, letar du upp den `host.json` filen i Azure Functions-projekt eller Azure Functions-App och [konfigurera nödvändiga loggningsnivå](../azure-functions/functions-monitoring.md#log-configuration-in-hostjson). Du måste aktivera spårningar för `Host.Triggers.CosmosDB` som visas i följande exempel:

```js
{
  "version": "2.0",
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "Host.Triggers.CosmosDB": "Trace"
    }
  }
}
```

När Azure Function har distribuerats med den uppdaterade konfigurationen kan visas i Azure Cosmos DB-utlösare-loggarna som en del av dina spårningar. Du kan visa loggarna i dina konfigurerade loggningsprovider under den *kategori* `Host.Triggers.CosmosDB`.

## <a name="query-the-logs"></a>Fråga loggarna

Kör följande fråga för att fråga loggarna som genereras av Azure Cosmos DB-utlösare i [Azure Application Insights Analytics](../azure-monitor/app/analytics.md):

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="next-steps"></a>Nästa steg

* [Aktivera övervakning](../azure-functions/functions-monitoring.md) i dina program med Azure Functions.
* Lär dig hur du [diagnostisera och felsöka vanliga problem med](./troubleshoot-changefeed-functions.md) när du använder Azure Cosmos DB-utlösare i Azure Functions.