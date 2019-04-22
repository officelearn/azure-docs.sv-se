---
title: Så här skapar du flera oberoende Azure Cosmos DB-utlösare
description: Lär dig hur du konfigurerar flera oberoende Azure Cosmos DB-utlösare för att skapa en händelsedriven arkitektur för Azure Functions.
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 4/15/2019
ms.author: maquaran
ms.openlocfilehash: 7a47a928e97d52535a6d3baa808f1fcb81d9bb55
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59700145"
---
# <a name="create-multiple-azure-cosmos-db-triggers"></a>Skapa flera Azure Cosmos DB-utlösare

Den här artikeln beskrivs hur du kan konfigurera flera Cosmos DB-utlösare för att arbeta parallellt och oberoende reagera på förändringar.

![Funktioner utan Server händelsebaserad arbeta med Azure Cosmos DB-utlösare och dela en behållare för lån](./media/change-feed-functions/multi-trigger.png)

## <a name="event-based-architecture-requirements"></a>Händelsebaserad arkitektur krav

När du skapar arkitekturer utan server med [Azure Functions](../azure-functions/functions-overview.md), den har [rekommenderas](../azure-functions/functions-best-practices.md#avoid-long-running-functions) att skapa små funktionen uppsättningar som arbetar tillsammans i stället för stora tidskrävande funktioner.

När du skapar händelsebaserad serverlös flöden med hjälp av den [Azure Cosmos DB-utlösare](./change-feed-functions.md), kör i ett scenario där du vill göra flera saker, när det finns en ny händelse i en viss [Azure Cosmos-behållaren](./databases-containers-items.md#azure-cosmos-containers). Om åtgärder som du vill ska utlösa, oberoende av varandra, är den perfekta lösningen att **skapar en Cosmos DB-utlösare per åtgärd** du vill göra allt lyssna efter ändringar på samma Azure-Cosmos-behållare.

## <a name="optimizing-containers-for-multiple-triggers"></a>Optimera behållare för flera utlösare

Beroende på *krav* i Cosmos DB-utlösare, behöver vi en andra behållare för att lagra tillstånd, även kallat den *lån behållare*. Innebär det att du behöver en separat lån behållare för varje Azure-funktion?

Här har du två alternativ:

* Skapa **ett lån behållare per funktion**: Den här metoden kan översättas till ytterligare kostnader, om du inte använder en [delade dataflöde databasen](./set-throughput.md#set-throughput-on-a-database). Kom ihåg att det lägsta dataflöden på behållarenivån är 400 [programbegäran](./request-units.md), och när det gäller behållaren lån endast de används för att kontrollpunkten förloppet och upprätthålla tillstånd.
* Har **en låna ut behållare och dela den** för alla funktioner: Det här alternativet om andra gör bättre användning av den etablerade begäranden för behållaren eftersom det ger flera Azure Functions för att dela och använda det etablerade dataflödet som samma.

Målet med den här artikeln är att guida dig för att utföra det andra alternativet.

## <a name="configuring-a-shared-leases-container"></a>Konfigurera en delad lån-behållare

Om du vill konfigurera delade lån behållaren endast extra konfigurationen måste du göra på din utlösare är att lägga till den `LeaseCollectionPrefix` [attributet](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---c-attributes) om du använder C# eller `leaseCollectionPrefix` [attributet](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---javascript-example)om du använder JavaScript. Värdet för attributet ska vara en logisk beskrivning av den aktuella utlösaren.

Exempel: Om du har tre utlösare: en som skickar e-postmeddelanden, en som utför en aggregering för att skapa en materialiserad vy och en som skickar ändringarna till en annan lagringsplats för senare analys kan du tilldela den `LeaseCollectionPrefix` av ”e-postmeddelanden” till den första ”, materialiseras ”till den andra, och” analytics ”till den tredje.

Den viktiga delen är att alla tre utlöser **kan använda samma lån behållarkonfigurationen** (konto, databas och container-name).

Ett enkelt kodexempel som använder den `LeaseCollectionPrefix` attributet i C#, skulle se ut så här:

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

Och för JavaScript, kan du tillämpa konfigurationen på den `function.json` filen med den `leaseCollectionPrefix` attribut:

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
> Övervaka alltid på programbegäran har etablerats på din delade lån-behållare. Varje utlösare som delar den, ökar dataflödet genomsnittlig förbrukning, så att du kan behöva öka det etablerade dataflödet som när du ökar antalet Azure-funktioner som använder den.

## <a name="next-steps"></a>Nästa steg

* Se den fullständiga konfigurationen för den [Azure Cosmos DB-utlösare](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration)
* Kontrollera den utökade [listan med exempel](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---example) för alla språk.
* Besök med Azure Cosmos DB och Azure Functions utan Server recept [GitHub-lagringsplatsen](https://github.com/ealsur/serverless-recipes/tree/master/cosmosdbtriggerscenarios) för fler exempel.