---
title: Hur du hanterar anslutningar i Azure Functions
description: Lär dig att undvika prestandaproblem i Azure Functions genom att använda statiska-klienter.
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
ms.openlocfilehash: 4ea2b033d8d67dd3c921fb833462605ba0aeb687
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655377"
---
# <a name="how-to-manage-connections-in-azure-functions"></a>Hur du hanterar anslutningar i Azure Functions

Funktioner i en funktionsapp dela resurser och bland de delade resurserna finns anslutningar &mdash; HTTP-anslutningar, databas och anslutningar till Azure-tjänster, till exempel lagring. När många funktioner som körs samtidigt är det möjligt att få slut på tillgängliga anslutningar. Den här artikeln beskriver hur du kodar dina funktioner för att undvika att använda fler anslutningar än de verkligen behöver.

## <a name="connections-limit"></a>Kopplingsgränsen

Antalet tillgängliga anslutningar är begränsat delvis eftersom en funktionsapp körs i den [Azure App Service sandbox](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). En av de begränsningar som sandbox inför på din kod är en [kapacitet för antalet anslutningar som för närvarande 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits). När du når den här gränsen functions-runtime skapar en logg med följande meddelande: `Host thresholds exceeded: Connections`.

Risken för att överskrida gränsen öka när den [skala domänkontrollanten lägger till funktionen app-instanser](functions-scale.md#how-the-consumption-plan-works). Varje funktion app-instansen kan anropar funktioner många gånger på samma gång, och alla dessa funktioner använder anslutningar som 300 räknas.

## <a name="use-static-clients"></a>Använd statiska klienter

Återanvända klienten instanser i stället för att skapa nya med varje funktionsanrop för att undvika att hålla fler anslutningar än nödvändigt. .NET-klienter som den `HttpClient`, `DocumentClient`, och Azure Storage-klienter kan hantera anslutningar om du använder en enda, statiska klient.

Här följer några riktlinjer för att följa när du använder en tjänstspecifika klient i ett program med Azure Functions:

- **INTE** skapa en ny klient med varje funktionsanrop.
- **GÖR** skapa en enda, statiska klient som kan användas av alla funktionsanrop.
- **Överväg att** skapar en enda, statisk klient i en delad hjälpklass om olika funktioner som använder samma tjänst.

## <a name="httpclient-code-example"></a>HttpClient kodexempel

Här är ett exempel på funktionskod som skapar en statisk `HttpClient`:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("http://example.com");
    // Rest of function
}
```

Vanliga frågor om .NET `HttpClient` är ”bör jag vara avyttring min klient”? I allmänhet kan du ta bort objekt som implementerar `IDisposable` när du är klar med dem. Men du inte ta bort en statisk klient eftersom du inte använda den när funktionen avslutas. Vill du statiska klienten live för varaktighet för ditt program.

## <a name="documentclient-code-example"></a>DocumentClient kodexempel

`DocumentClient` ansluter till en Cosmos-DB-instans. Cosmos-DB-dokumentationen rekommenderar att du [använder klienten en singleton-Azure Cosmos DB för livslängden för ditt program](https://docs.microsoft.com/en-us/azure/cosmos-db/performance-tips#sdk-usage). I följande exempel visas ett mönster för att göra det i en funktion.

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

För mer information om varför statiska klienter rekommenderas, se [felaktig instansiering antipattern](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Flera Azure Functions prestandatips finns [optimera prestanda och tillförlitlighet i Azure Functions](functions-best-practices.md).