---
title: Återskapa ett Azure Kognitiv sökning-index
titleSuffix: Azure Cognitive Search
description: Lägg till nya element, uppdatera befintliga element eller dokument eller ta bort föråldrade dokument i en fullständig återskapning eller delvis stegvis indexering för att uppdatera ett Azure Kognitiv sökning-index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1301ee2494aa24720905b9e3e68edce04fb17e72
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466440"
---
# <a name="how-to-rebuild-an-azure-cognitive-search-index"></a>Återskapa ett Azure Kognitiv sökning-index

I den här artikeln förklaras hur du återaktiverar ett Azure Kognitiv sökning-index, under vilka omständigheter som återuppbyggnadar krävs och rekommendationer för att minimera effekten av återuppbyggnad av pågående fråge förfrågningar.

En *Rebuild* syftar på att släppa och återskapa de fysiska data strukturer som är associerade med ett index, inklusive alla fältbaserade inverterade index. I Azure Kognitiv sökning kan du inte släppa och återskapa enskilda fält. För att återskapa ett index måste alla fält lagrings enheter tas bort, återskapas baserat på ett befintligt eller uppdaterat index schema och sedan fyllas i igen med data som skickas till indexet eller hämtas från externa källor. Det är vanligt att bygga om index under utvecklingen, men du kan också behöva återskapa ett index på produktions nivå för att hantera strukturella ändringar, till exempel lägga till komplexa typer eller lägga till fält i förslag.

Till skillnad från återskapande som tar ett index offline körs *data uppdatering* som en bakgrunds aktivitet. Du kan lägga till, ta bort och ersätta dokument med minimala avbrott för att ställa frågor till arbets belastningar, även om frågor vanligt vis tar längre tid att slutföra. Mer information om hur du uppdaterar index innehåll finns i [lägga till, uppdatera eller ta bort dokument](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="rebuild-conditions"></a>Återskapa villkor

| Tillstånd | Beskrivning |
|-----------|-------------|
| Ändra en fält definition | Att ändra ett fält namn, data typ eller specifika [indexattribut](https://docs.microsoft.com/rest/api/searchservice/create-index) (sökbar, Filterable, sorterbar, aspektbar) kräver en fullständig återuppbyggnad. |
| Tilldela ett fält till en analys | [Analys](search-analyzers.md) verktyg definieras i ett index och tilldelas sedan till fält. Du kan lägga till en ny analys definition till ett index när som helst, men du kan bara *tilldela* en analys när fältet har skapats. Detta gäller både för egenskaperna **Analyzer** och **indexAnalyzer** . Egenskapen **searchAnalyzer** är ett undantag (du kan tilldela den här egenskapen till ett befintligt fält). |
| Uppdatera eller ta bort en analys definition i ett index | Du kan inte ta bort eller ändra en befintlig analys konfiguration (Analyzer, tokenizer, token filter eller char filter) i indexet om du inte bygger om hela indexet. |
| Lägg till ett fält till en förslags ställare | Om det redan finns ett fält och du vill lägga till det i en [förslags](index-add-suggesters.md) konstruktion måste du återskapa indexet. |
| Ta bort ett fält | Om du vill ta bort alla spår för ett fält fysiskt måste du återskapa indexet. När en omedelbar återuppbyggnad inte är praktisk kan du ändra program koden för att inaktivera åtkomst till fältet "borttaget". Fysiskt är fält definitionen och innehållet kvar i indexet tills nästa återuppbyggnad när du använder ett schema som utelämnar fältet i fråga. |
| Växlings nivåer | Om du behöver mer kapacitet finns det ingen uppgradering på plats i Azure Portal. En ny tjänst måste skapas och index måste byggas från grunden på den nya tjänsten. Du kan automatisera processen genom att använda exempel koden **index-Backup-Restore** i den här [Azure kognitiv sökning .net-exempel lagrings platsen](https://github.com/Azure-Samples/azure-search-dotnet-samples). Den här appen kommer att säkerhetskopiera ditt index till en serie JSON-filer och sedan återskapa indexet i en Sök tjänst som du anger.|

Eventuella andra ändringar kan göras utan att det påverkar befintliga fysiska strukturer. Mer specifikt kräver följande ändringar *inte* en index återuppbyggnad:

+ Lägg till ett nytt fält
+ Ange det **hämtnings** bara attributet för ett befintligt fält
+ Ange ett **searchAnalyzer** för ett befintligt fält
+ Lägga till en ny analys definition i ett index
+ Lägga till, uppdatera eller ta bort bedömnings profiler
+ Lägga till, uppdatera eller ta bort CORS-inställningar
+ Lägg till, uppdatera eller ta bort synonymMaps

När du lägger till ett nytt fält får befintliga indexerade dokument ett null-värde för det nya fältet. Vid en framtida data uppdatering ersätter värden från externa källdata de null-värden som läggs till av Azure Kognitiv sökning. Mer information om hur du uppdaterar index innehåll finns i [lägga till, uppdatera eller ta bort dokument](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="partial-indexing"></a>Partiell indexering

I Azure Kognitiv sökning kan du inte styra indexeringen baserat på varje fält och välja att ta bort eller återskapa specifika fält. Det finns inte heller någon inbyggd mekanism för [indexering av dokument baserat på kriterier](https://stackoverflow.com/questions/40539019/azure-search-what-is-the-best-way-to-update-a-batch-of-documents). Eventuella krav för villkors driven indexering måste uppfyllas genom anpassad kod.

Vad du kan göra enkelt är att *Uppdatera dokument* i ett index. För många Sök lösningar är externa källdata temporära och synkronisering mellan källdata och ett sökindex är en vanlig metod. I kod anropar du åtgärden [Lägg till, uppdatera eller ta bort dokument](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) eller [.net motsvarande](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexesoperationsextensions.createorupdate?view=azure-dotnet) för att uppdatera index innehåll, eller för att lägga till värden för ett nytt fält.

## <a name="partial-indexing-with-indexers"></a>Partiell indexering med indexerare

[Indexerare](search-indexer-overview.md) fören klar data uppdaterings åtgärden. En indexerare kan bara indexera en tabell eller vy i den externa data källan. För att indexera flera tabeller är det enklaste sättet att skapa en vy som kopplar tabeller och projekt till de kolumner som du vill indexera. 

När du använder indexerare som crawlar externa data källor, kontrollerar du att det finns en "hög vatten märkes kolumn" i källdata. Om det finns en sådan kan du använda den för stegvis ändrings identifiering genom att bara hämta de rader som innehåller nytt eller uppdaterat innehåll. För [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection)används ett `lastModified`-fält. I [Azure Table Storage](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection)har `timestamp` samma syfte. På samma sätt har både [Azure SQL Database indexerare](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) och [Azure Cosmos db indexerare](search-howto-index-cosmosdb.md#indexing-changed-documents) fält för att flagga rad uppdateringar. 

Mer information om indexerare finns i [Översikt över indexerare](search-indexer-overview.md) och [återställ indexerare REST API](https://docs.microsoft.com/rest/api/searchservice/reset-indexer).

## <a name="how-to-rebuild-an-index"></a>Återskapa ett index

Planera regelbundet, fullständig återuppbyggnad under aktiv utveckling, när index scheman är i ett flödes skick. För program som redan finns i produktion rekommenderar vi att du skapar ett nytt index som kör sida vid sida ett befintligt index för att undvika frågor om avbrott.

Läs-och Skriv behörighet på service nivå krävs för index uppdateringar. 

Det går inte att återskapa ett index i portalen. Program mässigt kan du anropa [uppdaterings Index REST API](https://docs.microsoft.com/rest/api/searchservice/update-index) eller [motsvarande .NET-API: er](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexesoperations.createorupdatewithhttpmessagesasync?view=azure-dotnet) för en fullständig återuppbyggnad. En begäran om uppdaterings index är identisk med [create index REST API](https://docs.microsoft.com/rest/api/searchservice/create-index), men har en annan kontext.

Följande arbets flöde är fördelat mot REST API, men gäller även för .NET SDK.

1. När du återanvänder ett index namn [släpper du det befintliga indexet](https://docs.microsoft.com/rest/api/searchservice/delete-index). 

   Alla frågor som är riktade mot indexet släpps direkt. Att ta bort ett index går inte att ångra, förstöra fysiskt lagrings utrymme för fält samlingen och andra konstruktioner. Se till att du är klar med konsekvenserna av att ta bort ett index innan du släpper det. 

2. Formulera en begäran om [uppdaterings index](https://docs.microsoft.com/rest/api/searchservice/update-index) med tjänstens slut punkt, API-nyckel och en [Administratörs nyckel](https://docs.microsoft.com/azure/search/search-security-api-keys). En administratörs nyckel krävs för Skriv åtgärder.

3. I bröd texten i begäran, ange ett index schema med de ändrade eller ändrade fält definitionerna. Begär ande texten innehåller index schemat, samt konstruktioner för bedömnings profiler, analyser, förslag och CORS-alternativ. Schema krav dokumenteras i [create index](https://docs.microsoft.com/rest/api/searchservice/create-index).

4. Skicka en begäran om att [Uppdatera index](https://docs.microsoft.com/rest/api/searchservice/update-index) för att återskapa det fysiska uttrycket för indexet på Azure kognitiv sökning. 

5. [Läs in indexet med dokument](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) från en extern källa.

När du skapar ett index allokeras det fysiska lagrings utrymmet för varje fält i index schemat, med ett inverterat index som skapats för varje sökbart fält. Fält som inte är sökbara kan användas i filter eller uttryck, men inte har inverterade index och är inte full text eller fuzzy sökbar. Vid en index återskapning tas dessa inverterade index bort och återskapas baserat på det index schema som du anger.

När du läser in indexet fylls varje fälts inverterade index med alla unika, token ord från varje dokument, med en karta till motsvarande dokument-ID. Om du till exempel indexerar en hotell data uppsättning kan ett inverterat index som skapats för ett stads fält innehålla villkor för Seattle, Göteborg och så vidare. Dokument som innehåller Seattle eller Göteborg i fältet stad har sitt dokument-ID som anges tillsammans med termen. På alla åtgärder för att [lägga till, uppdatera eller ta bort](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) uppdateras villkoren och dokument-ID-listan.

> [!NOTE]
> Om du har stränga SLA-krav kan du överväga att tillhandahålla en ny tjänst särskilt för detta arbete, med utveckling och indexering som inträffar i fullständig isolering från ett produktions index. En separat tjänst körs på sin egen maskin vara, vilket eliminerar risken för resurs konkurrens. När utvecklingen är klar skulle du antingen lämna det nya indexet på plats, omdirigera frågor till den nya slut punkten och indexet, eller så kan du köra slutförd kod för att publicera ett reviderat index på den ursprungliga Azure Kognitiv sökning-tjänsten. Det finns för närvarande ingen mekanism för att flytta ett klart att använda-index till en annan tjänst.

## <a name="view-updates"></a>Visa uppdateringar

Du kan börja fråga ett index så snart det första dokumentet har lästs in. Om du känner till ett dokuments ID returnerar [Sök dokumentet REST API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) det aktuella dokumentet. För bredare testning bör du vänta tills indexet har lästs in och sedan använda frågor för att kontrol lera den kontext som du förväntar dig att se.

## <a name="see-also"></a>Se även

+ [Översikt över indexeraren](search-indexer-overview.md)
+ [Indexera stora data mängder i skala](search-howto-large-index.md)
+ [Indexering i portalen](search-import-data-portal.md)
+ [Azure SQL Database indexerare](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB-indexeraren](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage-indexeraren](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage-indexeraren](search-howto-indexing-azure-tables.md)
+ [Säkerhet i Azure Kognitiv sökning](search-security-overview.md)