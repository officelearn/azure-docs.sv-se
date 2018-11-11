---
title: Importera data i Azure Search| Microsoft Docs
description: Lär dig hur du laddar upp data till ett index i Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: heidist
ms.openlocfilehash: ab26adb330e69f71d94aa296ede558b44e47a187
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51249786"
---
# <a name="indexing-in-azure-search"></a>Indexering i Azure Search
> [!div class="op_single_selector"]
> * [Översikt](search-what-is-data-import.md)
> * [NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

I Azure Search körs frågor på det innehåll som lästs in i ett [sökindex](search-what-is-an-index.md). Den här artikeln går igenom två grundläggande sätt att läsa in innehåll i ett index: *skicka data (push)* till indexet programmatiskt, eller peka en [Azure Search-indexerare](search-indexer-overview.md) mot en datakälla som stöds för att *hämta in data (pull)*.

## <a name="pushing-data-to-an-index"></a>Skicka data till ett index
Push-modellen, som används för att programmatiskt skicka data till Azure Search, är den mest flexibla metoden. För det första finns det inga begränsningar på vilken typ av datakälla som får användas. En datauppsättning som består av JSON-dokument kan skickas till ett Azure Search-index, förutsatt att alla dokument i datauppsättningen har fält som mappar till fälten som anges i indexschemat. För det andra finns det inga begränsningar på körningsfrekvensen. Du kan skicka ändringar till ett index så ofta du vill. För program som har mycket låga fördröjningskrav (t.ex. om det är viktigt att sökåtgärder är synkroniserade med dynamiska inventeringsdatabaser) är push-modellen ditt enda alternativ.

Den här metoden är mer flexibel än pull-modellen eftersom du kan ladda upp dokument individuellt eller i batchar (upp till 1 000 per batch eller 16 MB, beroende på vilken gräns som nås först). Med push-modellen kan du också ladda upp dokument till Azure Search, oavsett var dina data finns.

### <a name="how-to-push-data-to-an-azure-search-index"></a>Så här skickar du data till ett Azure Search-index

Du kan använda följande API:er för att läsa in ett eller flera dokument i ett index:

+ [Lägg till, uppdatera eller ta bort dokument (REST API)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [indexAction-klass](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) eller [indexBatch-klass](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 

Det finns för närvarande inget verktygsstöd för att skicka data via portalen.

En introduktion till varje metod finns i [Importera data med REST](search-import-data-rest-api.md) eller [Importera data med .NET](search-import-data-dotnet.md).


## <a name="pulling-data-into-an-index"></a>Hämta in data till ett index
Datahämtningsmodellen crawlar en datakälla som stöds och överför automatiskt data till ditt index. I Azure Search har den här funktionen implementerats med hjälp av *indexerare*, som för närvarande finns tillgängliga för följande plattformar:

+ [Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](https://aka.ms/documentdb-search-indexer)
+ [Azure SQL Database och SQL Server på virtuella Azure-datorer](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Indexerare ansluter ett index till en datakälla (vanligtvis en tabell, vy eller motsvarande struktur) och mappar källfält till motsvarande fält i indexet. Under körningen omvandlas raduppsättningen automatiskt till JSON och läses in i det angivna indexet. Alla indexerare stöder schemaläggning så att du kan ange hur ofta data ska uppdateras. De flesta indexerare tillhandahåller ändringsspårning om datakällan har stöd för det. Indexerare spårar ändringar och borttagningar av befintliga dokument och identifierar nya dokument, vilket gör att du slipper hantera dina data i indexet aktivt. 


### <a name="how-to-pull-data-into-an-azure-search-index"></a>Så här hämtar du in data till ett Azure Search-index

Indexerarfunktioner exponeras på [Azure Portal](search-import-data-portal.md), i [REST-API:et](/rest/api/searchservice/Indexer-operations) och i [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperationsextensions). 

En fördel med att använda portalen är att Azure Search vanligtvis kan generera ett standardindexschema åt dig genom att läsa källdatauppsättningens metadata. Du kan ändra det genererade indexet tills indexet har bearbetats. Därefter är de enda schemaändringarna som tillåts de som inte kräver omindexering. Om de ändringar som du vill göra påverkar schemat direkt måste indexet återskapas. 

## <a name="verify-data-import-with-search-explorer"></a>Verifiera dataimporten med Sökutforskaren

Ett snabbt sätt att utföra en preliminär kontroll av dokumentöverföringen är att använda **Sökutforskaren** i portalen. Med utforskaren kan du köra frågor mot ett index utan att behöva skriva kod. Sökupplevelse baseras på standardinställningar, t.ex. [enkel syntax](/rest/api/searchservice/simple-query-syntax-in-azure-search) och [searchMode-frågeparametern](/rest/api/searchservice/search-documents). Resultaten returneras i JSON så att du kan gå igenom hela dokumentet.

> [!TIP]
> Många [Azure Search-kodexempel](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) innehåller inbäddade eller lättillgängliga datauppsättningar, som hjälper dig att snabbt komma igång. Portalen innehåller också en exempelindexerare och datakälla som består av en liten datauppsättning med fastighetsinformation (med namnet ”realestate-us-sample”). När du kör den förkonfigurerade indexeraren på datakällan exemplet skapas ett index och fylls med dokument som kan efterfrågas i Sökutforskaren eller med kod som du skriver.

## <a name="see-also"></a>Se också

+ [Översikt över indexeraren](search-indexer-overview.md)
+ [Steg-för-steg-beskrivning av portalen: skapa, läsa in och avfråga ett index](search-get-started-portal.md)
