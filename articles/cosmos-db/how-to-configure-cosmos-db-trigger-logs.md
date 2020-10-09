---
title: Konfigurera och läsa loggar med Azure Functions-utlösare för Cosmos DB
description: Lär dig hur du exponerar loggarna i pipeline för Azure Functions loggning när du använder Azure Functions utlösare för Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 31c9f9d94acab7322cf11d084e4010f2defd6f3c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "85261706"
---
# <a name="how-to-configure-and-read-the-logs-when-using-azure-functions-trigger-for-cosmos-db"></a>Konfigurera och läsa loggar när du använder Azure Functions-utlösare för Cosmos DB

Den här artikeln beskriver hur du kan konfigurera din Azure Functions-miljö för att skicka Azure Functions-utlösaren för Cosmos DB loggar till den konfigurerade [övervaknings lösningen](../azure-functions/functions-monitoring.md).

## <a name="included-logs"></a>Inkluderade loggar

Azure Functions-utlösaren för Cosmos DB använder [processor biblioteket för ändrings flöden](./change-feed-processor.md) internt och biblioteket genererar en uppsättning hälso loggar som kan användas för att övervaka interna åtgärder i [fel söknings syfte](./troubleshoot-changefeed-functions.md).

Hälso loggarna beskriver hur Azure Functions utlösare för Cosmos DB fungerar vid försök att utföra åtgärder vid belastnings Utjämnings scenarier eller initiering.

## <a name="enabling-logging"></a>Aktivera loggning

Om du vill aktivera loggning när du använder Azure Functions utlösare för Cosmos DB, letar du upp `host.json` filen i Azure Functions-projektet eller Azure Functions app och [konfigurerar önskad loggnings nivå](../azure-functions/functions-monitoring.md#log-configuration-in-hostjson). Du måste aktivera spårningarna  `Host.Triggers.CosmosDB` som visas i följande exempel:

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

När Azure-funktionen har distribuerats med den uppdaterade konfigurationen visas Azure Functions utlösare för Cosmos DB loggar som en del av dina spår. Du kan visa loggarna i den konfigurerade Logging-providern under *kategorin* `Host.Triggers.CosmosDB` .

## <a name="query-the-logs"></a>Skicka frågor till loggarna

Kör följande fråga för att skicka frågor till loggarna som genereras av Azure Functions utlösare för Cosmos DB i [Azure Application insikter-analys](../azure-monitor/app/analytics.md):

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="next-steps"></a>Nästa steg

* [Aktivera övervakning](../azure-functions/functions-monitoring.md) i Azure Functions-program.
* Lär dig hur du [diagnostiserar och felsöker vanliga problem](./troubleshoot-changefeed-functions.md) när du använder Azure Functions-utlösare för Cosmos dB.
