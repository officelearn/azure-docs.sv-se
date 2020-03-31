---
title: Övervaka indexerarstatus och resultat
titleSuffix: Azure Cognitive Search
description: Övervaka status, förlopp och resultat för Azure Cognitive Search-indexerare i Azure-portalen med REST API eller .NET SDK.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 699b5a4e5a7f10c883667ca5030dd971855467f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112989"
---
# <a name="how-to-monitor-azure-cognitive-search-indexer-status-and-results"></a>Så här övervakar du Azure Cognitive Search-indexerarens status och resultat

Azure Cognitive Search ger status och övervakningsinformation om aktuella och historiska körningar för varje indexerare.

Indexeringsövervakning är användbar när du vill:

* Spåra förloppet för en indexerare under en pågående körning.
* Granska resultaten av pågående eller tidigare indexeringskörning.
* Identifiera indexeringsfel på den högsta nivån och fel eller varningar om att enskilda dokument indexeras.

## <a name="get-status-and-history"></a>Få status och historik

Du kan komma åt indexerarövervakningsinformation på olika sätt, bland annat:

* I [Azure Portal](#portal)
* Använda [REST API](#restapi)
* Använda [.NET SDK](#dotnetsdk)

Tillgänglig övervakningsinformation för indexerare omfattar alla följande (även om dataformaten skiljer sig beroende på vilken åtkomstmetod som används):

* Statusinformation om själva indexeraren
* Information om den senaste körningen av indexeraren, inklusive dess status, start- och sluttider och detaljerade fel och varningar.
* En lista över historiska indexerare körs och deras status, resultat, fel och varningar.

Indexerare som bearbetar stora mängder data kan ta lång tid att köra. Indexerare som hanterar miljontals källdokument kan till exempel köras i 24 timmar och sedan starta om nästan omedelbart. Statusen för högvolymsindexerare kan alltid säga **Pågår** i portalen. Även när en indexerare körs finns information om pågående framsteg och tidigare körningar.

<a name="portal"></a>

## <a name="monitor-using-the-portal"></a>Övervaka med hjälp av portalen

Du kan se aktuell status för alla indexare i **indexerarlistan** på sidan Översikt för söktjänsten.

   ![Indexerare lista](media/search-monitor-indexers/indexers-list.png "Indexerare lista")

När en indexerare körs visar statusen i listan **Pågående**och värdet **För att lyckades med dokument** visar antalet dokument som bearbetats hittills. Det kan ta några minuter för portalen att uppdatera indexerarens statusvärden och antalet dokument.

En indexerare vars senaste körning lyckades visar **Framgång**. En indexeringskörning kan lyckas även om enskilda dokument har fel, om antalet fel är mindre än indexerarens **max-misslyckade objektinställning.**

Om den senaste körningen slutade med ett fel visas **Det inte gick att visa**statusen Misslyckades . Status för **Återställ** innebär att indexerarens ändringsspårningstillstånd återställdes.

Klicka på en indexerare i listan för att se mer information om indexerarens aktuella och senaste körningar.

   ![Indexer sammanfattning och körning historia](media/search-monitor-indexers/indexer-summary.png "Indexer sammanfattning och körning historia")

Sammanfattningsdiagrammet **Indexer** visar ett diagram över antalet dokument som bearbetats i de senaste körningarna.

Listan **Körningsinformation** visar upp till 50 av de senaste körningsresultaten.

Klicka på ett körningsresultat i listan för att se detaljer om körningen. Detta inkluderar start- och sluttider och eventuella fel och varningar som inträffat.

   ![Information om indexeringskörning](media/search-monitor-indexers/indexer-execution.png "Information om indexeringskörning")

Om det fanns dokumentspecifika problem under körningen visas de i fälten Fel och varningar.

   ![Indexer information med fel](media/search-monitor-indexers/indexer-execution-error.png "Indexer information med fel")

Varningar är vanliga med vissa typer av indexerare och tyder inte alltid på något problem. Indexerare som använder kognitiva tjänster kan till exempel rapportera varningar när bild- eller PDF-filer inte innehåller någon text att bearbeta.

Mer information om hur du undersöker indexfel och varningar finns [i Felsöka vanliga indexeringsproblem i Azure Cognitive Search](search-indexer-troubleshooting.md).

<a name="restapi"></a>

## <a name="monitor-using-rest-apis"></a>Övervaka med REST-API:er

Du kan hämta status- och körningshistoriken för en indexerare med [kommandot Hämta indexerarestatus:](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

Svaret innehåller övergripande indexerarstatus, den sista (eller pågående) indexerarens åkallan och historiken för de senaste indexeringsyrkena.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

Körningshistorik innehåller upp till de 50 senaste körningarna, som sorteras i omvänd kronologisk ordning (senaste först).

Observera att det finns två olika statusvärden. Status på den översta nivån är för indexeraren själv. En indexerarstatus **för körning** innebär att indexeraren är korrekt inställd och tillgänglig för körning, men inte att den körs för tillfället.

Varje körning av indexeraren har också sin egen status som anger om den specifika körningen pågår (**körs),** eller redan har slutförts med en **lyckad**, **transientFailure**eller **persistentFailure-status.** 

När en indexerare återställs för att uppdatera sitt ändringsspårningstillstånd läggs en separat körningshistorikpost till med status **för återställning.**

Mer information om statuskoder och indexerarövervakningsdata finns i [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

<a name="dotnetsdk"></a>

## <a name="monitor-using-the-net-sdk"></a>Övervaka med hjälp av .NET SDK

Du kan definiera schemat för en indexerare med hjälp av Azure Cognitive Search .NET SDK. Det gör du **schedule** genom att inkludera schemaegenskapen när du skapar eller uppdaterar en indexerare.

I följande C#-exempel skrivs information om en indexerares status och resultatet av dess senaste (eller pågående) körning till konsolen.

```csharp
static void CheckIndexerStatus(Indexer indexer, SearchServiceClient searchService)
{
    try
    {
        IndexerExecutionInfo execInfo = searchService.Indexers.GetStatus(indexer.Name);

        Console.WriteLine("Indexer has run {0} times.", execInfo.ExecutionHistory.Count);
        Console.WriteLine("Indexer Status: " + execInfo.Status.ToString());

        IndexerExecutionResult result = execInfo.LastResult;

        Console.WriteLine("Latest run");
        Console.WriteLine("  Run Status: {0}", result.Status.ToString());
        Console.WriteLine("  Total Documents: {0}, Failed: {1}", result.ItemCount, result.FailedItemCount);

        TimeSpan elapsed = result.EndTime.Value - result.StartTime.Value;
        Console.WriteLine("  StartTime: {0:T}, EndTime: {1:T}, Elapsed: {2:t}", result.StartTime.Value, result.EndTime.Value, elapsed);

        string errorMsg = (result.ErrorMessage == null) ? "none" : result.ErrorMessage;
        Console.WriteLine("  ErrorMessage: {0}", errorMsg);
        Console.WriteLine("  Document Errors: {0}, Warnings: {1}\n", result.Errors.Count, result.Warnings.Count);
    }
    catch (Exception e)
    {
        // Handle exception
    }
}
```

Utgången i konsolen kommer att se ut ungefär så här:

    Indexer has run 18 times.
    Indexer Status: Running
    Latest run
      Run Status: Success
      Total Documents: 7, Failed: 0
      StartTime: 10:02:46 PM, EndTime: 10:02:47 PM, Elapsed: 00:00:01.0990000
      ErrorMessage: none
      Document Errors: 0, Warnings: 0

Observera att det finns två olika statusvärden. Status på den översta nivån är status för själva indexeraren. Status för status **för** indexering innebär att indexeraren är korrekt och tillgänglig för körning, men inte att den körs för tillfället.

Varje körning av indexeraren har också sin egen status för om den specifika körningen pågår (**Kör**), eller redan har slutförts med **statusen Framgång** eller **TransientError.** 

När en indexerare återställs för att uppdatera sitt ändringsspårningstillstånd läggs en separat historikpost till med **återställningsstatus.**

Mer information om statuskoder och övervakningsinformation för indexerare finns i [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) i REST API.

Information om dokumentspecifika fel eller varningar kan hämtas genom `IndexerExecutionResult.Errors` att `IndexerExecutionResult.Warnings`räkna upp listorna och .

Mer information om de .NET SDK-klasser som används för att övervaka indexerare finns i [IndexerExecutionInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo?view=azure-dotnet) och [IndexerExecutionResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet).
