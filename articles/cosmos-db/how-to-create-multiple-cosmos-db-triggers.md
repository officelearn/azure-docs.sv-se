---
title: Skapa flera oberoende Azure Functions-utlösare för Cosmos DB
description: Lär dig hur du konfigurerar flera oberoende Azure Functions-utlösare för Cosmos DB för att skapa händelsedrivna arkitekturer.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 32b680acdee29bf97a0e132fee93d5fee3377245
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604954"
---
# <a name="create-multiple-azure-functions-triggers-for-cosmos-db"></a>Skapa flera Azure Functions-utlösare för Cosmos DB

I den här artikeln beskrivs hur du kan konfigurera flera Azure Functions-utlösare för Cosmos DB att arbeta parallellt och reagera på ändringar oberoende av varandra.

![Serverlösa händelsebaserade funktioner som arbetar med Azure Functions-utlösaren för Cosmos DB och dela en lånebehållare](./media/change-feed-functions/multi-trigger.png)

## <a name="event-based-architecture-requirements"></a>Händelsebaserade arkitekturkrav

När du skapar serverlösa arkitekturer med [Azure Functions](../azure-functions/functions-overview.md) [rekommenderas](../azure-functions/functions-best-practices.md#avoid-long-running-functions) att du skapar små funktionsuppsättningar som fungerar tillsammans i stället för stora tidskrävande funktioner.

När du skapar händelsebaserade serverlösa flöden med Azure [Functions-utlösaren för Cosmos DB,](./change-feed-functions.md)kommer du att stöta på scenariot där du vill göra flera saker när det finns en ny händelse i en viss [Azure Cosmos-behållare](./databases-containers-items.md#azure-cosmos-containers). Om åtgärder som du vill utlösa, är oberoende av varandra, skulle den idealiska lösningen vara att **skapa en Azure Functions-utlösare för Cosmos DB per åtgärd** som du vill göra, alla lyssnar efter ändringar på samma Azure Cosmos-behållare.

## <a name="optimizing-containers-for-multiple-triggers"></a>Optimera behållare för flera utlösare

Med tanke på *kraven* för Azure Functions-utlösaren för Cosmos DB behöver vi en andra behållare för att lagra tillstånd, även kallad *lånebehållaren*. Innebär det att du behöver en separat lånebehållare för varje Azure-funktion?

Här har du två alternativ:

* Skapa **en lånebehållare per funktion**: Den här metoden kan översättas till extra kostnader, såvida du inte använder en delad [dataflödesdatabas](./set-throughput.md#set-throughput-on-a-database). Kom ihåg att det minsta dataflödet på behållarnivå är 400 [begärandeenheter](./request-units.md), och när det gäller lånebehållaren används det bara för att väga förloppet och underhålla tillståndet.
* Ha **en lånebehållare och dela den** för alla dina funktioner: Det här andra alternativet utnyttjar de etablerade begärandeenheterna på behållaren bättre, eftersom det gör det möjligt för flera Azure-funktioner att dela och använda samma etablerade dataflöde.

Målet med den här artikeln är att guida dig att utföra det andra alternativet.

## <a name="configuring-a-shared-leases-container"></a>Konfigurera en behållare för delade lån

För att konfigurera behållaren för delade lån är den enda extra konfigurationen du behöver göra på dina utlösare att lägga `LeaseCollectionPrefix` till [attributet](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#attributes-and-annotations) om du använder C# eller `leaseCollectionPrefix` [attribut](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md) om du använder JavaScript. Värdet för attributet ska vara en logisk beskrivning av vad den specifika utlösaren.

Om du till exempel har tre utlösare: en som skickar e-postmeddelanden, en som gör en aggregering för att skapa en `LeaseCollectionPrefix` materialiserad vy och en som skickar ändringarna till en annan lagring, för senare analys, kan du tilldela av "e-postmeddelanden" till den första, "materialiserade" till den andra och "analytics" till den tredje.

Den viktiga delen är att alla tre utlösare **kan använda samma lånebehållarekonfiguration** (konto-, databas- och behållarnamn).

En mycket enkel kodexempel med `LeaseCollectionPrefix` attributet i C#, skulle se ut så här:

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

[FunctionName("SendEmails")]
public static void SendEmails([CosmosDBTrigger(
    databaseName: "ToDoItems",
    collectionName: "Items",
    ConnectionStringSetting = "CosmosDBConnection",
    LeaseCollectionName = "leases",
    LeaseCollectionPrefix = "emails")]IReadOnlyList<Document> documents,
    ILogger log)
{
    ...
}

[FunctionName("MaterializedViews")]
public static void MaterializedViews([CosmosDBTrigger(
    databaseName: "ToDoItems",
    collectionName: "Items",
    ConnectionStringSetting = "CosmosDBConnection",
    LeaseCollectionName = "leases",
    LeaseCollectionPrefix = "materialized")]IReadOnlyList<Document> documents,
    ILogger log)
{
    ...
}
```

Och för JavaScript kan du använda `function.json` konfigurationen `leaseCollectionPrefix` på filen, med attributet:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "CosmosDBConnection",
    "databaseName": "ToDoItems",
    "collectionName": "Items",
    "leaseCollectionPrefix": "emails"
},
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "CosmosDBConnection",
    "databaseName": "ToDoItems",
    "collectionName": "Items",
    "leaseCollectionPrefix": "materialized"
}
```

> [!NOTE]
> Övervaka alltid på de begärandeenheter som etablerats på din behållare för delade lån. Varje utlösare som delar den, kommer att öka dataflödet genomsnittlig förbrukning, så du kan behöva öka det etablerade dataflödet när du ökar antalet Azure-funktioner som använder den.

## <a name="next-steps"></a>Nästa steg

* Se den fullständiga konfigurationen för [Azure Functions-utlösaren för Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)
* Kontrollera den utökade [listan med exempel](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md) för alla språk.
* Besök serverlösa recept med Azure Cosmos DB och Azure Functions [GitHub-databasen](https://github.com/ealsur/serverless-recipes/tree/master/cosmosdbtriggerscenarios) för fler exempel.