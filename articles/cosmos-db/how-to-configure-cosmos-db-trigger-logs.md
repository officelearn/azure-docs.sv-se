---
title: Konfigurera och läsa loggar med Azure Functions-utlösare för Cosmos DB
description: Lär dig hur du exponerar loggarna för Azure Functions loggningspipeline när du använder Azure Functions-utlösaren för Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 5ff747b225f8984bcaafd80015e85a9f014bdb50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441833"
---
# <a name="how-to-configure-and-read-the-logs-when-using-azure-functions-trigger-for-cosmos-db"></a>Konfigurera och läsa loggarna när du använder Azure Functions-utlösaren för Cosmos DB

I den här artikeln beskrivs hur du kan konfigurera din Azure Functions-miljö för att skicka Azure Functions-utlösaren för Cosmos DB-loggar till din konfigurerade [övervakningslösning](../azure-functions/functions-monitoring.md).

## <a name="included-logs"></a>Inkluderade loggar

Azure Functions-utlösaren för Cosmos DB använder [ändringsflödesprocessorbiblioteket](./change-feed-processor.md) internt och biblioteket genererar en uppsättning hälsologgar som kan användas för att övervaka interna åtgärder för [felsökning .](./troubleshoot-changefeed-functions.md)

Hälsologgarna beskriver hur Azure Functions-utlösaren för Cosmos DB beter sig vid försöksåtgärder under belastningsutjämningsscenarier eller initiering.

## <a name="enabling-logging"></a>Aktivera loggning

Om du vill aktivera loggning när du använder `host.json` Azure Functions-utlösaren för Cosmos DB letar du reda på filen i azure functions-projektet eller Azure Functions App och [konfigurerar nivån för nödvändig loggning](../azure-functions/functions-monitoring.md#log-configuration-in-hostjson). Du måste aktivera spårningarna `Host.Triggers.CosmosDB` för som visas i följande exempel:

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

När Azure-funktionen har distribuerats med den uppdaterade konfigurationen ser du Azure Functions-utlösaren för Cosmos DB-loggar som en del av dina spårningar. Du kan visa loggarna i din konfigurerade loggningsprovider under *kategorin* `Host.Triggers.CosmosDB`.

## <a name="query-the-logs"></a>Fråga loggarna

Kör följande fråga för att fråga loggarna som genereras av Azure Functions-utlösaren för Cosmos DB i [Azure Application Insights Analytics:](../azure-monitor/app/analytics.md)

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="next-steps"></a>Nästa steg

* [Aktivera övervakning](../azure-functions/functions-monitoring.md) i dina Azure Functions-program.
* Lär dig hur du [diagnostiserar och felsöker vanliga problem](./troubleshoot-changefeed-functions.md) när du använder Azure Functions-utlösaren för Cosmos DB.