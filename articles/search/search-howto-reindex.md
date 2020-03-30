---
title: Återskapa ett sökindex
titleSuffix: Azure Cognitive Search
description: Lägg till nya element, uppdatera befintliga element eller dokument eller ta bort föråldrade dokument i en fullständig återskapande eller partiell indexering för att uppdatera ett Azure Cognitive Search-index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 58b60a0eee8ab407709f33911d3c6b13ffbf301a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77498383"
---
# <a name="how-to-rebuild-an-index-in-azure-cognitive-search"></a>Återskapa ett index i Azure Cognitive Search

I den här artikeln beskrivs hur du återskapar ett Azure Cognitive Search-index, under vilka omständigheter det krävs om återskapanden och rekommendationer för att minska effekten av återskapanden på pågående frågebegäranden.

En *ombyggnad* avser att släppa och återskapa de fysiska datastrukturer som är associerade med ett index, inklusive alla fältbaserade inverterade index. I Azure Cognitive Search kan du inte släppa och återskapa enskilda fält. Om du vill återskapa ett index måste all fältlagring tas bort, återskapas baserat på ett befintligt eller reviderat indexschema och sedan fyllas i med data som skickas till indexet eller hämtas från externa källor. 

Det är vanligt att återskapa index under utveckling, men du kan också behöva återskapa ett index på produktionsnivå för att hantera strukturella ändringar, till exempel lägga till komplexa typer eller lägga till fält till förslagsföreare.

## <a name="rebuild-conditions"></a>Ombyggnadsförhållanden

Släpp och återskapa ett index om något av följande villkor är sant. 

| Villkor | Beskrivning |
|-----------|-------------|
| Ändra en fältdefinition | Att revidera ett fältnamn, datatyp eller specifika [indexattribut](https://docs.microsoft.com/rest/api/searchservice/create-index) (sökbara, filterbara, sorterbara, ansiktsbordiga) kräver en fullständig ombyggnad. |
| Tilldela en analysator till ett fält | [Analysatorer](search-analyzers.md) definieras i ett index och tilldelas sedan till fält. Du kan lägga till en ny analysatordefinition i ett index när som helst, men du kan bara *tilldela* en analysator när fältet skapas. Detta gäller både **analysatorn** och **indexAnalyzer-egenskaperna.** Egenskapen **searchAnalyzer** är ett undantag (du kan tilldela den här egenskapen till ett befintligt fält). |
| Uppdatera eller ta bort en analysatordefinition i ett index | Du kan inte ta bort eller ändra en befintlig analysatorkonfiguration (analysator, tokenizer, tokenfilter eller teckenfilter) i indexet om du inte återskapar hela indexet. |
| Lägga till ett fält i en förslagsförenare | Om det redan finns ett fält och du vill lägga till det i en [förslagsförelänningskonstruktion](index-add-suggesters.md) måste du återskapa indexet. |
| Ta bort ett fält | Om du vill ta bort alla spår av ett fält fysiskt måste du återskapa indexet. När en omedelbar ombyggnad inte är praktisk kan du ändra programkoden för att inaktivera åtkomsten till fältet "borttaget" eller använda [parametern $select för](search-query-odata-select.md) att välja vilka fält som ska representeras i resultatuppsättningen. Fysiskt finns fältdefinitionen och innehållet kvar i indexet till nästa ombyggnad, när du använder ett schema som utelämnar fältet i fråga. |
| Växla nivåer | Om du behöver mer kapacitet finns det ingen uppgradering på plats i Azure-portalen. En ny tjänst måste skapas och index måste byggas från grunden på den nya tjänsten. För att automatisera den här processen kan du använda exempelkoden **för index-säkerhetskopieringsåterställning** i den här [Azure Cognitive Search .NET-exempelreponyeringen](https://github.com/Azure-Samples/azure-search-dotnet-samples). Den här appen säkerhetskopierar indexet till en serie JSON-filer och återskapar sedan indexet i en söktjänst som du anger.|

## <a name="update-conditions"></a>Uppdatera villkor

Många andra ändringar kan göras utan att påverka befintliga fysiska strukturer. Närmare bestämt kräver följande ändringar *inte* en indexreprisning. För dessa ändringar kan du [uppdatera en indexdefinition](https://docs.microsoft.com/rest/api/searchservice/update-index) med dina ändringar.

+ Lägga till ett nytt fält
+ Ange **attributet hämtningsbart** för ett befintligt fält
+ Ange en **searchAnalyzer** på ett befintligt fält
+ Lägga till en ny analysatordefinition i ett index
+ Lägga till, uppdatera eller ta bort bedömningsprofiler
+ Lägga till, uppdatera eller ta bort CORS-inställningar
+ Lägga till, uppdatera eller ta bort synonymMaps

När du lägger till ett nytt fält får befintliga indexerade dokument ett null-värde för det nya fältet. Vid en framtida datauppdatering ersätter värden från externa källdata de nulls som lagts till av Azure Cognitive Search. Mer information om hur du uppdaterar indexinnehåll finns i [Lägga till, Uppdatera eller Ta bort dokument](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="how-to-rebuild-an-index"></a>Så här återskapar du ett index

Under utvecklingen ändras indexschemat ofta. Du kan planera för det genom att skapa index som kan tas bort, återskapas och laddas om snabbt med en liten representativ datauppsättning.

För program som redan är i produktion rekommenderar vi att du skapar ett nytt index som körs sida vid sida med ett befintligt index för att undvika avbrott i frågan. Programkoden ger omdirigering till det nya indexet.

Indexeringen körs inte i bakgrunden och tjänsten balanserar ytterligare indexering mot pågående frågor. Under indexeringen kan du [övervaka frågebegäranden](search-monitor-queries.md) i portalen för att säkerställa att frågor slutförs i tid.

1. Ta reda på om en ombyggnad krävs. Om du bara lägger till fält eller ändrar någon del av indexet som inte har något samband med fält, kanske du enkelt kan [uppdatera definitionen](https://docs.microsoft.com/rest/api/searchservice/update-index) utan att ta bort, återskapa och helt läsa in den.

1. [Få en indexdefinition](https://docs.microsoft.com/rest/api/searchservice/get-index) om du behöver den för framtida referens.

1. [Släpp det befintliga indexet](https://docs.microsoft.com/rest/api/searchservice/delete-index), förutsatt att du inte kör nya och gamla index sida vid sida. 

   Alla frågor som är inriktade på indexet tas omedelbart bort. Kom ihåg att det går oåterkalleligt att ta bort ett index, vilket förstör fysisk lagring för fältsamlingen och andra konstruktioner. Paus för att tänka på konsekvenserna innan du släpper den. 

1. [Skapa ett reviderat index](https://docs.microsoft.com/rest/api/searchservice/create-index), där brödtexten i begäran innehåller ändrade eller ändrade fältdefinitioner.

1. [Läs in indexet med dokument](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) från en extern källa.

När du skapar indexet allokeras fysisk lagring för varje fält i indexschemat, med ett inverterat index som skapats för varje sökbart fält. Fält som inte är sökbara kan användas i filter eller uttryck, men som inte har inverterade index och är inte fulltextiga eller suddiga sökbara. Vid en indexreprisning tas dessa inverterade index bort och återskapas baserat på det indexschema som du anger.

När du läser in indexet fylls varje fälts inverterade index med alla unika, tokeniserade ord från varje dokument, med en karta till motsvarande dokument-ID. När du till exempel indexerar en hotelldatauppsättning kan ett inverterat index som skapats för ett cityfält innehålla termer för Seattle, Portland och så vidare. Dokument som innehåller Seattle eller Portland i fältet Ort skulle få sitt dokument-ID i listan bredvid termen. Vid en [åtgärd för tillägg, uppdatering eller borttagning](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) uppdateras villkoren och dokument-ID-listan i enlighet med detta.

> [!NOTE]
> Om du har stränga SLA-krav kan du överväga att etablera en ny tjänst specifikt för det här arbetet, med utveckling och indexering som sker helt isolerat från ett produktionsindex. En separat tjänst körs på egen maskinvara, vilket eliminerar alla möjligheter till resurskonkurrens. När utvecklingen är klar lämnar du antingen det nya indexet på plats, omdirigerar frågor till den nya slutpunkten och indexet, eller så kör du färdig kod för att publicera ett reviderat index på din ursprungliga Azure Cognitive Search-tjänst. Det finns för närvarande ingen mekanism för att flytta ett färdigt index till en annan tjänst.

## <a name="check-for-updates"></a>Sök efter uppdateringar

Du kan börja fråga efter ett index så fort det första dokumentet har lästs in. Om du känner till ett dokuments ID returnerar [REST-API:et för uppslagsdokument](https://docs.microsoft.com/rest/api/searchservice/lookup-document) det specifika dokumentet. För bredare testning bör du vänta tills indexet är fullständigt inläst och sedan använda frågor för att verifiera kontexten du förväntar dig att se.

Du kan använda [Sökutforskaren](search-explorer.md) eller ett webbtestverktyg som [Postman](search-get-started-postman.md) för att söka efter uppdaterat innehåll.

Om du har lagt till eller bytt namn på ett fält använder [du $select](search-query-odata-select.md) för att returnera fältet:`search=*&$select=document-id,my-new-field,some-old-field&$count=true`

## <a name="see-also"></a>Se även

+ [Översikt över indexeraren](search-indexer-overview.md)
+ [Indexera stora datamängder i stor skala](search-howto-large-index.md)
+ [Indexering i portalen](search-import-data-portal.md)
+ [Azure SQL Database-indexerare](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB-indexeraren](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage-indexeraren](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage-indexeraren](search-howto-indexing-azure-tables.md)
+ [Säkerhet i Azure Cognitive Search](search-security-overview.md)