---
title: Skapa en RESTful-slutpunkt
description: Den här självstudien visar hur du skapar en RESTful-slutpunkt för anpassade leverantörer. Den beskriver hur du hanterar begäranden och svar för de RESTful HTTP-metoder som stöds.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: d7f6c51211ce0572797ade659b9316003502da1f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75650025"
---
# <a name="author-a-restful-endpoint-for-custom-providers"></a>Skapa en RESTful-slutpunkt för anpassade leverantörer

En anpassad provider är ett kontrakt mellan Azure och en slutpunkt. Med anpassade leverantörer kan du anpassa arbetsflöden på Azure. Den här självstudien visar hur du skapar en anpassad leverantör RESTful slutpunkt. Om du inte känner till Azure Custom Providers läser du [översikten över anpassade resursleverantörer](overview.md).

> [!NOTE]
> Den här självstudien bygger på självstudien [Konfigurera Azure-funktioner för Azure Custom Providers](./tutorial-custom-providers-function-setup.md). Några av stegen i den här självstudien fungerar bara om en Azure-funktionsapp har konfigurerats för att fungera med anpassade leverantörer.

## <a name="work-with-custom-actions-and-custom-resources"></a>Arbeta med anpassade åtgärder och anpassade resurser

I den här självstudien uppdaterar du funktionsappen så att den fungerar som en RESTful-slutpunkt för din anpassade leverantör. Resurser och åtgärder i Azure modelleras efter följande grundläggande RESTful-specifikation:

- **PUT**: Skapa en ny resurs
- **GET (instans)**: Hämta en befintlig resurs
- **DELETE**: Ta bort en befintlig resurs
- **POST**: Utlösa en åtgärd
- **GET (samling)**: Lista alla befintliga resurser

 För den här självstudien använder du Azure Table storage. Men alla databaser eller lagringstjänster kan fungera.

## <a name="partition-custom-resources-in-storage"></a>Partitionera anpassade resurser i lagring

Eftersom du skapar en RESTful-tjänst måste du lagra de skapade resurserna. För Azure Table-lagring måste du generera partitions- och radnycklar för dina data. För anpassade leverantörer bör data partitioneras till den anpassade providern. När en inkommande begäran skickas till den anpassade `x-ms-customproviders-requestpath` providern lägger den anpassade providern till huvudet i utgående begäranden till slutpunkten.

I följande exempel `x-ms-customproviders-requestpath` visas ett sidhuvud för en anpassad resurs:

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

Baserat på exemplets `x-ms-customproviders-requestpath` rubrik kan du skapa parametern *partitionKey* och *rowKey* för lagring enligt följande tabell:

Parameter | Mall | Beskrivning
---|---|---
*partitionKey (partitionKey)* | `{subscriptionId}:{resourceGroupName}:{resourceProviderName}` | Parametern *partitionKey* anger hur data partitioneras. Vanligtvis partitioneras data av den anpassade providerinstansen.
*radNyckel* | `{myResourceType}:{myResourceName}` | Parametern *rowKey* anger den enskilda identifieraren för data. Vanligtvis är identifieraren namnet på resursen.

Du måste också skapa en ny klass för att modellera din anpassade resurs. I den här självstudien lägger du till följande **CustomResource-klass** i funktionsappen:

```csharp
// Custom Resource Table Entity
public class CustomResource : TableEntity
{
    public string Data { get; set; }
}
```
**CustomResource** är en enkel, allmän klass som accepterar alla indata. Den är baserad på **TableEntity**, som används för att lagra data. Klassen **CustomResource** ärver två egenskaper från **TableEntity:** **partitionKey** och **rowKey**.

## <a name="support-custom-provider-restful-methods"></a>Stöd för restful-metoder för anpassad leverantör

> [!NOTE]
> Om du inte kopierar koden direkt från den här självstudien måste `Content-Type` svarsinnehållet vara giltigt JSON som anger rubriken till `application/json`.

Nu när du har ställt in datapartitionering skapar du de grundläggande CRUD- och utlösa metoderna för anpassade resurser och anpassade åtgärder. Eftersom anpassade leverantörer fungerar som proxyservrar måste DEN RESTful-slutpunkten modellera och hantera begäran och svaret. Följande kodavsnitt visar hur du hanterar de grundläggande RESTful-åtgärderna.

### <a name="trigger-a-custom-action"></a>Utlösa en anpassad åtgärd

För anpassade leverantörer utlöses en anpassad åtgärd via POST-begäranden. En anpassad åtgärd kan eventuellt acceptera en begärandetext som innehåller en uppsättning indataparametrar. Åtgärden returnerar sedan ett svar som signalerar resultatet av åtgärden och om den lyckades eller misslyckades.

Lägg till följande **TriggerCustomAction-metod** i funktionsappen:

```csharp
/// <summary>
/// Triggers a custom action with some side effects.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <returns>The HTTP response result of the custom action.</returns>
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

**Metoden TriggerCustomAction** accepterar en inkommande begäran och ekar helt enkelt tillbaka svaret med en statuskod.

### <a name="create-a-custom-resource"></a>Skapa en anpassad resurs

För anpassade leverantörer skapas en anpassad resurs via PUT-begäranden. Den anpassade providern accepterar en JSON-begärandetext, som innehåller en uppsättning egenskaper för den anpassade resursen. Resurser i Azure följer en RESTful-modell. Du kan använda samma URL för begäran för att skapa, hämta eller ta bort en resurs.

Lägg till följande **CreateCustomResource-metod** för att skapa nya resurser:

```csharp
/// <summary>
/// Creates a custom resource and saves it to table storage.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="azureResourceId">The parsed Azure resource ID.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the created custom resource.</returns>
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

Metoden **CreateCustomResource** uppdaterar den inkommande begäran så att den innehåller Azure-specifika **fält-ID,** **namn**och **typ**. Dessa fält är egenskaper på högsta nivå som används av tjänster i Azure. De låter den anpassade providern samverka med andra tjänster som Azure Policy, Azure Resource Manager-mallar och Azure Activity Log.

Egenskap | Exempel | Beskrivning
---|---|---
**Namn** | {myCustomResourceName} | Namnet på den anpassade resursen
**Typ** | Microsoft.CustomProviders/resourceProviders/{resourceTypeName} | Namnområdet för resurstyp
**Id** | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>leverantörer/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>{resourceTypeName}/{myCustomResourceName} | Resurs-ID:et

Förutom att lägga till egenskaperna har du även sparat JSON-dokumentet i Azure Table Storage.

### <a name="retrieve-a-custom-resource"></a>Hämta en anpassad resurs

För anpassade leverantörer hämtas en anpassad resurs via GET-begäranden. En anpassad provider accepterar *inte* en JSON-begärandetext. För GET-begäranden använder `x-ms-customproviders-requestpath` slutpunkten huvudet för att returnera den redan skapade resursen.

Lägg till följande **Metoden RetrieveCustomResource** för att hämta befintliga resurser:

```csharp
/// <summary>
/// Retrieves a custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the existing custom resource.</returns>
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

I Azure följer resurser en RESTful-modell. Url:en för begäran som skapar en resurs returnerar också resursen om en GET-begäran utförs.

### <a name="remove-a-custom-resource"></a>Ta bort en anpassad resurs

För anpassade leverantörer tas en anpassad resurs bort via DELETE-begäranden. En anpassad provider accepterar *inte* en JSON-begärandetext. För en DELETE-begäran använder `x-ms-customproviders-requestpath` slutpunkten huvudet för att ta bort den redan skapade resursen.

Lägg till följande **Metoden RemoveCustomResource** för att ta bort befintliga resurser:

```csharp
/// <summary>
/// Removes an existing custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure storage account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the result of the deletion.</returns>
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

I Azure följer resurser en RESTful-modell. Url:en för begäran som skapar en resurs tar också bort resursen om en DELETE-begäran utförs.

### <a name="list-all-custom-resources"></a>Lista alla anpassade resurser

För anpassade leverantörer kan du räkna upp en lista över befintliga anpassade resurser med hjälp av hämta GET-begäranden. En anpassad provider accepterar *inte* en JSON-begärandetext. För en samling GET-begäranden använder `x-ms-customproviders-requestpath` slutpunkten huvudet för att räkna upp de redan skapade resurserna.

Lägg till följande **metod För UppräkningAllCustomResources** för att räkna upp befintliga resurser:

```csharp
/// <summary>
/// Enumerates all the stored custom resources for a given type.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="resourceType">The resource type of the enumeration.</param>
/// <returns>The HTTP response containing a list of resources stored under 'value'.</returns>
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
> Syntaxen för RowKey QueryComparisons.GreaterThan och QueryComparisons.LessThan är Azure Table Storage-syntax för att utföra en "startswith"-fråga för strängar.

Om du vill visa en lista över alla befintliga resurser genererar du en Azure Table-lagringsfråga som säkerställer att resurserna finns under din anpassade providerpartition. Frågan kontrollerar sedan att radnyckeln `{myResourceType}` börjar med samma värde.

## <a name="integrate-restful-operations"></a>Integrera RESTful-åtgärder

När alla RESTful-metoder har lagts till i funktionsappen uppdaterar du **huvudkörningsmetoden** som anropar funktionerna för att hantera de olika REST-begäranden:

```csharp
/// <summary>
/// Entry point for the Azure function app webhook that acts as the service behind a custom provider.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="log">The logger.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <returns>The HTTP response for the custom Azure API.</returns>
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
        // Action request for a custom action.
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

Den uppdaterade **körmetoden** innehåller nu tableStorage-indatabindningen som du har lagt till för Azure Table storage. *tableStorage* Den första delen av `x-ms-customproviders-requestpath` metoden läser `Microsoft.Azure.Management.ResourceManager.Fluent` huvudet och använder biblioteket för att tolka värdet som ett resurs-ID. Huvudet `x-ms-customproviders-requestpath` skickas av den anpassade providern och anger sökvägen till den inkommande begäran.

Genom att använda det tolkade resurs-ID:t kan du generera **partitionKey-** och **rowKey-värdena** för att data ska slå upp eller lagra anpassade resurser.

När du har lagt till metoder och klasser måste du uppdatera **med metoderna** för funktionsappen. Lägg till följande kod högst upp i C#-filen:

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

Om du går vilse vid någon tidpunkt i den här självstudien kan du hitta det fullständiga kodexemplet i den [anpassade providern C# RESTful endpoint reference](./reference-custom-providers-csharp-endpoint.md). När du är klar med funktionsappen sparar du funktionsappens URL. Den kan användas för att utlösa funktionsappen i senare självstudier.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat en RESTful-slutpunkt för att arbeta med en slutpunkt för Azure Custom Provider. Om du vill veta hur du skapar en anpassad leverantör går du till artikeln [Självstudiekurs: Skapa en anpassad leverantör](./tutorial-custom-providers-create.md).
