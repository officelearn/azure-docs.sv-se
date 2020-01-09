---
title: Skapa flera oberoende Azure Functions-utlösare för Cosmos DB
description: Lär dig hur du konfigurerar flera oberoende Azure Functions-utlösare för Cosmos DB för att skapa händelse drivna arkitekturer.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: fbf1e11d7a283ca6c93356f055198c35350e0332
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445358"
---
# <a name="create-multiple-azure-functions-triggers-for-cosmos-db"></a>Skapa flera Azure Functions utlösare för Cosmos DB

I den här artikeln beskrivs hur du kan konfigurera flera Azure Functions-utlösare för Cosmos DB att arbeta parallellt och reagera på ändringar oberoende av varandra.

![Server lösa händelsebaserade funktioner som fungerar med Azure Functions-utlösaren för Cosmos DB och delning av en behållare för lån](./media/change-feed-functions/multi-trigger.png)

## <a name="event-based-architecture-requirements"></a>Händelsebaserade arkitektur krav

När du skapar Server lösa arkitekturer med [Azure Functions](../azure-functions/functions-overview.md) [rekommenderar](../azure-functions/functions-best-practices.md#avoid-long-running-functions) vi att du skapar små funktions uppsättningar som fungerar tillsammans i stället för stora tids krävande funktioner.

När du skapar händelsebaserade Server lösa flöden med [Azure Functions utlösare för Cosmos DB](./change-feed-functions.md), kan du köra i scenariot där du vill göra flera saker när det finns en ny händelse i en viss [Azure Cosmos-behållare](./databases-containers-items.md#azure-cosmos-containers). Om åtgärder som du vill utlösa är oberoende av varandra, är den perfekta lösningen att **skapa en Azure Functions utlösare för Cosmos dB per åtgärd** som du vill göra, alla som lyssnar efter ändringar i samma Azure Cosmos-behållare.

## <a name="optimizing-containers-for-multiple-triggers"></a>Optimera behållare för flera utlösare

Med tanke på *kraven* i Azure Functions-utlösaren för Cosmos DB behöver vi en andra behållare för att lagra tillstånd, även kallade *leasing avtal*. Innebär det att du behöver en separat container för varje Azure-funktion?

Här har du två alternativ:

* Skapa **en container container per funktion**: den här metoden kan översättas till ytterligare kostnader, om du inte använder en [delad data flödes databas](./set-throughput.md#set-throughput-on-a-database). Kom ihåg att det lägsta data flödet på behållar nivån är 400 [enheter för programbegäran](./request-units.md), och när det gäller leasing behållaren, används den bara för att ange en kontroll punkt för status och underhåll.
* Ha **en container och dela den** för alla dina funktioner: det andra alternativet gör att de allokerade enheterna i behållaren bättre används, eftersom det gör att flera Azure Functions kan dela och använda samma allokerade data flöde.

Syftet med den här artikeln är att hjälpa dig att utföra det andra alternativet.

## <a name="configuring-a-shared-leases-container"></a>Konfigurera en behållare för delade lån

Om du vill konfigurera behållaren för delade lån måste du lägga till `LeaseCollectionPrefix` [attributet](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---c-attributes) om du använder C# eller `leaseCollectionPrefix`- [attribut](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---javascript-example) om du använder Java Script. Värdet för attributet ska vara en logisk Beskrivning av vad som är en viss utlösare.

Om du till exempel har tre utlösare: en som skickar e-post, en agg regering för att skapa en materialiserad vy och en som skickar ändringarna till en annan lagrings plats, kan du tilldela `LeaseCollectionPrefix` "e-post" till den första, "materialiserade" till den andra, och "analys" till den tredje.

Den viktiga delen är att alla tre utlösare **kan använda samma konfiguration för leasing avtals behållare** (konto, databas och behållar namn).

Ett enkelt kod exempel som använder `LeaseCollectionPrefix`-attributet i C#, skulle se ut så här:

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

Och för Java Script kan du tillämpa konfigurationen på `function.json`-filen med attributet `leaseCollectionPrefix`:

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
> Övervaka alltid på de enheter för programbegäran som etablerades i behållaren för delade lån. Varje utlösare som delar den ökar data flödets genomsnittliga förbrukning, så du kan behöva öka det etablerade data flödet när du ökar antalet Azure Functions som använder den.

## <a name="next-steps"></a>Nästa steg

* Se den fullständiga konfigurationen för [Azure Functions utlösare för Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration)
* Kontrol lera den utökade [listan över exempel](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---example) för alla språk.
* Besök de serverbaserade recepten med Azure Cosmos DB och Azure Functions [GitHub-lagringsplatsen](https://github.com/ealsur/serverless-recipes/tree/master/cosmosdbtriggerscenarios) för fler exempel.