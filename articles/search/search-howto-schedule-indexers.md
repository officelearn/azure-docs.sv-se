---
title: Så här schemalägger du indexerare – Azure Search
description: Schemalägga Azure Search-indexerare att indexera innehåll med jämna mellanrum eller vid specifika tidpunkter.
ms.date: 05/31/2019
author: RobDixon22
manager: HeidiSteen
ms.author: v-rodixo
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 764fca8d3cb4cd9c40d7880043637f89ef1a8578
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66755387"
---
# <a name="how-to-schedule-indexers-for-azure-search"></a>Så här schemalägger du indexerare för Azure Search
En indexerare körs vanligtvis en gång, omedelbart när den har skapats. Du kan köra den igen på begäran med hjälp av portalen, REST API eller .NET SDK. Du kan också konfigurera en indexerare för att köras med jämna mellanrum enligt ett schema.

Vissa situationer där indexeraren schemaläggning är användbart:

* Källdata ändras med tiden och du vill att Azure Search-indexerare att bearbeta ändrade data automatiskt.
* Indexet fylls i från flera datakällor och du vill kontrollera att indexerarna som körs vid olika tidpunkter för att minska konflikter.
* Källdata är mycket stort och du vill att sprida indexeraren behandling över tid. Läs mer om indexering stora mängder data, [indexera stora datauppsättningar i Azure Search](search-howto-large-index.md).

Scheduler är en inbyggd funktion i Azure Search. Du kan inte använda en extern schemaläggare för att styra search-indexerare.

## <a name="define-schedule-properties"></a>Definiera schemaegenskaper

En indexerschemat har två egenskaper:
* **Intervall**, som definierar hur lång tid mellan schemalagda indexeraren körningar. Den minsta tillåtna är 5 minuter och den största är 24 timmar.
* **Starta Time (UTC)** , vilket anger första gången som indexeraren ska köras.

Du kan ange ett schema när du först skapar indexeraren eller genom att uppdatera den indexeraren egenskaper senare. Indexeraren scheman kan anges med hjälp av den [portal](#portal), [REST API](#restApi), eller [.NET SDK](#dotNetSdk).

Endast en körning av en indexerare kan köras i taget. Om en indexerare körs när dess nästa körning har schemalagts, är den körningen skjutas upp tills nästa schemalagda tid.

Vi tar ett exempel för att göra detta mer konkret. Anta att vi konfigurera en indexerare schema med en **intervall** för varje timme och en **starttid** av 1 juni 2019 8:00:00: 00 UTC. Här är vad som kan inträffa när en indexerare som kör tar längre tid än en timme:

* Den första körningen av indexeraren startar vid eller runt 1 juni 2019 8:00:00 UTC. Anta att den här körningen tar 20 minuter (eller när som helst mindre än 1 timme).
* Den andra körningen startar vid eller runt den 1 juni 2019 9:00:00 UTC. Anta att den här körningen tar 70: e minut – mer än en timme – och det går inte att slutföra fram till kl. 10:10 UTC.
* Tredje körningen är schemalagd att starta 10:00:00 UTC, men då föregående körning fortfarande körs. Det schemalagda körning sedan hoppas över. Nästa körning av indexeraren startar inte förrän 11:00:00 UTC.

<a name="portal"></a>

## <a name="define-a-schedule-in-the-portal"></a>Definiera ett schema i portalen

Guiden Importera Data i portalen kan du definiera schemat för en indexerare vid tidpunkten för skapandet. Standardinställningen för schema är **per timme**, vilket innebär att indexeraren körs en gång när den har skapats och körs igen varje timme efteråt.

Du kan ändra inställningen för schema att **när** om du inte vill att indexeraren ska köras igen automatiskt, eller till **dagliga** ska köras en gång per dag. Ange den till **anpassad** om du vill ange ett annat intervall eller en specifik framtida starttid.

När du anger schemat till **anpassade**, fält visas så att du kan ange den **intervall** och **starta Time (UTC)** . Den kortaste tid som tillåts är 5 minuter och den längsta är 1440 minuter (24 timmar).

   ![Inställningen indexerschemat i guiden Importera Data](media/search-howto-schedule-indexers/schedule-import-data.png "inställningen indexerschemat i guiden Importera Data")

Du kan ändra schemat på indexeraren redigera panelen när du har skapat en indexerare. Schema-fält är samma som guiden Importera Data.

   ![Ange schemat i indexeraren redigera panelen](media/search-howto-schedule-indexers/schedule-edit.png "ange schemat i indexeraren redigera panel")

<a name="restApi"></a>

## <a name="define-a-schedule-using-the-rest-api"></a>Definiera ett schema med hjälp av REST-API

Du kan definiera schemat för en indexerare med REST API. Gör detta genom att inkludera den **schema** egenskapen när du skapar eller uppdaterar indexeraren. Exemplet nedan visar en PUT-begäran för att uppdatera en befintlig indexerare:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

Den **intervall** parametern är obligatorisk. Intervallet avser tiden mellan början av två på varandra följande indexeraren körningar. Minsta tillåtna intervall är 5 minuter. den längsta är en dag. Den måste vara formaterad som en XSD-värde för ”dayTimeDuration” (en begränsad delmängd av en [varaktighet i ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) värde). Mönstret för det här är: `P(nD)(T(nH)(nM))`. Exempel: `PT15M` för varje kvart `PT2H` för varannan timme.

Den valfria **startTime** när schemalagda körningar ska börja. Om det utelämnas används den aktuella UTC-tiden. Nu kan vara tidigare fall den första körningen har schemalagts som om indexeraren har körts sedan ursprungligt oavbrutet **startTime**.

Du kan också köra en indexerare på begäran när som helst med hjälp av kör Indexer-anrop. Läs mer om hur du kör indexerare och ställa in indexeraren scheman [köra indexeraren](https://docs.microsoft.com/rest/api/searchservice/run-indexer), [hämta indexeraren](https://docs.microsoft.com/rest/api/searchservice/get-indexer), och [uppdatering indexeraren](https://docs.microsoft.com/rest/api/searchservice/update-indexer) i REST API-referens.

<a name="dotNetSdk"></a>

## <a name="define-a-schedule-using-the-net-sdk"></a>Definiera ett schema med .NET SDK

Du kan definiera schemat för en indexerare med hjälp av Azure Search .NET SDK. Gör detta genom att inkludera den **schema** egenskapen när du skapar eller uppdaterar en indexerare.

Följande C# exempel skapar en indexerare med hjälp av en fördefinierad datakälla och index, och anger dess schema som ska köras en gång varje dag från nu och med 30 minuter:

```
    Indexer indexer = new Indexer(
        name: "azure-sql-indexer",
        dataSourceName: dataSource.Name,
        targetIndexName: index.Name,
        schedule: new IndexingSchedule(
                        TimeSpan.FromDays(1), 
                        new DateTimeOffset(DateTime.UtcNow.AddMinutes(30))
                    )
        );
    await searchService.Indexers.CreateOrUpdateAsync(indexer);
```
Om den **schema** parametern utelämnas, indexeraren körs bara en gång direkt när den har skapats.

Den **startTime** parameter kan anges till en gång tidigare. I så fall kan den första körningen schemaläggs som om indexeraren har körts kontinuerligt eftersom den angivna **startTime**.

Schemat har definierats med hjälp av den [IndexingSchedule](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingschedule?view=azure-dotnet) klass. Den **IndexingSchedule** konstruktor kräver en **intervall** parameter har angetts med hjälp av en **TimeSpan** objekt. Det minsta tillåtna intervallvärdet är 5 minuter och den största är 24 timmar. Andra **startTime** parametern som angetts som en **DateTimeOffset** objekt, är valfritt.

Med .NET SDK kan du kontrollen indexeraren åtgärder med hjälp av den [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) klassen och dess [indexerare](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexers) egenskapen, som implementerar metoderna från den **IIndexersOperations**gränssnitt. 

Du kan köra en indexerare på begäran när som helst med någon av de [kör](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run), [RunAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync), eller [RunWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync) metoder.

Mer information om hur du skapar, uppdaterar och köra indexerare, finns i [IIindexersOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations?view=azure-dotnet).
