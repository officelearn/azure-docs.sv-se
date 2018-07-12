---
title: Hur du hanterar anslutningar i Azure Functions
description: Lär dig hur du undviker problem med prestanda i Azure Functions med hjälp av statiska-klienter.
services: functions
documentationcenter: ''
author: tdykstra
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: tdykstra
ms.openlocfilehash: 6c0af8f6f7e1d4aea8880a7af311aaa21f474f7e
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969012"
---
# <a name="how-to-manage-connections-in-azure-functions"></a>Hur du hanterar anslutningar i Azure Functions

Funktioner i en funktionsapp delar resurser och bland de delade resurserna finns anslutningar &mdash; HTTP-anslutningar, databasanslutningar och anslutningar till Azure-tjänster som lagring. Det är möjligt att få slut på tillgängliga anslutningar när många funktioner som körs samtidigt. Den här artikeln beskriver hur du kodar dina funktioner för att undvika att använda fler anslutningar än de verkligen behöver.

## <a name="connections-limit"></a>Anslutningsgränsen

Antalet tillgängliga anslutningar är begränsat delvis eftersom en funktionsapp som körs i den [Azure App Service-sandboxen](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). En av de begränsningar som sandbox-miljön inför din kod är en [gräns för antalet anslutningar som för närvarande 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits). När du når den här gränsen kan funktionskörningen skapar en logg med följande meddelande: `Host thresholds exceeded: Connections`.

Risken för att överskrida gränsen öka när den [skala controller lägger till funktionen app-instanserna](functions-scale.md#how-the-consumption-plan-works). Varje funktion app-instansen kan anropar functions många gånger på samma gång, och alla dessa funktioner använder anslutningar som 300 räknas.

## <a name="use-static-clients"></a>Använda statiska klienter

För att undvika att fler anslutningar än nödvändigt kan återanvända klientinstanser i stället för att skapa nya med varje funktionsanrop. .NET-klienter som den `HttpClient`, `DocumentClient`, och Azure Storage-klienter kan hantera anslutningar om du använder en enda, statiska klient.

Här följer några riktlinjer för att följa när du använder en tjänstspecifika klient i ett program för Azure Functions:

- **INTE** skapa en ny klient med varje funktionsanrop.
- **GÖR** skapar en enda, statiska klient som kan användas av varje funktionsanrop.
- **Överväg att** skapar en enda, statisk klient i en delad hjälparklass om olika funktioner använder samma tjänst.

## <a name="httpclient-code-example"></a>HttpClient-kodexempel

Här är ett exempel på function-koden som skapar en statisk `HttpClient`:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("http://example.com");
    // Rest of function
}
```

Vanliga frågor om .NET `HttpClient` är ”bör jag vara tar bort min klient”? Generellt sett kan du ta bort objekt som implementerar `IDisposable` när du är klar med dessa. Men du inte ta bort en statisk klient eftersom du inte är klar med den när funktionen har upphört. Du vill att statisk klienten TTL-värde för varaktigheten för ditt program.

## <a name="documentclient-code-example"></a>DocumentClient-kodexempel

`DocumentClient` ansluter till en Cosmos DB-instans. Cosmos DB-dokumentationen rekommenderar att du [använder en singleton Azure Cosmos DB-klienten för hela ditt programs livslängd](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage). I följande exempel visar ett mönster för att göra som i en funktion.

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

## <a name="next-steps"></a>Nästa steg

För mer information om varför statiska klienter rekommenderas, se [felaktig instansiering antimönstret](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Prestandatips för mer Azure Functions, se [optimera prestanda och tillförlitlighet i Azure Functions](functions-best-practices.md).