---
title: Azure Cosmos DB utlösare för functions 2. x
description: Lär dig att använda Azure Cosmos DB-utlösaren i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: c006aa8c46864b78ae46aa9c351605cca1d1e425
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77606586"
---
# <a name="azure-cosmos-db-trigger-for-azure-functions-2x"></a>Azure Cosmos DB utlösare för Azure Functions 2. x

Azure Cosmos DB-utlösaren använder [Azure Cosmos DB ändra feed](../cosmos-db/change-feed.md) för att lyssna efter infogningar och uppdateringar över partitioner. Ändringsflöde publicerar infogningar och uppdateringar, inte borttagningar.

Information om konfiguration och konfigurations information finns i [översikten](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som anropas när det finns infogningar eller uppdateringar i den angivna databasen och samlingen.

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

# <a name="c-script"></a>[C#Över](#tab/csharp-script)

I följande exempel visas en Cosmos DB trigger-bindning i en *Function. JSON* -fil och en [ C# skript funktion](functions-reference-csharp.md) som använder bindningen. Funktionen skriver logg meddelanden när Cosmos DB poster läggs till eller ändras.

Här är bindnings data i *Function. JSON* -filen:

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

Här är C#-skriptkoden:

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en Cosmos DB trigger-bindning i en *Function. JSON* -fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen skriver logg meddelanden när Cosmos DB poster läggs till eller ändras.

Här är bindnings data i *Function. JSON* -filen:

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

Här är JavaScript-kod:

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

# <a name="python"></a>[Python](#tab/python)

I följande exempel visas en Cosmos DB trigger-bindning i en *Function. JSON* -fil och en [python-funktion](functions-reference-python.md) som använder bindningen. Funktionen skriver loggmeddelanden när Cosmos DB-poster har ändrats.

Här är bindnings data i *Function. JSON* -filen:

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

Här är python-koden:

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


I [Java Functions runtime-biblioteket](/java/api/overview/azure/functions/runtime)använder du `@CosmosDBTrigger` kommentar för parametrar vars värde kommer från Cosmos dB.  Den här anteckningen kan användas med inbyggda Java-typer, Pojo eller null-värden med hjälp av `Optional<T>`.

---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

Använd attributet [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) i [ C# klass bibliotek](functions-dotnet-class-library.md).

Attributets konstruktorn tar databasens namn och samlingens namn. Information om dessa inställningar och andra egenskaper som du kan konfigurera finns i [trigger-Configuration](#configuration). Här är ett exempel på ett `CosmosDBTrigger`-attribut i en metodsignatur:

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

Ett fullständigt exempel finns i [utlösare](#example).

# <a name="c-script"></a>[C#Över](#tab/csharp-script)

Attribut stöds inte av C# skript.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av python.

# <a name="java"></a>[Java](#tab/java)

I [Java Functions runtime-bibliotek](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)använder du `@CosmosDBInput` kommentar för parametrar som läser data från Cosmos dB.

---

## <a name="configuration"></a>Konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i filen *Function. JSON* och `CosmosDBTrigger`-attributet.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ** | Saknas | måste anges till `cosmosDBTrigger`. |
|**riktning** | Saknas | måste anges till `in`. Den här parametern anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Saknas | Variabelnamnet som används i Funktionskoden som representerar en lista över dokument med ändringar. |
|**connectionStringSetting**|**ConnectionStringSetting** | Namnet på en appinställning som innehåller anslutningssträngen som används för att ansluta till Azure Cosmos DB-kontot som övervakas. |
|**Databas**|**Databas**  | Namnet på Azure Cosmos DB-databasen med den samling som övervakas. |
|**Samling** |**Samling** | Namnet på samlingen som övervakas. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | Valfritt Namnet på en app-inställning som innehåller anslutnings strängen till det Azure Cosmos DB konto som innehåller låne samlingen. Om värdet inte anges används `connectionStringSetting` svärdet. Den här parametern anges automatiskt när bindningen skapas i portalen. Anslutningssträngen för lånsamlingen måste ha skrivbehörighet.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Valfritt) Namnet på den databas som innehåller den samling som används för att lagra lån. När inställningen inte anges används värdet för `databaseName`-inställningen. Den här parametern anges automatiskt när bindningen skapas i portalen. |
|**leaseCollectionName** | **LeaseCollectionName** | (Valfritt) Namnet på den samling som används för att lagra lån. När den inte anges används värdet `leases`. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | Valfritt När värdet är `true`skapas låne samlingen automatiskt när den inte redan finns. Standardvärdet är `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| Valfritt Definierar antalet enheter för programbegäran som ska tilldelas när låne samlingen skapas. Den här inställningen används bara när `createLeaseCollectionIfNotExists` är inställt på `true`. Den här parametern anges automatiskt när bindningen har skapats med hjälp av portalen.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| Valfritt När värdet har angetts läggs värdet till som ett prefix till de lån som skapas i leasing samlingen för den här funktionen. Genom att använda ett prefix kan två separata Azure Functions dela samma Lease-samling med olika prefix.
|**feedPollDelay**| **FeedPollDelay**| Valfritt Tiden (i millisekunder) för fördröjningen mellan avsökning av en partition för nya ändringar i flödet efter att alla aktuella ändringar har tömts. Standardvärdet är 5 000 millisekunder eller 5 sekunder.
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (Valfritt) När värdet definierar den, i millisekunder, intervallet sätta igång en uppgift att beräkna om partitioner fördelas jämnt mellan kända ha instanser. Standardvärdet är 13000 (13 sekunder).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (Valfritt) När värdet definierar den, i millisekunder, intervallet som fattas lånet om ett lån som representerar en partition. Om lånet inte förnyas inom intervallet, det gör att det upphör att gälla och ägarskap för partitionen flyttas till en annan instans. Standardvärdet är 60000 (60 sekunder).
|**leaseRenewInterval**| **LeaseRenewInterval**| (Valfritt) När värdet definierar den, i millisekunder, förnyelseintervallet för alla lån för partitioner som för tillfället hålls av en instans. Standardvärdet är 17000 (17 sekunder).
|**checkpointFrequency**| **CheckpointFrequency**| (Valfritt) När värdet definierar den, i millisekunder, hur många lån kontrollpunkter. Standardvärdet är alltid efter varje funktions anrop.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| Valfritt När den här egenskapen anges anger den här egenskapen det maximala antalet objekt som tas emot per funktions anrop. Om åtgärder i den övervakade samlingen utförs genom lagrade procedurer bevaras [transaktions omfång](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) vid läsning av objekt från ändrings flödet. Det innebär att antalet mottagna objekt kan vara högre än det angivna värdet så att objekten som ändras av samma transaktion returneras som en del av en atomisk batch.
|**startFromBeginning**| **StartFromBeginning**| Valfritt Det här alternativet anger att utlösaren ska läsa ändringar från början av samlingens ändrings historik i stället för att starta vid den aktuella tiden. Det går bara att läsa från början första gången utlösaren startar, precis som vid efterföljande körningar. kontroll punkterna är redan lagrade. Att ange det här alternativet för att `true` när det redan har skapats lån har ingen påverkan.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Användning

Utlösaren kräver en andra samling som används för att lagra _lån_ över partitionerna. Både den samling som övervakas och den samling som innehåller lån måste vara tillgänglig för utlösaren ska fungera.

>[!IMPORTANT]
> Om flera funktioner har kon figurer ATS för att använda en Cosmos DB-utlösare för samma samling, ska varje funktion använda en dedikerad Lease-samling eller ange en annan `LeaseCollectionPrefix` för varje funktion. Annars kan aktiveras endast en av funktionerna. Information om prefixet finns i [avsnittet konfiguration](#configuration).

Utlösaren anger inte om ett dokument har uppdateras eller infogas, den bara innehåller själva dokumentet. Om du vill hantera uppdateringar och infogningar på olika sätt, kan du göra det genom att implementera värdefält för infogning eller uppdaterar.

## <a name="next-steps"></a>Nästa steg

- [Läsa ett Azure Cosmos DB dokument (ingående bindning)](./functions-bindings-cosmosdb-v2-input.md)
- [Spara ändringar i ett Azure Cosmos DB dokument (utgående bindning)](./functions-bindings-cosmosdb-v2-output.md)