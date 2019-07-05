---
title: Så här övervakar statusen för indexerare och resultat – Azure Search
description: Övervaka status, förlopp och resultat av Azure Search-indexerare i Azure-portalen med hjälp av REST API eller .NET SDK.
ms.date: 06/28/2019
author: RobDixon22
manager: HeidiSteen
ms.author: v-rodixo
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 07b4fe2ef830c3ce09b655cf4b433d14923229a9
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486290"
---
# <a name="how-to-monitor-azure-search-indexer-status-and-results"></a>Så här övervakar statusen för Azure Search-indexeraren och resultat

Azure Search innehåller information om aktuella och historiska körningar av varje indexerare om övervakning och status.

Indexeraren övervakning är användbart när du vill:

* Spåra förloppet för en indexerare under en pågående kör.
* Granska resultatet av pågående eller föregående indexeraren körs.
* Identifiera översta indexeraren fel, och fel eller varningar om enskilda dokument som indexeras.

## <a name="find-indexer-status-and-history-details"></a>Hitta indexeraren status och historik för information

Du kan komma åt indexeraren övervakningsinformation på olika sätt, inklusive:

* I [Azure Portal](#portal)
* Med hjälp av den [REST-API](#restapi)
* Med hjälp av den [.NET SDK](#dotnetsdk)

Tillgängliga indexerare övervakningsinformation innehåller följande (även om de data som skiljer sig åt format baserat på den åtkomstmetod som används):

* Statusinformation om indexeraren själva
* Information om den senaste körning av indexerare, inklusive dess status, start och sluttid och detaljerade fel och varningar.
* En lista över historiska indexeraren körs, och deras status, resultat, fel och varningar.

Indexerare som bearbetar stora mängder data kan ta lång tid att köra. Indexerare som hanterar miljontals källa dokument kan till exempel köra i 24 timmar och sedan starta om nästan omedelbart. Status för stora volymer indexerare kan säger alltid **pågår** i portalen. Även om en indexerare körs finns information om pågående förlopp och tidigare körningar.

<a name="portal"></a>

## <a name="monitor-indexers-in-the-portal"></a>Övervaka indexerare på portalen

Du kan se den aktuella statusen för alla dina indexerare i den **indexerare** listan på din översiktssidan för söktjänsten.

   ![Indexerare lista](media/search-monitor-indexers/indexers-list.png "indexerare lista")

När indexeraren körs, status i listan visar **pågår**, och **Docs lyckades** värdet visar antalet bearbetade hittills dokument. Det kan ta några minuter för portalen att uppdatera indexeraren statusvärden och dokumentantal.

En indexerare vars senaste körningen har lyckats visas **lyckades**. En indexerare som kör kan vara lyckas även om enskilda dokument innehåller fel, om antalet fel är mindre än indexeraren **max. misslyckade objekt** inställningen.

Om den senaste körningen avslutades med ett fel visas status **misslyckades**. Statusen **återställa** innebär att den indexeraren tillstånd för ändringsspårning har återställts.

Klicka på en indexerare i listan om du vill se mer information om indexeraren aktuella och nyligen körs.

   ![Historik för sammanfattning och körning av indexerare](media/search-monitor-indexers/indexer-summary.png "historik för sammanfattning och körning av indexerare")

Den **indexeraren sammanfattning** diagrammet visar ett diagram över antal dokument som behandlas i dess senaste körningar.

Den **utförandeinformation** i listan visas upp till 50 resultatets senaste körning.

Klicka på ett Körningsresultat i listan om du vill se mer information om som körs. Detta inkluderar dess start- och sluttider, och eventuella fel och varningar som inträffat.

   ![Information om körning av indexerare](media/search-monitor-indexers/indexer-execution.png "körningsinformation för indexerare")

Om det var problem med visst dokument under körningen, visas de i fälten fel och varningar.

   ![Indexeraren information med fel](media/search-monitor-indexers/indexer-execution-error.png "indexeraren information med fel")

Varningar är vanliga med vissa typer av indexerare och alltid indikerar inte ett problem. Indexerare som använder kognitiva tjänster kan till exempel rapportera varningar när bild- eller PDF-filer som inte innehåller någon text att bearbeta.

Mer information om hur du undersöker indexeraren fel och varningar finns i [felsökning av vanliga problem med indexerare i Azure Search](search-indexer-troubleshooting.md).

<a name="restapi"></a>

## <a name="monitor-indexers-using-the-rest-api"></a>Övervaka indexerare med hjälp av REST API

Du kan hämta status och körning historiken för en indexerare med hjälp av den [hämta Status för indexeraren kommandot](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

Svaret innehåller status för övergripande indexerare, senaste (eller pågående) indexer-anrop och historiken för de senaste indexer-anrop.

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

Körningshistorik innehåller upp till 50 senaste körningar, vilket är sorterade i omvänd kronologisk ordning (senaste först).

Observera att det finns två olika statusvärden. Översta nivån statusen är för indexeraren själva. Statusen indexeraren **kör** innebär att indexeraren är korrekt konfigurerad och köras, men inte att den är för närvarande körs.

Varje körning av indexeraren har också sin egen status som anger om den specifika körningen är pågående (**kör**), eller redan har slutförts med en **lyckades**, **transientFailure**, eller **persistentFailure** status. 

När en indexerare återställs för att uppdatera ändringsspårning tillstånd, läggs en separat körning historik-post med en **återställa** status.

Mer information om statuskoder och indexerare övervakning av data finns i [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

<a name="dotnetsdk"></a>

## <a name="monitor-indexers-using-the-net-sdk"></a>Övervaka indexerare med hjälp av .NET SDK

Du kan definiera schemat för en indexerare med hjälp av Azure Search .NET SDK. Gör detta genom att inkludera den **schema** egenskapen när du skapar eller uppdaterar en indexerare.

Följande C# exempel skriver information om status för en indexerare och resultatet av dess mest senaste (eller pågående) kör till konsolen.

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

I konsolen utdata ser ut ungefär så här:

    Indexer has run 18 times.
    Indexer Status: Running
    Latest run
      Run Status: Success
      Total Documents: 7, Failed: 0
      StartTime: 10:02:46 PM, EndTime: 10:02:47 PM, Elapsed: 00:00:01.0990000
      ErrorMessage: none
      Document Errors: 0, Warnings: 0

Observera att det finns två olika statusvärden. Översta statusen är status för indexeraren själva. Statusen indexeraren **kör** innebär att indexeraren är korrekt konfigurerat och tillgänglig för körning, men inte att den körs för tillfället.

Varje körning av indexeraren har också sin egen status för om den specifika körningen är pågående (**kör**), eller redan har slutförts med en **lyckades** eller **TransientError** status. 

När en indexerare återställs för att uppdatera ändringsspårning tillstånd, läggs en separat historik-post med en **återställa** status.

Mer information om statuskoder och indexerare information om övervakning finns i [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) i REST-API.

Information om dokumentet-specifikt fel eller varningar som kan hämtas genom att räkna upp listor `IndexerExecutionResult.Errors` och `IndexerExecutionResult.Warnings`.

Läs mer om .NET SDK-klasser som används för att övervaka indexerare [IndexerExecutionInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo?view=azure-dotnet) och [IndexerExecutionResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet).
