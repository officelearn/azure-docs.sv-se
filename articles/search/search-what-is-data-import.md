---
title: "Överföra data med Azure Search| Microsoft Docs"
description: "Lär dig hur du laddar upp data till ett index i Azure Search."
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
editor: 
tags: 
ms.assetid: aa8d47c1-4ae6-4209-a8ce-48d5a9474707
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: ashmaka
ms.openlocfilehash: 5a601b75ec67824e72d8736bc3c45f8e1231ca86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="upload-data-to-azure-search"></a>Ladda upp data till Azure Search
> [!div class="op_single_selector"]
> * [Översikt](search-what-is-data-import.md)
> * [NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

Du kan fylla ett index med dina data på två sätt. Det första alternativet är push-modellen där du skickar dina data manuellt till indexet med hjälp av [REST-API:et](search-import-data-rest-api.md) eller [.NET SDK](search-import-data-dotnet.md) för Azure Search. Det andra alternativet är att [peka en datakälla som stöds](search-indexer-overview.md) på ditt index och låta Azure Search hämta dina data automatiskt.

## <a name="push-data-to-an-index"></a>Skicka data till ett index
Med den här metoden skickar du data via programmering till Azure Search så att de blir tillgängliga för sökning. För program som har mycket låga fördröjningskrav (t.ex. om det är viktigt att sökåtgärder är synkroniserade med dynamiska inventeringsdatabaser) är push-modellen ditt enda alternativ.

Du kan använda [REST-API:et](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) eller [.NET SDK](search-import-data-dotnet.md) för att skicka data till ett index. Det finns för närvarande inget verktygsstöd för att skicka data via portalen.

Den här metoden är mer flexibel än pull-modellen eftersom du kan ladda upp dokument individuellt eller i batchar (upp till 1 000 per batch eller 16 MB, beroende på vilken gräns som nås först). Med push-modellen kan du också ladda upp dokument till Azure Search, oavsett var dina data finns.

Det dataformat som Azure Search förstår är JSON, och alla dokument i datauppsättningen måste ha fält som mappar till fält som definierats i ditt indexschema. 

## <a name="pull-data-into-an-index"></a>Hämta in data till ett index
Datahämtningsmodellen crawlar en datakälla som stöds och överför automatiskt data till ditt index. I Azure Search implementeras den här funktionen genom *indexerare*, som för närvarande är tillgängliga för [Blob Storage](search-howto-indexing-azure-blob-storage.md), [Table Storage](search-howto-indexing-azure-tables.md), [Azure Cosmos DB](http://aka.ms/documentdb-search-indexer), [Azure SQL Database och SQL Server på virtuella datorer i Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md). 

Indexerare ansluter ett index till en datakälla (vanligtvis en tabell, vy eller motsvarande struktur) och mappar källfält till motsvarande fält i indexet. Under körningen omvandlas raduppsättningen automatiskt till JSON och läses in i det angivna indexet. Alla indexerare stöder schemaläggning så att du kan ange hur ofta data ska uppdateras. De flesta indexerare tillhandahåller ändringsspårning om datakällan har stöd för det. Indexerare spårar ändringar och borttagningar av befintliga dokument och identifierar nya dokument, vilket gör att du slipper hantera dina data i indexet aktivt. 

Indexerarfunktioner exponeras på [Azure Portal](search-import-data-portal.md), i [REST-API:et](/rest/api/searchservice/Indexer-operations) och i [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperations). 

En fördel med att använda portalen är att Azure Search vanligtvis kan generera ett standardindexschema åt dig genom att läsa källdatauppsättningens metadata. Du kan ändra det genererade indexet tills indexet har bearbetats. Därefter är de enda schemaändringarna som tillåts de som inte kräver omindexering. Om de ändringar som du vill göra påverkar schemat direkt måste indexet återskapas. 

När indexet är fyllt kan du använda **Sökutforskaren** i kommandofältet på portalen som ett verifieringssteg.

## <a name="query-an-index-using-search-explorer"></a>Köra frågor mot ett index med hjälp av Sökutforskaren

Ett snabbt sätt att utföra en preliminär kontroll av dokumentöverföringen är att använda **Sökutforskaren** på portalen. Med utforskaren kan du köra frågor mot ett index utan att behöva skriva kod. Sökupplevelse baseras på standardinställningar, t.ex. [enkel syntax](/rest/api/searchservice/simple-query-syntax-in-azure-search) och [searchMode-frågeparametern](/rest/api/searchservice/search-documents). Resultaten returneras i JSON så att du kan gå igenom hela dokumentet.

> [!TIP]
> Många [Azure Search-kodexempel](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) innehåller inbäddade eller lättillgängliga datauppsättningar, som hjälper dig att snabbt komma igång. Portalen innehåller också en exempelindexerare och datakälla som består av en liten datauppsättning med fastighetsinformation (med namnet ”realestate-us-sample”). När du kör den förkonfigurerade indexeraren mot exempeldatakällan skapas ett index och fylls med dokument som du kan köra frågor mot i Sökutforskaren eller med hjälp av kod som du skriver.