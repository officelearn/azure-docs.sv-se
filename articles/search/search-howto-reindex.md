---
title: Återskapa en Azure Search-index eller uppdatera sökbart innehåll – Azure Search
description: Lägga till nya element, uppdatera befintliga element eller dokument eller ta bort föråldrade dokument i en fullständig återskapning eller partiella inkrementella indexering för att uppdatera ett Azure Search-index.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 55de72b2a82dea3dfe763d786966565beb229042
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53745099"
---
# <a name="how-to-rebuild-an-azure-search-index"></a>Återskapar ett Azure Search-index

Den här artikeln beskriver hur du återskapa en Azure Search-index, omständigheter som behöver krävs och rekommendationer för att minimera effekten av bygger på pågående frågebegäranden.

En *återskapa* avser släppa och återskapa de fysiska datastrukturer som är associerade med ett index, inklusive alla fält-baserade vägar i inverterad index. Du kan inte ta bort och återskapa specifika fält i Azure Search. Om du vill bygga om index, lagring för alla fält tas bort, återskapas baserat på ett befintligt eller ändrade indexschema och sedan fyllas i på nytt med data som skickas till indexet eller hämtas från externa källor. Det är vanligt att bygga om index under utvecklingen, men du kan också behöva återskapa ett produktionsnivån index för att hantera strukturella förändringar, till exempel lägga till komplexa typer.

Till skillnad från behöver som kopplar ett index *datauppdatering* körs som en bakgrundsaktivitet. Du kan lägga till, ta bort och ersätter dokument med minimala störningar för frågearbetsbelastningar, även om frågor vanligtvis tar längre tid att slutföra. Mer information om hur du uppdaterar indexera innehåll finns i [lägga till, uppdatera eller ta bort dokument](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="rebuild-conditions"></a>Återskapa villkor

| Tillstånd | Beskrivning |
|-----------|-------------|
| Ändra en fältdefinition | Ändra ett namn, datatyp eller specifika [indexattribut](https://docs.microsoft.com/rest/api/searchservice/create-index) (sökbar, filtrerbar, sorterbar, fasettbar) kräver återskapning av en fullständig. |
| Tar bort ett fält | Du måste återskapa indexet för att ta bort alla spår av ett fält fysiskt. När en omedelbar återskapning inte är praktiskt, ändra programkod för att inaktivera åtkomst till fältet ”borttagen” i de flesta utvecklare. Fysiskt, förblir fältdefinition och innehåll i indexet tills nästa återskapandet, med hjälp av ett schema som utesluter fältet i fråga. |
| Växla nivåer | Om du behöver mer kapacitet, finns det ingen uppgradering på plats. En ny tjänst skapas på den nya kapacitet punkten och index skapas från början på den nya tjänsten. |

Alla andra ändringar kan göras utan att påverka befintliga fysiska strukturer. Mer specifikt kan följande ändringar gör *inte* indikerar en återskapning av ett index:

+ Lägg till ett nytt fält
+ Ange den **hämtningsbara** attributet på ett befintligt fält
+ Ange en analyzer för ett befintligt fält
+ Lägga till, uppdatera eller ta bort bedömningsprofiler
+ Lägga till, uppdatera eller ta bort CORS-inställningar
+ Lägga till, uppdatera eller ta bort förslagsställare
+ Lägga till, uppdatera eller ta bort synonymMaps

När du lägger till ett nytt fält, ges befintliga indexerade dokumenten ett null-värde för det nya fältet. Vid en framtida datauppdatering Ersätt värden från externa källdata null-värden har lagts till av Azure Search.

## <a name="partial-or-incremental-indexing"></a>Partiell eller inkrementell indexering

I Azure Search kan inte kan du styra indexering på basis av per fält, välja att ta bort eller återskapa specifika fält. På samma sätt kan det finns ingen inbyggd mekanism för [indexera dokument baserat på kriterier](https://stackoverflow.com/questions/40539019/azure-search-what-is-the-best-way-to-update-a-batch-of-documents). Alla krav som du har för kriterier-drivna indexering måste vara uppfyllda genom anpassad kod.

Vad du kan göra enkelt, men är *uppdatera dokument* i ett index. För många söklösningar extern källa data är temporära och synkroniseringen mellan datakällor och en search-index är en vanlig metod. I koden anropar den [lägga till, uppdatera eller ta bort dokument](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) åtgärden eller [.NET motsvarande](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexesoperationsextensions.createorupdate?view=azure-dotnet) att uppdatera indexet innehåll eller lägga till värden för ett nytt fält.

## <a name="partial-indexing-with-indexers"></a>Partiellt indexering med indexerare

[Indexerare](search-indexer-overview.md) förenklar för uppdatering av data. En indexerare kan endast indexera en tabell eller vy i den externa datakällan. Om du vill indexera flera tabeller, är det enklaste sättet att skapa en vy som ansluter till tabeller och projekt de kolumner som du vill indexera. 

När du använder indexerare som crawlar externa datakällor, Sök efter ett ”vattenmärke”-kolumnen i källdata. Om en sådan finns, kan du använda den för identifiering av inkrementell ändring av genom att välja de rader som innehåller nya eller ändrade innehållet. För [Azure Blob storage](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection), ett `lastModified` fältet används. På [Azure Table storage](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection), `timestamp` har samma funktion. På samma sätt, både [Azure SQL Database-indexeraren](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) och [Azure Cosmos DB-indexeraren](search-howto-index-cosmosdb.md#indexing-changed-documents) ha fält för flagga uppdateringar av rader. 

Läs mer om indexerare [översikt över indexerare](search-indexer-overview.md) och [återställa indexeraren REST API](https://docs.microsoft.com/rest/api/searchservice/reset-indexer).

## <a name="how-to-rebuild-an-index"></a>Så här att bygga om index

Plan för frekventa, fullständig återskapar under aktivt med utveckling, när indexet scheman är i ett tillstånd för som. För program redan i produktion rekommenderar vi att skapa ett nytt index som kan köras sida vid sida ett befintligt index om du vill undvika avbrott i fråga.

Om du har strikta SLA-krav, kan du etablera en ny tjänst specifikt för detta arbete med utveckling och indexering inträffar fullständig avskilt från ett index för produktion. En separat tjänst körs på en egen maskinvara, vilket eliminerar resurskonkurrens risk. När utveckling är klar kan du lämna antingen det nya indexet på plats, omdirigerar frågor till ny slutpunkt och index eller kör du färdiga koden för att publicera ett reviderade index för Azure Search-tjänsten. Det finns för närvarande ingen mekanism för att flytta ett färdiga att använda index till en annan tjänst.

Läs-och skrivbehörighet på servicenivån måste anges för index uppdateringar. Programmässigt, kan du anropa [Update Index REST API](https://docs.microsoft.com/rest/api/searchservice/update-index) eller .NET API: er för en fullständig återskapning. Begäran är identiska med [skapa Index REST API](https://docs.microsoft.com/rest/api/searchservice/create-index), men har en annan kontext.

1. Om du återanvänder Indexnamnet, [ta bort det befintliga indexet](https://docs.microsoft.com/rest/api/searchservice/delete-index). Alla frågor som riktar in sig på index släpps omedelbart. Ta bort ett index kan inte ångras, förstöra fysiska lagringsplatsen för fältsamlingen och andra konstruktioner. Kontrollera att du är tydliga på följderna av att ta bort ett index innan du släpper den. 

2. Ange ett indexschema med ändrade eller ändrade fältdefinitioner. Schema-krav är dokumenterade i [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

3. Ange en [administratörsnyckel](https://docs.microsoft.com/azure/search/search-security-api-keys) på begäran.

4. Skicka en [uppdatera Index](https://docs.microsoft.com/rest/api/searchservice/update-index) kommando för att återskapa den fysiska index i Azure Search-uttrycken. Begärandetexten innehåller indexschemat samt konstruktioner för bedömning profiler, analysverktyg, förslagsställare och CORS-alternativ.

5. [Läsa in indexet med dokument](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) från en extern källa. Du kan också använda detta API om du uppdaterar en befintlig, oförändrade indexschema med uppdaterade dokument.

När du skapar indexet har fysisk lagring allokerats för varje fält i indexschemat, med ett vägar i inverterad index som skapats för varje sökbara fält. Fälten är den inte sökbart kan användas i filter eller uttryck, men inte inverterade index och inte är fulltext sökbara. På ett index återskapa dessa vägar i inverterad index tas bort och skapas baserat på indexschema som du anger.

När du läser in indexet fylls vägar i inverterad index för varje fält med alla unika, principfilerna ord från varje dokument med en karta till motsvarande dokument-ID: N. När du indexerar en datauppsättning för hotell, kan ett vägar i inverterad index som skapats för ett fält med stad till exempel innehålla villkoren för Seattle, Portland och så vidare. Dokument som innehåller Seattle eller Portland i fältet Stad skulle ha sina dokument-ID som visas tillsammans med termen. På någon [lägga till, uppdatera eller ta bort](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) åtgärden, villkor och listan över standarddokument ID uppdateras.

## <a name="view-updates"></a>Visa uppdateringar

Du kan börja fråga ett index så snart det första dokumentet har lästs in. Om du känner till ett dokument-ID, den [Lookup dokumentet REST API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) returnerar det specifika dokumentet. För större testning bör du vänta tills indexet har lästs in helt och sedan använda frågor för att verifiera kontexten som du förväntar dig att se.

## <a name="see-also"></a>Se också

+ [Översikt över indexeraren](search-indexer-overview.md)
+ [Index stora datauppsättningar i stor skala](search-howto-large-index.md)
+ [Indexeringen i portalen](search-import-data-portal.md)
+ [Azure SQL Database-indexeraren](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB-indexeraren](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage-indexeraren](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage-indexeraren](search-howto-indexing-azure-tables.md)
+ [Säkerhet i Azure Search](search-security-overview.md)