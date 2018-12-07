---
title: Hur du hanterar anslutningar i Azure Functions
description: Lär dig hur du undviker problem med prestanda i Azure Functions med hjälp av statiska-klienter.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: glenga
ms.openlocfilehash: 9780f98be7d488f87e280a1f391f7cc536ce0f8c
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52999569"
---
# <a name="how-to-manage-connections-in-azure-functions"></a>Hur du hanterar anslutningar i Azure Functions

Funktioner i en funktionsapp delar resurser och bland de delade resurserna finns anslutningar &mdash; HTTP-anslutningar, databasanslutningar och anslutningar till Azure-tjänster som lagring. När många funktioner som körs samtidigt, är det möjligt att få slut på tillgängliga anslutningar. Den här artikeln beskriver hur du kodar dina funktioner för att undvika att använda fler anslutningar än de verkligen behöver.

## <a name="connections-limit"></a>Anslutningsgränsen

Antalet tillgängliga anslutningar är begränsat delvis eftersom en funktionsapp som körs i den [Azure App Service-sandboxen](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). En av de begränsningar som sandbox-miljön inför din kod är en [gräns för antalet anslutningar som för närvarande 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits). När du når den här gränsen kan funktionskörningen skapar en logg med följande meddelande: `Host thresholds exceeded: Connections`.

Sannolikheten för som överstiger gränsen går upp när den [skala controller lägger till funktionen app-instanserna](functions-scale.md#how-the-consumption-plan-works) att hantera fler begäranden. Varje funktion app-instansen kan köra många funktioner samtidigt, som använder anslutningar som 300 räknas.

## <a name="use-static-clients"></a>Använda statiska klienter

För att undvika att fler anslutningar än nödvändigt kan återanvända klientinstanser i stället för att skapa nya med varje funktionsanrop. .NET-klienter som den [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx), [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
), och Azure Storage-klienter kan hantera anslutningar om du använder en enda, statiska klient.

Här följer några riktlinjer för att följa när du använder en tjänstspecifika klient i ett program för Azure Functions:

- **INTE** skapa en ny klient med varje funktionsanrop.
- **GÖR** skapar en enda, statiska klient som kan användas av varje funktionsanrop.
- **Överväg att** skapar en enda, statisk klient i en delad hjälparklass om olika funktioner använder samma tjänst.

## <a name="client-code-examples"></a>Kodexempel för klienten

Det här avsnittet visar bra arbetsmetoder för skapar och använder klienter från funktionskoden.

### <a name="httpclient-example-c"></a>HttpClient exempel (C#)

Här är ett exempel på C# fungera kod som skapar en statisk [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx):

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

Vanliga frågor om .NET [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) är ”bör jag vara tar bort min klient”? Generellt sett kan du ta bort objekt som implementerar `IDisposable` när du är klar med dessa. Men du inte ta bort en statisk klient eftersom du inte är klar med den när funktionen har upphört. Du vill att statisk klienten TTL-värde för varaktigheten för ditt program.

### <a name="http-agent-examples-nodejs"></a>HTTP-agenten exempel (Node.js)

Eftersom det ger bättre anslutning hanteringsalternativ, bör du använda inbyggt [ `http.agent` ](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) klassen i stället för icke-intern metoder, till exempel den `node-fetch` modulen. Anslutningsparametrar konfigureras med hjälp av alternativen på den `http.agent` klass. Se [ny Agent (\[alternativ\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options) för detaljerade alternativ med HTTP-agenten.

Den globala `http.globalAgent` används av `http.request()` har alla de här värdena standardvärdena respektive. Det rekommenderade sättet att konfigurera anslutningsgräns i funktioner är att ange ett högsta antal globalt. I följande exempel anger det maximala antalet sockets för funktionsappen:

```js
http.globalAgent.maxSockets = 200;
```

 I följande exempel skapas en ny HTTP-begäran med en anpassad HTTP-agenten endast för begäran.

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

## <a name="sqlclient-connections"></a>SqlClient anslutningar

Funktionskoden kan använda .NET Framework Data Provider för SQL Server ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) att skapa anslutningar till en SQL-relationsdatabas. Det här är den underliggande providern för data-ramverk som förlitar sig på ADO.NET, till exempel Entity Framework. Till skillnad från [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) och [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) anslutningar, ADO.NET implementerar anslutningspooler som standard. Eftersom du kan fortfarande köra av anslutningar, bör du optimera anslutningar till databasen. Mer information finns i [SQL Server anslutning poolning (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Vissa data-ramverk, till exempel [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx), kommer vanligtvis anslutningssträngar från den **ConnectionStrings** i en konfigurationsfil. I det här fallet måste du uttryckligen lägga till SQL database-anslutningssträngar till den **anslutningssträngar** samling av din funktionsappinställningar och i den [local.settings.json-fil](functions-run-local.md#local-settings-file) i projektet lokalt. Om du skapar en [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) i Funktionskoden, bör du lagra Anslutningssträngens värde i **programinställningar** med dina andra anslutningar.

## <a name="next-steps"></a>Nästa steg

För mer information om varför statiska klienter rekommenderas, se [felaktig instansiering antimönstret](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Prestandatips för mer Azure Functions, se [optimera prestanda och tillförlitlighet i Azure Functions](functions-best-practices.md).
