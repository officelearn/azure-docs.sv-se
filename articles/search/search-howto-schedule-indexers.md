---
title: Schemalägg körning av indexerare
titleSuffix: Azure Cognitive Search
description: Schemalägg Azure Cognitive Search-indexerare för att indexera innehåll regelbundet eller vid specifika tidpunkter.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 72326413d463d449d339b1f3fd241ba2c27b4b6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112941"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>Så här schemalägger du indexerare i Azure Cognitive Search

En indexerare körs normalt en gång, omedelbart efter att den har skapats. Du kan köra den igen på begäran med hjälp av portalen, REST API eller .NET SDK. Du kan också konfigurera en indexerare så att den körs med jämna mellanrum enligt ett schema.

Vissa situationer där indexeringsschemaläggning är användbar:

* Källdata ändras med tiden och du vill att Azure Cognitive Search-indexerarna automatiskt ska bearbeta de ändrade data.
* Indexet fylls i från flera datakällor och du vill se till att indexerarna körs vid olika tidpunkter för att minska konflikter.
* Källdata är mycket stora och du vill sprida indexeringsbearbetningen över tid. Mer information om hur du indexerar stora mängder data finns i [Så här indexerar du stora datauppsättningar i Azure Cognitive Search](search-howto-large-index.md).

Schemaläggaren är en inbyggd funktion i Azure Cognitive Search. Du kan inte använda en extern schemaläggare för att styra sökindexerare.

## <a name="define-schedule-properties"></a>Definiera schemaegenskaper

Ett indexerarschema har två egenskaper:
* **Intervall**, som definierar hur lång tid det emellan schemalagda indexeringskörningar. Det minsta tillåtna intervallet är 5 minuter och det största är 24 timmar.
* **Starttid (UTC),** som anger första gången indexeraren ska köras.

Du kan ange ett schema när du först skapar indexeraren eller genom att uppdatera indexerarens egenskaper senare. Indexeringsscheman kan ställas in med hjälp av [portalen,](#portal) [REST API](#restApi)eller [.NET SDK](#dotNetSdk).

Endast en körning av en indexerare kan köras åt gången. Om en indexerare redan körs när nästa körning är schemalagd, skjuts körningen upp till nästa schemalagda tid.

Låt oss överväga ett exempel för att göra detta mer konkret. Anta att vi konfigurerar ett indexerarschema med ett **intervall per** timme och en **starttid** den 1 juni 2019 klockan 8:00:00 UTC. Så här kan det hända när en indexeringskörning tar längre tid än en timme:

* Den första indexeringskörningen startar omkring den 1 juni 2019 klockan 8:00 UTC. Anta att körningen tar 20 minuter (eller när som helst mindre än 1 timme).
* Den andra körningen startar omkring den 1 juni 2019 09:00 UTC. Anta att körningen tar 70 minuter - mer än en timme - och den slutförs inte förrän 10:10 UTC.
* Den tredje körningen är schemalagd att starta klockan 10:00 UTC, men vid den tidpunkten körs den tidigare körningen fortfarande. Den här schemalagda körningen hoppas sedan över. Nästa körning av indexeraren startar inte förrän 11:00 UTC.

> [!NOTE]
> Om en indexerare är inställd på ett visst schema men upprepade gånger misslyckas på samma dokument om och om igen varje gång den körs, börjar indexeraren köras med ett mindre frekvent intervall (upp till maximalt minst en gång var 24:e timme) tills den lyckas göra framsteg Igen.  Om du tror att du har åtgärdat det problem som orsakade att indexeraren fastnade vid en viss punkt, kan du utföra en on demand-körning av indexeraren, och om det lyckas med framsteg återgår indexeraren till det inställda schemaintervallet igen.

<a name="portal"></a>

## <a name="schedule-in-the-portal"></a>Schema i portalen

Med guiden Importera data i portalen kan du definiera schemat för en indexerare när den skapas. Standardinställningen För schema är **Varje timme**, vilket innebär att indexeraren körs en gång efter att den har skapats och körs igen varje timme efteråt.

Du kan ändra schemainställningen till **En** gång om du inte vill att indexeraren ska köras igen automatiskt eller till **Dagligen** för att köras en gång per dag. Ställ in den på **Anpassad** om du vill ange ett annat intervall eller en viss framtida starttid.

När du ställer in schemat på **Anpassad**visas fälten så att du kan ange **intervall** och **starttid (UTC)**. Det kortaste tillåtna tidsintervallet är 5 minuter och det längsta är 1440 minuter (24 timmar).

   ![Ställa in indexerarschema i guiden Importera data](media/search-howto-schedule-indexers/schedule-import-data.png "Ställa in indexerarschema i guiden Importera data")

När en indexerare har skapats kan du ändra schemainställningarna med hjälp av indexerarens redigeringspanel. Fälten Schema är desamma som i guiden Importera data.

   ![Ställa in schemat på panelen Redigera indexerare](media/search-howto-schedule-indexers/schedule-edit.png "Ställa in schemat på panelen Redigera indexerare")

<a name="restApi"></a>

## <a name="schedule-using-rest-apis"></a>Schemalägg med REST-API:er

Du kan definiera schemat för en indexerare med REST API. Det gör du **schedule** genom att inkludera schemaegenskapen när du skapar eller uppdaterar indexeraren. Exemplet nedan visar en PUT-begäran om att uppdatera en befintlig indexerare:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

**Intervallparametern** krävs. Intervallet refererar till tiden mellan början av två på varandra följande indexeringskörningar. Det minsta tillåtna intervallet är 5 minuter; den längsta är en dag. Det måste formateras som ett XSD "dayTimeDuration"-värde (en begränsad delmängd av ett [VARAKTIGHETSVÄRDE FÖR ISO 8601).](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) Mönstret för detta `P(nD)(T(nH)(nM))`är: . Exempel: `PT15M` för varje 15 minuter, `PT2H` för varje 2 timmar.

Den valfria **startTime** anger när schemalagda körningar ska påbörjas. Om den utelämnas används den aktuella UTC-tiden. Den här tiden kan vara i det förflutna, i vilket fall den första körningen är schemalagd som om indexeraren har körts kontinuerligt sedan den ursprungliga **startTime**.

Du kan också köra en indexerare på begäran när som helst med hjälp av ringningen Kör indexerare. Mer information om hur du kör indexerare och ange indexerarscheman finns i [Kör indexerare](https://docs.microsoft.com/rest/api/searchservice/run-indexer), [Hämta indexerare](https://docs.microsoft.com/rest/api/searchservice/get-indexer)och [Uppdatera indexerare](https://docs.microsoft.com/rest/api/searchservice/update-indexer) i REST API-referensen.

<a name="dotNetSdk"></a>

## <a name="schedule-using-the-net-sdk"></a>Schemalägg med .NET SDK

Du kan definiera schemat för en indexerare med hjälp av Azure Cognitive Search .NET SDK. Det gör du **schedule** genom att inkludera schemaegenskapen när du skapar eller uppdaterar en indexerare.

I följande C#-exempel skapas en indexerare med hjälp av en fördefinierad datakälla och ett fördefinierat index och anger att schemat ska köras en gång om dagen med början 30 minuter från och med nu:

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
Om **schemaparametern** utelämnas körs indexeraren bara en gång direkt efter att den har skapats.

**Parametern startTime** kan ställas in på en tid tidigare. I så fall schemaläggs den första körningen som om indexeraren har körts kontinuerligt sedan den angivna **startTime**.

Schemat definieras med klassen [IndexingSchedule.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingschedule?view=azure-dotnet) **IndexingSchedule-konstruktorn** kräver en **intervallparameter** som anges med hjälp av ett **TimeSpan-objekt.** Det minsta tillåtna intervallvärdet är 5 minuter och det största är 24 timmar. Den **startTime** andra startTime-parametern, som anges som **datetimeoffset-objekt,** är valfri.

Med .NET SDK kan du styra indexeraråtgärder med klassen [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) och dess [indexerare,](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexers) som implementerar metoder från **gränssnittet IIndexersOperations.** 

Du kan köra en indexerare på begäran när som helst med hjälp av någon av metoderna [Run,](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run) [RunAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync)eller [RunWithHttpMessagesAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync)

Mer information om hur du skapar, uppdaterar och kör indexerare finns i [IIindexersOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations?view=azure-dotnet).
