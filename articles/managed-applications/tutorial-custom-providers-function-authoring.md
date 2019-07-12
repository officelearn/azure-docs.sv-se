---
title: Redigering av en RESTful-slutpunkt för anpassade providers
description: Den här självstudien får du gå igenom hur du skapar en RESTful-slutpunkt för anpassade providers. Den övergår i detalj om hur du hanterar begäranden och svar för RESTful HTTP-metoder som stöds.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 176e3b02cbda7577e306d86363cfe5b41335fb6e
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800038"
---
# <a name="authoring-a-restful-endpoint-for-custom-providers"></a>Redigering av en RESTful-slutpunkt för anpassade providers

Anpassade providers kan du anpassa arbetsflöden på Azure. En anpassad provider är ett avtal mellan Azure och en `endpoint`. Den här självstudien går igenom processen med att skapa en anpassad provider RESTful `endpoint`. Om du inte känner till Azure anpassade Providers, se [översikt över anpassade resursprovidrar](./custom-providers-overview.md).

Den här självstudien är uppdelad i följande steg:

- Arbeta med anpassade åtgärder och anpassade resurser
- Hur du ska partitionera anpassade resurser i storage
- Stöd för anpassad provider RESTful-metoder
- Integrera RESTful åtgärder

Den här självstudiekursen skapar följande självstudiekurser:

- [Konfigurera Azure Functions för Azure anpassade Providers](./tutorial-custom-providers-function-setup.md)

> [!NOTE]
> Den här kursen skapar av den föregående självstudiekursen. Vissa av stegen i självstudien fungerar bara om en Azure-funktion har ställts in att arbeta med anpassade providers.

## <a name="working-with-custom-actions-and-custom-resources"></a>Arbeta med anpassade åtgärder och anpassade resurser

Vi kan uppdatera funktionen ska fungera som en RESTful-slutpunkt för vår anpassad provider i de här självstudierna. I Azure modelleras-resurser och åtgärder efter den grundläggande RESTful-specifikationen: PLACERA – skapar en ny resurs GET (instans) – hämtar en befintlig resurs, DELETE - tar bort en befintlig resurs, POST – utlösa en åtgärd och GET (samling) – visar en lista över alla befintliga resurser. Vi kommer att använda Azure-tabeller som vårt lagringsutrymme i den här självstudien, men alla databasen eller lagringsgruppen tjänster fungerar.

## <a name="how-to-partition-custom-resources-in-storage"></a>Hur du ska partitionera anpassade resurser i storage

Eftersom vi skapar en RESTful-tjänst, som vi behöver lagra de skapade resurserna. Vi måste skapa partition och radnycklar nycklar för våra data för Azure Table storage. För anpassade providers, bör data partitioneras till den anpassade providern. När en inkommande begäran skickas till den anpassade providern använder den anpassade providern lägger till den `x-ms-customproviders-requestpath` sidhuvud på utgående begäran till den `endpoint`.

exemplet `x-ms-customproviders-requestpath` rubrik för en anpassad resurs:

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

Baserat på exemplet ovan `x-ms-customproviders-requestpath` rubrik, vi kan skapa partitionKey och rowKey för vårt lagringsutrymme enligt följande:

Parameter | Mall | Beskrivning
---|---
partitionKey | '{subscriptionId}:{resourceGroupName}:{resourceProviderName}' | PartitionKey är hur data är partitionerad. I de flesta fall bör data partitioneras av anpassad provider-instans.
RowKey | '{myResourceType}:{myResourceName}' | RowKey är enskilda identifieraren för data. I de flesta fall är namnet på resursen.

Dessutom kan behöva vi också skapa en ny klass för att modellera vår anpassad resurs. I den här självstudien ska vi lägga till den `CustomResource` klass till vår funktion som är en generisk klass som tar emot någon inputted data:

```csharp
// Custom Resource Table Entity
public class CustomResource : TableEntity
{
    public string Data { get; set; }
}
```

Detta skapar en grundläggande klass baserat på `TableEntity`, som används för att lagra data. Den `CustomResource` klassen ärver två egenskaper från `TableEntity`: partitionKey och rowKey.

## <a name="support-custom-provider-restful-methods"></a>Stöd för anpassad provider RESTful-metoder

> [!NOTE]
> Om du inte kopierar koden direkt från självstudierna, svarsinnehållet ska vara giltig JSON och anger den `Content-Type` rubrik som `application/json`.

Nu när vi har data partitionsinställningar Autogenerera vi ut metoderna grundläggande CRUD och utlösare för anpassade resurser och anpassade åtgärder. Eftersom anpassade providers fungerar som proxy, begäran och svaret måste modelleras och hanteras av RESTful `endpoint`. Följ den nedan kodfragment för att hantera grundläggande RESTful åtgärder:

### <a name="trigger-custom-action"></a>Anpassad åtgärd för utlösare

För anpassade providers, en anpassad åtgärd som utlöses av `POST` begäranden. En anpassad åtgärd kan du kan också acceptera en brödtext i begäran som innehåller en uppsättning indataparametrar. Åtgärden bör sedan gå tillbaka tillbaka ett svar signally resultatet av åtgärden, samt om den lyckades eller misslyckades. I den här självstudien ska vi lägga till metoden `TriggerCustomAction` till vår funktion:

```csharp
/// <summary>
/// Triggers a custom action with some side effect.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <returns>The http response result of the custom action.</returns>
public static async Task<HttpResponseMessage> TriggerCustomAction(HttpRequestMessage requestMessage)
{
    var myCustomActionRequest = await requestMessage.Content.ReadAsStringAsync();

    var actionResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    actionResponse.Content = myCustomActionRequest != string.Empty ? 
        new StringContent(JObject.Parse(myCustomActionRequest).ToString(), System.Text.Encoding.UTF8, "application/json") :
        null;
    return actionResponse;
}
```

Den `TriggerCustomAction` metoden godkänner en inkommande begäran och helt enkelt ta bort eko tillbaka svaret med statuskoden lyckades. 

### <a name="create-custom-resource"></a>Skapa anpassade resurshanteringsprinciper

För anpassade providers, skapas en anpassad resurs via `PUT` begäranden. Den anpassade providern ska ta emot en JSON-begärandetexten, som innehåller en uppsättning egenskaper för den anpassade resursen. I Azure följer resurser en RESTful-modell. Samma begäran-URL som användes för att skapa en resurs bör också att kunna hämta och ta bort resursen. I den här självstudien ska vi lägga till metoden `CreateCustomResource` kan skapa nya resurser:

```csharp
/// <summary>
/// Creates a custom resource and saves it to table storage.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="azureResourceId">The parsed Azure resource Id.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the created custom resource.</returns>
public static async Task<HttpResponseMessage> CreateCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, ResourceId azureResourceId, string partitionKey, string rowKey)
{
    // Adds the Azure top-level properties.
    var myCustomResource = JObject.Parse(await requestMessage.Content.ReadAsStringAsync());
    myCustomResource["name"] = azureResourceId.Name;
    myCustomResource["type"] = azureResourceId.FullResourceType;
    myCustomResource["id"] = azureResourceId.Id;

    // Save the resource into storage.
    var insertOperation = TableOperation.InsertOrReplace(
        new CustomResource
        {
            PartitionKey = partitionKey,
            RowKey = rowKey,
            Data = myCustomResource.ToString(),
        });
    await tableStorage.ExecuteAsync(insertOperation);

    var createResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    createResponse.Content = new StringContent(myCustomResource.ToString(), System.Text.Encoding.UTF8, "application/json");
    return createResponse;
}
```

Den `CreateCustomResource` metoden uppdaterar den inkommande begäran till inkluderar Azure specifika fält: `id`, `name`, och `type`. Det här är översta egenskaper som används i tjänster i Azure. De kan den anpassade providern att integrera med andra tjänster som Azure Policy, Azure Resource Manager-mallar och Azure-aktivitetsloggar.

Egenskap | Exempel | Beskrivning
---|---|---
name | '{myCustomResourceName}' | Namnet på den anpassade resursen.
type | 'Microsoft.CustomProviders/resourceProviders/{resourceTypeName}' | Namnområdet för resursen.
id | '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>{resourceTypeName}/{myCustomResourceName}' | Resurs-ID.

Förutom att lägga till egenskaperna, spara vi också dokumentet till Azure Table Storage. 

### <a name="retrieve-custom-resource"></a>Hämta anpassad resurs

För anpassade providers hämtas en anpassad resurs via `GET` begäranden. Den anpassade providern kommer *inte* accepterar en JSON-begärandetexten. I fall med `GET` begäranden, den **endpoint** bör använda den `x-ms-customproviders-requestpath` huvudet för att returnera den redan skapade resursen. I den här självstudien ska vi lägga till metoden `RetrieveCustomResource` att hämta befintliga resurser:

```csharp
/// <summary>
/// Retrieves a custom resource.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the existing custom resource.</returns>
public static async Task<HttpResponseMessage> RetrieveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    var retrieveResponse = requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NotFound);

    retrieveResponse.Content = existingCustomResource != null ?
            new StringContent(existingCustomResource.Data, System.Text.Encoding.UTF8, "application/json"):
            null;
    return retrieveResponse;
}
```

Resurser bör följa en RESTful-modell i Azure. Fråge-URL som skapade resursen ska även återställa resursen om en `GET` begäran utförs.

### <a name="remove-custom-resource"></a>Ta bort anpassad resurs

För anpassade providers, en anpassad resurs tas bort via `DELETE` begäranden. Den anpassade providern kommer *inte* accepterar en JSON-begärandetexten. I fall med `DELETE` begäranden, den **endpoint** bör använda den `x-ms-customproviders-requestpath` huvudet för att ta bort den redan skapade resursen. I den här självstudien ska vi lägga till metoden `RemoveCustomResource` att ta bort befintliga resurser:

```csharp
/// <summary>
/// Removes an existing custom resource.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the result of the delete.</returns>
public static async Task<HttpResponseMessage> RemoveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    if (existingCustomResource != null) {
        var deleteOperation = TableOperation.Delete(existingCustomResource);
        await tableStorage.ExecuteAsync(deleteOperation);
    }

    return requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NoContent);
}
```

Resurser bör följa en RESTful-modell i Azure. Fråge-URL som skapade resursen bör också ta bort resursen, om en `DELETE` begäran utförs.

### <a name="list-all-custom-resources"></a>Lista över alla anpassade resurser

För anpassade providers, en lista över befintliga anpassade resurser kan att räkna upp via samling `GET` begäranden. Den anpassade providern kommer *inte* accepterar en JSON-begärandetexten. När det gäller samling `GET` begäranden, den `endpoint` bör använda den `x-ms-customproviders-requestpath` rubrik att räkna upp de redan skapade resurserna. I den här självstudien ska vi lägga till metoden `EnumerateAllCustomResources` att räkna upp de befintliga resurserna.

```csharp
/// <summary>
/// Enumerates all the stored custom resources for a given type.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="resourceType">The resource type of the enumeration.</param>
/// <returns>The http response containing a list of resources stored under 'value'.</returns>
public static async Task<HttpResponseMessage> EnumerateAllCustomResources(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string resourceType)
{
    // Generate upper bound of the query.
    var rowKeyUpperBound = new StringBuilder(resourceType);
    rowKeyUpperBound[rowKeyUpperBound.Length - 1]++;

    // Create the enumeration query.
    var enumerationQuery = new TableQuery<CustomResource>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey),
            TableOperators.And,
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, resourceType),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, rowKeyUpperBound.ToString()))));
    
    var customResources = (await tableStorage.ExecuteQuerySegmentedAsync(enumerationQuery, null))
        .ToList().Select(customResource => JToken.Parse(customResource.Data));

    var enumerationResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    enumerationResponse.Content = new StringContent(new JObject(new JProperty("value", customResources)).ToString(), System.Text.Encoding.UTF8, "application/json");
    return enumerationResponse;
}
```

> [!NOTE]
> Mer än och mindre än Azure Table-syntax för att köra en fråga ”startswith” för strängar Radnyckeln. 

För att lista alla befintliga resurser, genererar vi en Azure Table-fråga som säkerställer att resurserna som finns under vår anpassad provider-partitionen. Fråga och kontrollerar att Radnyckeln börjar med samma `{myResourceType}`.

## <a name="integrate-restful-operations"></a>Integrera RESTful åtgärder

När alla RESTful metoder läggs till funktionen, kan vi uppdatera huvudsakliga `Run` metod för att anropa funktionerna för att hantera olika REST-begäranden:

```csharp
/// <summary>
/// Entry point for the Azure Function webhook and acts as the service behind a custom provider.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="log">The logger.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <returns>The http response for the custom Azure API.</returns>
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log, CloudTable tableStorage)
{
    // Get the unique Azure request path from request headers.
    var requestPath = req.Headers.GetValues("x-ms-customproviders-requestpath").FirstOrDefault();

    if (requestPath == null)
    {
        var missingHeaderResponse = req.CreateResponse(HttpStatusCode.BadRequest);
        missingHeaderResponse.Content = new StringContent(
            new JObject(new JProperty("error", "missing 'x-ms-customproviders-requestpath' header")).ToString(),
            System.Text.Encoding.UTF8, 
            "application/json");
    }

    log.LogInformation($"The Custom Provider Function received a request '{req.Method}' for resource '{requestPath}'.");

    // Determines if it is a collection level call or action.
    var isResourceRequest = requestPath.Split('/').Length % 2 == 1;
    var azureResourceId = isResourceRequest ? 
        ResourceId.FromString(requestPath) :
        ResourceId.FromString($"{requestPath}/");

    // Create the Partition Key and Row Key
    var partitionKey = $"{azureResourceId.SubscriptionId}:{azureResourceId.ResourceGroupName}:{azureResourceId.Parent.Name}";
    var rowKey = $"{azureResourceId.FullResourceType.Replace('/', ':')}:{azureResourceId.Name}";

    switch (req.Method)
    {
        // Action request for an custom action.
        case HttpMethod m when m == HttpMethod.Post && !isResourceRequest:
            return await TriggerCustomAction(
                requestMessage: req);

        // Enumerate request for all custom resources.
        case HttpMethod m when m == HttpMethod.Get && !isResourceRequest:
            return await EnumerateAllCustomResources(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                resourceType: rowKey);

        // Retrieve request for a custom resource.
        case HttpMethod m when m == HttpMethod.Get && isResourceRequest:
            return await RetrieveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Create request for a custom resource.
        case HttpMethod m when m == HttpMethod.Put && isResourceRequest:
            return await CreateCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                azureResourceId: azureResourceId,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Remove request for a custom resource.
        case HttpMethod m when m == HttpMethod.Delete && isResourceRequest:
            return await RemoveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Invalid request received.
        default:
            return req.CreateResponse(HttpStatusCode.BadRequest);
    }
}
``` 

Den uppdaterade `Run` metoden innehåller nu den `tableStorage` indatabindning som har lagts till för Azure Table storage. Den första delen av metoden kommer nu att läsa den `x-ms-customproviders-requestpath` rubrik och Använd den `Microsoft.Azure.Management.ResourceManager.Fluent` biblioteket för att parsa värdet som en resurs-ID. Den `x-ms-customproviders-requestpath` rubrik som skickas av den anpassade providern och anger sökvägen till den inkommande begäranden. Med parsad resurs-ID kan skapa vi nu partitionKey och rowKey för data att söka efter eller lagra anpassade resurser.

Förutom att lägga till metoder och klasser, som vi behöver uppdatera med metoder för funktionen. Lägg till följande överst i filen:

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.WindowsAzure.Storage"
#r "../bin/Microsoft.Azure.Management.ResourceManager.Fluent.dll"

using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Configuration;
using System.Text;
using System.Threading;
using System.Globalization;
using System.Collections.Generic;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Om du har fått gå förlorade under den här självstudien slutförda kodexemplet finns på den [anpassad provider C# RESTful-slutpunkt referens](./reference-custom-providers-csharp-endpoint.md). När funktionen är klar kan du spara funktions-URL som kan användas för att utlösa funktionen eftersom den används i senare självstudier.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har vi skapat en RESTful-slutpunkt ska fungera med Azure anpassad Provider `endpoint`. Gå till nästa artikel om du vill lära dig hur du skapar en anpassad provider.

- [Självstudier: Skapa en anpassad provider](./tutorial-custom-providers-create.md)
