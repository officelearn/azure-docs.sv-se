---
title: Hantera anslutningar i Azure Functions
description: Lär dig hur du undviker problem med prestanda i Azure Functions med hjälp av statiska-klienter.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/25/2018
ms.author: glenga
ms.openlocfilehash: 4e9bd4e9ea467446c2814cdb8956a40b1503b027
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61020493"
---
# <a name="manage-connections-in-azure-functions"></a>Hantera anslutningar i Azure Functions

Funktioner i en funktionsapp delar resurser. Bland dessa delade resurser finns i anslutningar: HTTP-anslutningar, databasanslutningar och anslutningar till tjänster som Azure Storage. När många funktioner som körs samtidigt, är det möjligt att få slut på tillgängliga anslutningar. Den här artikeln beskriver hur du kodar dina funktioner för att undvika att använda fler anslutningar än vad de behöver.

## <a name="connection-limit"></a>Anslutningsbegränsning

Antalet tillgängliga anslutningar är begränsat delvis eftersom en funktionsapp som körs i en [testmiljö](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). En av de begränsningar som sandbox-miljön inför din kod är en [gräns för antalet anslutningar (för närvarande på 600 aktiva anslutningar och 1 200 Totalt antal anslutningar)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits) per instans. När du når den här gränsen kan funktionskörningen skapar en logg med följande meddelande: `Host thresholds exceeded: Connections`.

Den här gränsen är per instans.  När den [skala controller lägger till funktionen app-instanserna](functions-scale.md#how-the-consumption-and-premium-plans-work) för att hantera fler begäranden varje instans har ett oberoende anslutningsgräns. Det innebär att det finns ingen gräns för global anslutning och du kan ha mycket mer än 600 aktiva anslutningar över alla aktiva instanser.

När du felsöker kan du kontrollera att du har aktiverat Application Insights för din funktionsapp. Application Insights kan du visa mått för dina funktionsappar som körningar. Mer information finns i [visa telemetri i Application Insights](functions-monitoring.md#view-telemetry-in-application-insights).  

## <a name="static-clients"></a>Statiska klienter

För att undvika att fler anslutningar än nödvändigt kan återanvända klientinstanser i stället för att skapa nya med varje funktionsanrop. Vi rekommenderar att återanvända klientanslutningar för alla språk som du kan skriva din funktion i. Till exempel .NET-klienter som den [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx), [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
), och Azure Storage-klienter kan hantera anslutningar om du använder en enda, statiska klient.

Här följer några riktlinjer för att följa när du använder en tjänstspecifika klient i ett program för Azure Functions:

- *Inte* skapa en ny klient med varje funktionsanrop.
- *Gör* skapa en statisk klient som kan använda för varje funktionsanrop.
- *Överväg att* skapar en enda, statisk klient i en delad hjälparklass om olika funktioner använder samma tjänst.

## <a name="client-code-examples"></a>Kodexempel för klienten

Det här avsnittet visar bra arbetsmetoder för skapar och använder klienter från funktionskoden.

### <a name="httpclient-example-c"></a>HttpClient exempel (C#)

Här är ett exempel på C# fungera kod som skapar en statisk [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) instans:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

Vanliga frågor om [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) i .NET är ”bör jag använda min klient”? I allmänhet du kasta objekt som implementerar `IDisposable` när du är klar med dessa. Men du inte förfara med en statisk klient eftersom du inte är klar med den när funktionen har upphört. Du vill att statisk klienten TTL-värde för varaktigheten för ditt program.

### <a name="http-agent-examples-javascript"></a>HTTP-agenten exempel (JavaScript)

Eftersom det ger bättre anslutning hanteringsalternativ, bör du använda inbyggt [ `http.agent` ](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) klassen i stället för icke-intern metoder, till exempel den `node-fetch` modulen. Anslutningsparametrar konfigureras via alternativen på den `http.agent` klass. Detaljerade alternativ tillgängliga med HTTP-agenten finns i [ny Agent (\[alternativ\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options).

Den globala `http.globalAgent` klass som används av `http.request()` har alla de här värdena standardvärdena respektive. Det rekommenderade sättet att konfigurera anslutningsgräns i funktioner är att ange ett högsta antal globalt. I följande exempel anger det maximala antalet sockets för funktionsappen:

```js
http.globalAgent.maxSockets = 200;
```

 I följande exempel skapas en ny HTTP-begäran med en anpassad HTTP-agenten endast för begäran:

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>DocumentClient kodexempel (C#)

[DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) ansluter till en Azure Cosmos DB-instans. Azure Cosmos DB-dokumentationen rekommenderar att du [använder en singleton Azure Cosmos DB-klienten för hela ditt programs livslängd](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage). I följande exempel visar ett mönster för att göra som i en funktion:

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

### <a name="cosmosclient-code-example-javascript"></a>Kodexempel för CosmosClient (JavaScript)
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) ansluter till en Azure Cosmos DB-instans. Azure Cosmos DB-dokumentationen rekommenderar att du [använder en singleton Azure Cosmos DB-klienten för hela ditt programs livslängd](../cosmos-db/performance-tips.md#sdk-usage). I följande exempel visar ett mönster för att göra som i en funktion:

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const masterKey = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, auth: { masterKey } });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { result: itemArray } = await container.items.readAll().toArray();
    context.log(itemArray);
}
```

## <a name="sqlclient-connections"></a>SqlClient anslutningar

Funktionskoden kan använda .NET Framework Data Provider för SQL Server ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) att skapa anslutningar till en SQL-relationsdatabas. Det är också den underliggande providern för data-ramverk som förlitar sig på ADO.NET, till exempel [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Till skillnad från [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) och [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) anslutningar, ADO.NET implementerar anslutningspooler som standard. Men eftersom du kan fortfarande köra av anslutningar, bör du optimera anslutningar till databasen. Mer information finns i [SQL Server anslutning poolning (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Vissa data ramverk som Entity Framework, kommer vanligtvis anslutningssträngar från den **ConnectionStrings** i en konfigurationsfil. I det här fallet måste du uttryckligen lägga till SQL database-anslutningssträngar till den **anslutningssträngar** samling av din funktionsappinställningar och i den [local.settings.json-fil](functions-run-local.md#local-settings-file) i projektet lokalt. Om du skapar en instans av [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) i Funktionskoden, bör du lagra Anslutningssträngens värde i **programinställningar** med dina andra anslutningar.

## <a name="next-steps"></a>Nästa steg

Mer information om varför vi rekommenderar att statiska klienter finns i [felaktig instansiering antimönstret](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Prestandatips för mer Azure Functions, se [optimera prestanda och tillförlitlighet i Azure Functions](functions-best-practices.md).
