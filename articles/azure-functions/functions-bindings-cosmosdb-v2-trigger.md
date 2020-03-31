---
title: Azure Cosmos DB-utlösare för funktioner 2.x
description: Lär dig att använda Azure Cosmos DB-utlösaren i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: de8ad39ef731af3dc272d700eeee346acda64b53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277575"
---
# <a name="azure-cosmos-db-trigger-for-azure-functions-2x"></a>Azure Cosmos DB-utlösare för Azure Functions 2.x

Azure Cosmos DB-utlösaren använder [Azure Cosmos DB Change Feed](../cosmos-db/change-feed.md) för att lyssna efter infogningar och uppdateringar över partitioner. Ändringsflödet publicerar infogningar och uppdateringar, inte borttagningar.

Information om inställnings- och konfigurationsinformation finns i [översikten](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som anropas när det finns infogningar eller uppdateringar i den angivna databasen och samlingen.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "ToDoItems",
            collectionName: "Items",
            ConnectionStringSetting = "CosmosDBConnection",
            LeaseCollectionName = "leases",
            CreateLeaseCollectionIfNotExists = true)]IReadOnlyList<Document> documents,
            ILogger log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.LogInformation($"Documents modified: {documents.Count}");
                log.LogInformation($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

I följande exempel visas en Cosmos DB-utlösare som binder i en *function.json-fil* och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen skriver loggmeddelanden när Cosmos DB-poster läggs till eller ändras.

Här är bindningsdata i *filen function.json:*

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Här är C# skriptkoden:

```cs
    #r "Microsoft.Azure.DocumentDB.Core"

    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using Microsoft.Extensions.Logging;

    public static void Run(IReadOnlyList<Document> documents, ILogger log)
    {
      log.LogInformation("Documents modified " + documents.Count);
      log.LogInformation("First document Id " + documents[0].Id);
    }
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

I följande exempel visas en Cosmos DB-utlösare som binder i en *function.json-fil* och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen skriver loggmeddelanden när Cosmos DB-poster läggs till eller ändras.

Här är bindningsdata i *filen function.json:*

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Här är JavaScript-koden:

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

# <a name="python"></a>[Python](#tab/python)

I följande exempel visas en Cosmos DB-utlösare som binder i en *function.json-fil* och en [Python-funktion](functions-reference-python.md) som använder bindningen. Funktionen skriver loggmeddelanden när Cosmos DB-poster ändras.

Här är bindningsdata i *filen function.json:*

```json
{
    "name": "documents",
    "type": "cosmosDBTrigger",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Här är Python-koden:

```python
    import logging
    import azure.functions as func


    def main(documents: func.DocumentList) -> str:
        if documents:
            logging.info('First document Id modified: %s', documents[0]['id'])
```

# <a name="java"></a>[Java](#tab/java)

Den här funktionen anropas när det finns infogningar eller uppdateringar i den angivna databasen och samlingen.

```java
    @FunctionName("cosmosDBMonitor")
    public void cosmosDbProcessor(
        @CosmosDBTrigger(name = "items",
            databaseName = "ToDoList",
            collectionName = "Items",
            leaseCollectionName = "leases",
            createLeaseCollectionIfNotExists = true,
            connectionStringSetting = "AzureCosmosDBConnection") String[] items,
            final ExecutionContext context ) {
                context.getLogger().info(items.length + "item(s) is/are changed.");
            }
```


I [Java-funktionernas körningsbibliotek](/java/api/overview/azure/functions/runtime)använder du anteckningen `@CosmosDBTrigger` på parametrar vars värde skulle komma från Cosmos DB.  Den här anteckningen kan användas med inbyggda Java-typer, `Optional<T>`POJOs eller nullable värden med .

---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

I [klassbibliotek för C#](functions-dotnet-class-library.md)använder du attributet [CosmosDBTrigger.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs)

Attributets konstruktor tar databasnamnet och samlingsnamnet. Information om dessa inställningar och andra egenskaper som du kan konfigurera finns i [Utlösare - konfiguration](#configuration). Här är `CosmosDBTrigger` ett attributexempel i en metodsignatur:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        ILogger log)
    {
        ...
    }
```

Ett fullständigt exempel finns i [Utlösare](#example).

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Attribut stöds inte av C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Attribut stöds inte av JavaScript.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av Python.

# <a name="java"></a>[Java](#tab/java)

Använd anteckningen på parametrar som `@CosmosDBInput` läser data från Cosmos DB från [Java-funktionernas körningsbibliotek.](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)

---

## <a name="configuration"></a>Konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `CosmosDBTrigger` du anger i *filen function.json* och attributet.

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
|**Typ** | Saknas | Måste ställas `cosmosDBTrigger`in på . |
|**riktning** | Saknas | Måste ställas `in`in på . Den här parametern ställs in automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Saknas | Variabelnamnet som används i funktionskoden och som representerar listan över dokument med ändringar. |
|**anslutningStringInställning**|**ConnectionStringSetting** | Namnet på en appinställning som innehåller anslutningssträngen som används för att ansluta till Azure Cosmos DB-kontot som övervakas. |
|**Databasename**|**DatabaseName**  | Namnet på Azure Cosmos DB-databasen med samlingen som övervakas. |
|**collectionName (samlingsnamn)** |**CollectionName** | Namnet på den samling som övervakas. |
|**leConnectionStringSetting** | **LeaseConnectionStringSetting** | (Valfritt) Namnet på en appinställning som innehåller anslutningssträngen till Azure Cosmos DB-kontot som innehåller lånesamlingen. När värdet inte `connectionStringSetting` har angetts används det. Den här parametern ställs in automatiskt när bindningen skapas i portalen. Anslutningssträngen för lånesamlingen måste ha skrivbehörighet.|
|**leasaDatabaseName** |**LeaseDatabaseName** | (Valfritt) Namnet på databasen som innehåller samlingen som används för att lagra lån. När inställningen inte har `databaseName` ställts in används värdet för inställningen. Den här parametern ställs in automatiskt när bindningen skapas i portalen. |
|**leaseCollectionName** | **LeaseCollectionName** | (Valfritt) Namnet på den samling som används för att lagra leasingavtal. När värdet inte `leases` har angetts används det. |
|**skapaLeaseCollectionIfNotExists** | **SkapaLeaseCollectionIfNotExists** | (Valfritt) När den `true`är inställd på skapas lånesamlingen automatiskt när den inte redan finns. Standardvärdet är `false`. |
|**leasingerCollectionThroughput**| **LeasingUppsamlingGenomgenomströmning**| (Valfritt) Definierar antalet begärandeenheter som ska tilldelas när lånesamlingen skapas. Den här inställningen `createLeaseCollectionIfNotExists` används bara `true`när den är inställd på . Den här parametern ställs in automatiskt när bindningen skapas med hjälp av portalen.
|**leCollectionPrefix**| **LeaseCollectionPrefix**| (Valfritt) När värdet anges läggs värdet som ett prefix till de lån som skapats i lånesamlingen för den här funktionen. Med hjälp av ett prefix kan två separata Azure-funktioner dela samma Lånesamling med hjälp av olika prefix.
|**feedPollDelay**| **FeedPollDelay**| (Valfritt) Tiden (i millisekunder) för fördröjningen mellan avsökning av en partition för nya ändringar i flödet, när alla aktuella ändringar har tömts. Standard är 5 000 millisekunder eller 5 sekunder.
|**lendeRättInterval**| **LeaseAcquireInterval**| (Valfritt) När den är inställd definierar den i millisekunder intervallet för att starta en uppgift för att beräkna om partitioner fördelas jämnt mellan kända värdinstanser. Standard är 13000 (13 sekunder).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (Valfritt) När den är inställd definierar den i millisekunder det intervall för vilket lånet tas på ett lån som representerar en partition. Om lånet inte förnyas inom det här intervallet kommer det att leda till att det upphör att gälla och ägarskapet av partitionen flyttas till en annan instans. Standard är 60000 (60 sekunder).
|**leasingRenewInterval**| **LeaseRenewInterval**| (Valfritt) När den är inställd definierar den i millisekunder förnyelseintervallet för alla lån för partitioner som för närvarande innehas av en instans. Standard är 17000 (17 sekunder).
|**checkpointFrequency**| **KontrollpunktFrekvent**| (Valfritt) När den är inställd definierar den i millisekunder intervallet mellan lånekontrollpunkter. Standard är alltid efter varje funktionsanrop.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| (Valfritt) När den här egenskapen anges anger den maximala antalet artiklar som tas emot per funktionsanrop. Om åtgärder i den övervakade samlingen utförs genom lagrade procedurer bevaras [transaktionsomfattningen](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) när du läser objekt från ändringsflödet. Därför kan antalet mottagna artiklar vara högre än det angivna värdet så att artiklarna som ändras av samma transaktion returneras som en del av en atombatch.
|**startFrånBeginning**| **StartFrånBesning**| (Valfritt) Med det här alternativet visas ändringar från början av samlingens ändringshistorik i stället för att börja vid den aktuella tidpunkten. Läsning från början fungerar bara första gången utlösaren startar, som i efterföljande körningar, är kontrollpunkterna redan lagrade. Att ange `true` det här alternativet till när det redan har skapats lån har ingen effekt. |
|**preferredLocations**| **PreferredLocations**| (Valfritt) Definierar önskade platser (regioner) för geo-replikerade databaskonton i Azure Cosmos DB-tjänsten. Värdena ska kommaavskiljas. Till exempel "Östra USA, Södra centrala USA,Nordeuropa". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Användning

Utlösaren kräver en andra samling som den använder för att lagra _lån_ över partitionerna. Både samlingen som övervakas och samlingen som innehåller lånen måste vara tillgängliga för att utlösaren ska fungera.

>[!IMPORTANT]
> Om flera funktioner är konfigurerade för att använda en Cosmos DB-utlösare för samma samling, `LeaseCollectionPrefix` bör var och en av funktionerna använda en dedikerad lånesamling eller ange en annan för varje funktion. Annars utlöses bara en av funktionerna. Information om prefixet finns i [avsnittet Konfiguration](#configuration).

Utlösaren anger inte om ett dokument har uppdaterats eller infogats, utan själva dokumentet. Om du behöver hantera uppdateringar och infogar olika kan du göra det genom att implementera tidsstämpelfält för insättning eller uppdatering.

## <a name="next-steps"></a>Nästa steg

- [Läsa ett Azure Cosmos DB-dokument (indatabindning)](./functions-bindings-cosmosdb-v2-input.md)
- [Spara ändringar i ett Azure Cosmos DB-dokument (utdatabindning)](./functions-bindings-cosmosdb-v2-output.md)