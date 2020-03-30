---
title: Hantera anslutningar i Azure Functions
description: Lär dig hur du undviker prestandaproblem i Azure Functions med hjälp av statiska anslutningsklienter.
ms.topic: conceptual
ms.date: 02/25/2018
ms.openlocfilehash: 872ad9a1b8f0a7da6fe410e68f08469ac11045a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276457"
---
# <a name="manage-connections-in-azure-functions"></a>Hantera anslutningar i Azure Functions

Funktioner i en funktionsapp delar resurser. Bland dessa delade resurser finns anslutningar: HTTP-anslutningar, databasanslutningar och anslutningar till tjänster som Azure Storage. När många funktioner körs samtidigt är det möjligt att få på tillgängliga anslutningar. I den här artikeln beskrivs hur du kodar dina funktioner för att undvika att använda fler anslutningar än de behöver.

## <a name="connection-limit"></a>Anslutningsgräns

Antalet tillgängliga anslutningar är begränsat delvis på grund av att en funktionsapp körs i en [sandbox-miljö](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). En av de begränsningar som i sandlådan medför för koden är en gräns för antalet utgående anslutningar, som för närvarande är 600 aktiva (totalt 1 200) anslutningar per instans. När du når den här gränsen skriver funktionerna körtiden `Host thresholds exceeded: Connections`följande meddelande till loggarna: . Mer information finns i [funktionstjänstgränserna](functions-scale.md#service-limits).

Den här gränsen är per instans. När [skalningsstyrenheten lägger till funktionsappinstanser](functions-scale.md#how-the-consumption-and-premium-plans-work) för att hantera fler begäranden har varje instans en oberoende anslutningsgräns. Det innebär att det inte finns någon global anslutningsgräns och du kan ha mycket mer än 600 aktiva anslutningar i alla aktiva instanser.

När du felsöker kontrollerar du att du har aktiverat Application Insights för din funktionsapp. Med Application Insights kan du visa mått för dina funktionsappar som körningar. Mer information finns [i Visa telemetri i Application Insights](functions-monitoring.md#view-telemetry-in-application-insights).  

## <a name="static-clients"></a>Statiska klienter

Om du vill undvika att hålla fler anslutningar än nödvändigt kan du återanvända klientinstanser i stället för att skapa nya med varje funktionsanrop. Vi rekommenderar att du återanvänder klientanslutningar för alla språk som du kan skriva din funktion i. .NET-klienter som [httpclient-,](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) [DocumentClient-](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
)och Azure Storage-klienterna kan till exempel hantera anslutningar om du använder en enda statisk klient.

Här är några riktlinjer som du kan följa när du använder en tjänstspecifik klient i ett Azure Functions-program:

- *Skapa inte* en ny klient med varje funktionsyrop.
- *Skapa* en enda, statisk klient som varje funktionsyrop kan använda.
- *Överväg* att skapa en enda statisk klient i en delad hjälpklass om olika funktioner använder samma tjänst.

## <a name="client-code-examples"></a>Exempel på klientkod

Det här avsnittet innehåller metodtips för att skapa och använda klienter från funktionskoden.

### <a name="httpclient-example-c"></a>HttpClient-exempel (C#)

Här är ett exempel på C#-funktionskod som skapar en statisk [HttpClient-förekomst:](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

En vanlig fråga om [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) i .NET är "Ska jag göra mig av med min klient?" I allmänhet gör du dig `IDisposable` av med objekt som implementeras när du är klar med dem. Men du gör dig inte av med en statisk klient eftersom du inte är klar med den när funktionen slutar. Du vill att den statiska klienten ska leva under hela programmet.

### <a name="http-agent-examples-javascript"></a>HTTP-agentexempel (JavaScript)

Eftersom det ger bättre alternativ för anslutningshantering bör du använda den [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) `node-fetch` inbyggda klassen i stället för icke-inbyggda metoder, till exempel modulen. Anslutningsparametrar konfigureras `http.agent` via alternativ för klassen. Detaljerade alternativ som är tillgängliga med HTTP-agenten finns i [nya agentalternativ\[\]](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options).

Den `http.globalAgent` globala klassen `http.request()` som används av har alla dessa värden inställda på sina respektive standardvärden. Det rekommenderade sättet att konfigurera anslutningsgränser i Funktioner är att ange ett maximalt antal globalt. I följande exempel anges det maximala antalet sockets för funktionsappen:

```js
http.globalAgent.maxSockets = 200;
```

 I följande exempel skapas en ny HTTP-begäran med en anpassad HTTP-agent endast för den begäran:

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>Exempel på DocumentClient-kod (C#)

[DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) ansluter till en Azure Cosmos DB-instans. Azure Cosmos DB-dokumentationen rekommenderar att du [använder en Azure Cosmos DB-klient för singleton-Azure Cosmos DB-klienten under programmets livstid](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage). I följande exempel visas ett mönster för att göra det i en funktion:

```cs
#r "Microsoft.Azure.Documents.Client"
using Microsoft.Azure.Documents.Client;

private static Lazy<DocumentClient> lazyClient = new Lazy<DocumentClient>(InitializeDocumentClient);
private static DocumentClient documentClient => lazyClient.Value;

private static DocumentClient InitializeDocumentClient()
{
    // Perform any initialization here
    var uri = new Uri("example");
    var authKey = "authKey";
    
    return new DocumentClient(uri, authKey);
}

public static async Task Run(string input)
{
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("database", "collection");
    object document = new { Data = "example" };
    await documentClient.UpsertDocumentAsync(collectionUri, document);
    
    // Rest of function
}
```

### <a name="cosmosclient-code-example-javascript"></a>Exempel på CosmosClient-kod (JavaScript)
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) ansluter till en Azure Cosmos DB-instans. Azure Cosmos DB-dokumentationen rekommenderar att du [använder en Azure Cosmos DB-klient för singleton-Azure Cosmos DB-klienten under programmets livstid](../cosmos-db/performance-tips.md#sdk-usage). I följande exempel visas ett mönster för att göra det i en funktion:

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const key = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, key });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { resources: itemArray } = await container.items.readAll().fetchAll();
    context.log(itemArray);
}
```

## <a name="sqlclient-connections"></a>SqlClient-anslutningar

Din funktionskod kan använda .NET Framework Data Provider för SQL Server ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) för att skapa anslutningar till en SQL-relationsdatabas. Detta är också den underliggande leverantören för dataramverk som är beroende av ADO.NET, till exempel [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Till skillnad från [HttpClient-](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) och [DocumentClient-anslutningar](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) implementerar ADO.NET anslutningspoolning som standard. Men eftersom du fortfarande kan få på anslutningar bör du optimera anslutningarna till databasen. Mer information finns i [SQL Server Connection Pooling (ADO.NET).](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)

> [!TIP]
> Vissa dataramverk, till exempel Entitetsramverk, hämtar vanligtvis anslutningssträngar från avsnittet **ConnectionStrings** i en konfigurationsfil. I det här fallet måste du uttryckligen lägga till SQL-databasanslutningssträngar i samlingen **Anslutningssträngar** för funktionsappinställningarna och i [filen local.settings.json](functions-run-local.md#local-settings-file) i det lokala projektet. Om du skapar en instans av [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) i funktionskoden bör du lagra anslutningssträngvärdet i **Programinställningar med** dina andra anslutningar.

## <a name="next-steps"></a>Nästa steg

Mer information om varför vi rekommenderar statiska klienter finns i [Felaktig instansieringsantimönster](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Fler prestandatips för Azure Functions finns i [Optimera prestanda och tillförlitlighet för Azure Functions](functions-best-practices.md).
