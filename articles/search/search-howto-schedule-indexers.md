---
title: Schemalägg körning av indexerare
titleSuffix: Azure Cognitive Search
description: Schemalägg Azure Kognitiv sökning indexerare för att indexera innehåll regelbundet eller vid vissa tidpunkter.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 80c3f9aa02680097276f966ce6aea02acf1e40fb
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358804"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>Så här schemalägger du indexerare i Azure Kognitiv sökning

En indexerare körs vanligt vis en gång, omedelbart efter att den har skapats. Du kan köra den igen på begäran med hjälp av portalen, REST API eller .NET SDK. Du kan också konfigurera en indexerare så att den körs regelbundet enligt ett schema.

Några situationer där indexerare schemaläggning är användbart:

* Källdata ändras med tiden och du vill att Azure Kognitiv sökning indexerare automatiskt ska bearbeta de ändrade data.
* Indexet fylls från flera data källor och du vill se till att indexerarna körs vid olika tidpunkter för att minska konflikter.
* Käll informationen är mycket stor och du vill sprida bearbetningen av indexeraren över tid. Mer information om indexering av stora data volymer finns i [så här indexerar du stora data mängder i Azure kognitiv sökning](search-howto-large-index.md).

Scheduler är en inbyggd funktion i Azure Kognitiv sökning. Du kan inte använda en extern Scheduler för att kontrol lera Sök indexerarna.

## <a name="define-schedule-properties"></a>Definiera schema egenskaper

Ett Indexer schema har två egenskaper:
* **Intervall** , som definierar hur lång tid det tar mellan körningar av schemalagd indexerare. Det minsta tillåtna intervallet är 5 minuter och det största är 24 timmar.
* **Start tid (UTC)** , som anger första gången då indexeraren ska köras.

Du kan ange ett schema när du först skapar indexeraren eller genom att uppdatera indexeraren egenskaper senare. Indexerings scheman kan ställas in med hjälp av [portalen](#portal), [REST API](#restApi)eller [.NET SDK](#dotNetSdk).

Endast en körning av en indexerare kan köras i taget. Om en indexerare redan körs när nästa körning har schemalagts, senareläggs körningen tills nästa schemalagda tid.

Låt oss ta en titt på ett exempel för att göra detta mer konkret. Anta att vi konfigurerar ett indexare schema med ett **intervall** på varje timme och **Start tiden** den 1 juni 2019 kl. 8:00:00 am UTC. Det här kan inträffa när en indexerare-körning tar längre tid än en timme:

* Den första indexerare-körningen börjar vid eller med den 1 juni 2019 kl. 8:00 AM UTC. Anta att den här körningen tar 20 minuter (eller när som helst mindre än en timme).
* Den andra körningen börjar vid eller runt den 1 juni 2019 9:00 AM UTC. Anta att den här körningen tar 70 minuter – mer än en timme – och att den inte slutförs förrän 10:10 är UTC.
* Den tredje körningen är schemalagd att starta kl. 10:00 AM UTC, men vid den tidpunkten körs fortfarande föregående körning. Den här schemalagda körningen hoppas över. Nästa körning av indexeraren kommer inte att starta förrän 11:00 AM UTC.

> [!NOTE]
> Om en indexerare har angetts till ett visst schema men upprepade gånger Miss lyckas på samma dokument över och över igen varje gång den körs, kommer indexeraren att köras på ett mindre frekvent intervall (upp till max minst en gång var 24: e timme) tills den har lyckats med att göra ett nytt försök.  Om du tror att du har åtgärdat problemet som gjorde att indexeraren har fastnat vid en viss tidpunkt, kan du utföra en körning av indexeraren på begäran, och om detta sker fortsätter indexeraren med angivet schema intervall igen.

<a name="portal"></a>

## <a name="schedule-in-the-portal"></a>Schemalägg i portalen

Med guiden Importera data i portalen kan du definiera schemat för en indexerare när den skapas. Standard schema inställningen är **varje timme** , vilket innebär att indexeraren körs en gång efter att den har skapats och körs igen varje timme efteråt.

Du kan ändra schema inställningen till **en gång** om du inte vill att indexeraren ska köras igen automatiskt, eller till **varje** dag för att köras en gång per dag. Ange den som **anpassad** om du vill ange ett annat intervall eller en särskild framtida start tid.

När du ställer in schemat på **anpassad** visas fält där du kan ange **intervall** och **Start tid (UTC)**. Det kortaste tidsintervallet som tillåts är 5 minuter och det längsta är 1440 minuter (24 timmar).

   ![Konfigurera indexerings schema i guiden Importera data](media/search-howto-schedule-indexers/schedule-import-data.png "Konfigurera indexerings schema i guiden Importera data")

När du har skapat en indexerare kan du ändra schema inställningarna med hjälp av index panelens redigerings panel. Schema fälten är desamma som i guiden Importera data.

   ![Ange schemat i redigerings panelen för indexerare](media/search-howto-schedule-indexers/schedule-edit.png "Ange schemat i redigerings panelen för indexerare")

<a name="restApi"></a>

## <a name="schedule-using-rest-apis"></a>Schemalägg med REST API: er

Du kan definiera schemat för en indexerare med hjälp av REST API. Det gör du genom att lägga till egenskapen **schema** när du skapar eller uppdaterar indexeraren. Exemplet nedan visar en skicka-begäran om att uppdatera en befintlig indexerare:

```http
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }
```

Parametern **Interval** måste anges. Intervallet avser tiden från starten av två efterföljande körningar av indexerare. Det minsta tillåtna intervallet är 5 minuter; det längsta är en dag. Det måste formateras som ett XSD "dayTimeDuration"-värde (en begränsad delmängd av ett [varaktighets värde på ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Mönstret för detta är: `P(nD)(T(nH)(nM))` . Exempel: `PT15M` för var 15: e `PT2H` timme, för var 2: e timme.

Valfri **StartTime** anger när schemalagda körningar ska börja. Om den utelämnas används den aktuella UTC-tiden. Den här tiden kan vara förr, i vilket fall den första körningen schemaläggs som om indexeraren har körts kontinuerligt sedan den ursprungliga **Starttimen**.

Du kan också köra en indexerare på begäran när som helst med hjälp av anropet kör Indexer. Mer information om att köra indexerare och ange scheman för indexeraren finns i [köra indexerare](/rest/api/searchservice/run-indexer), [Hämta indexerare](/rest/api/searchservice/get-indexer)och [Uppdatera indexerare](/rest/api/searchservice/update-indexer) i REST API referensen.

<a name="dotNetSdk"></a>

## <a name="schedule-using-the-net-sdk"></a>Schemalägg med .NET SDK

Du kan definiera schemat för en indexerare med hjälp av Azure Kognitiv sökning .NET SDK. Det gör du genom att lägga till egenskapen **schema** när du skapar eller uppdaterar en indexerare.

Följande C#-exempel skapar en Azure SQL Database-indexerare med hjälp av en fördefinierad data källa och ett index, och ställer in schemat så att det körs en gång om dagen startar nu:

```csharp
var schedule = new IndexingSchedule(TimeSpan.FromDays(1))
{
    StartTime = DateTimeOffset.Now
};

var indexer = new SearchIndexer("hotels-sql-idxr", dataSource.Name, searchIndex.Name)
{
    Description = "Data indexer",
    Schedule = schedule
};

await indexerClient.CreateOrUpdateIndexerAsync(indexer);
```


Om **Schedule** -egenskapen utelämnas körs indexeraren bara en gång omedelbart efter att den har skapats.

Parametern **StartTime** kan ställas in på en tid i det förflutna. I så fall schemaläggs den första körningen som om indexeraren har körts kontinuerligt sedan den angivna **StartTime**.

Schemat definieras med [IndexingSchedule](/dotnet/api/azure.search.documents.indexes.models.indexingschedule) -klassen. **IndexingSchedule** -konstruktorn kräver en **intervall** parameter som anges med ett **TimeSpan** -objekt. Det minsta intervallet tillåts är 5 minuter och det största värdet är 24 timmar. Den andra **StartTime** -parametern, som anges som ett **DateTimeOffset** -objekt, är valfri.

Med .NET SDK kan du styra indexerings åtgärder med hjälp av [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient). 

Du kan köra en indexerare på begäran när som helst med hjälp av någon av metoderna [RunIndexer](/dotnet/api/azure.search.documents.indexes.searchindexerclient.runindexer) eller [RunIndexerAsync](/dotnet/api/azure.search.documents.indexes.searchindexerclient.runindexerasync) .

Mer information om att skapa, uppdatera och köra indexerare finns i [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient).