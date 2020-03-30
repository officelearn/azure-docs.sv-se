---
title: Importera data till ett sökindex med Azure-portalen
titleSuffix: Azure Cognitive Search
description: Lär dig hur du använder guiden Importera data i Azure-portalen för att genomsöka Azure-data från Cosmos DB, Blob storage, tabelllagring, SQL Database och SQL Server på virtuella Azure-datorer.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0ed2bd7f1e03d8d5fa11f7e76010d087605f0fe1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460696"
---
# <a name="import-data-wizard-for-azure-cognitive-search"></a>Guiden Importera data för Azure Cognitive Search

Azure-portalen tillhandahåller en **guiden Importera data** på instrumentpanelen för Azure Cognitive Search för prototyper och inläsning av ett index. Den här artikeln innehåller fördelar och begränsningar med att använda guiden, indata och utdata och viss användningsinformation. Praktisk vägledning för att gå igenom guiden med hjälp av inbyggda exempeldata finns i [Snabbstarten Skapa en Azure Cognitive Search med snabbstarten för Azure-portalen.](search-get-started-portal.md)

Åtgärder som den här guiden utför omfattar:

1 - Anslut till en Azure-datakälla som stöds.

2 - Skapa ett indexschema, som härleds av samplingskälladata.

3 - Alternativt, lägga till AI-anrikningar för att extrahera eller generera innehåll och struktur.

4 - Kör guiden för att skapa objekt, importera data, ange ett schema och andra konfigurationsalternativ.

Guiden matar ut ett antal objekt som sparas i söktjänsten, som du kan komma åt programatiskt eller i andra verktyg.

## <a name="advantages-and-limitations"></a>Fördelar och begränsningar

Innan du skriver någon kod kan du använda guiden för prototyper och proof-of-concept-testning. Guiden ansluter till externa datakällor, tar prover på data för att skapa ett första index och importerar sedan data som JSON-dokument till ett index på Azure Cognitive Search. 

Sampling är den process genom vilken ett indexschema härleds och det har vissa begränsningar. När datakällan skapas väljer guiden ett urval av dokument för att bestämma vilka kolumner som ingår i datakällan. Alla filer läss inte, eftersom det kan ta timmar för mycket stora datakällor. Med ett urval av dokument används källmetadata, till exempel fältnamn eller typ, för att skapa en fältsamling i ett indexschema. Beroende på komplexiteten i källdata kan du behöva redigera det ursprungliga schemat för noggrannhet eller utöka det för fullständighet. Du kan göra dina ändringar infogade på indexdefinitionssidan.

Sammantaget är fördelarna med att använda guiden tydliga: så länge kraven uppfylls kan du skapa prototyper av ett frågebart index inom några minuter. Vissa av komplexiteten i indexering, till exempel tillhandahålla data som JSON-dokument, hanteras av guiden.

Kända begränsningar sammanfattas på följande sätt:

+ Guiden stöder inte iteration eller återanvändning. Varje passage genom guiden skapar en ny index-, kunskapers- och indexkonfiguration. Endast datakällor kan sparas och återanvändas i guiden. Om du vill redigera eller förfina andra objekt måste du använda REST-API:erna eller .NET SDK för att hämta och ändra strukturerna.

+ Källinnehåll måste finnas i en Azure-datakälla som stöds.

+ Sampling är över en delmängd av källdata. För stora datakällor är det möjligt för guiden att missa fält. Du kan behöva utöka schemat eller korrigera de härledda datatyperna om samplingen är otillräcklig.

+ AI-anrikning, som exponeras i portalen, är begränsad till några inbyggda färdigheter. 

+ Ett [kunskapsarkiv](knowledge-store-concept-intro.md), som kan skapas av guiden, är begränsat till några standardprojektioner. Om du vill spara utökade dokument som skapats av guiden levereras blob-behållaren och tabellerna med standardnamn och struktur.

<a name="data-source-inputs"></a>

## <a name="data-source-input"></a>Indatakälla

Guiden **Importera data** ansluter till en extern datakälla med hjälp av den interna logiken som tillhandahålls av Azure Cognitive Search-indexerare, som är utrustade för att prova källan, läsa metadata, knäcka dokument för att läsa innehåll och struktur och serialisera innehåll som JSON för efterföljande import till Azure Cognitive Search.

Du kan bara importera från en enda tabell, databasvy eller motsvarande datastruktur, men strukturen kan innehålla hierarkiska eller kapslade understrukturer. Mer information finns i [Så här modellerar du komplexa typer](search-howto-complex-data-types.md).

Du bör skapa den här enstaka tabellen eller vyn innan du kör guiden och den måste innehålla innehåll. Av uppenbara skäl är det inte meningsfullt att köra guiden **Importera data** på en tom datakälla.

|  Val | Beskrivning |
| ---------- | ----------- |
| **Befintlig datakälla** |Om du redan har indexerare definierade i söktjänsten kan du ha en befintlig definition av datakällan som du kan återanvända. I Azure Cognitive Search används datakällobjekt endast av indexerare. Du kan skapa ett datakällobjekt programmässigt eller via guiden **Importera data** och återanvända dem efter behov.|
| **Prover**| Azure Cognitive Search innehåller två inbyggda exempeldatakällor som används i självstudier och snabbstarter: en SQL-databas för fastigheter och en Hotels-databas som finns på Cosmos DB. En genomgång baserat på exemplet Hotell finns [i snabbstarten Skapa ett index i Azure-portalen.](search-get-started-portal.md) |
| [**Azure SQL Database**](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) |Tjänstens namn, autentiseringsuppgifterna för en databasanvändare med läsbehörighet och ett databasnamn kan anges på sidan eller via en ADO.NET-anslutningssträng. Välj alternativet för anslutningssträngar till att visa eller anpassa egenskaperna. <br/><br/>Tabellen eller vyn som visar raduppsättningen måste anges på sidan. Det här alternativet visas när anslutningen lyckats, med en listruta där du kan välja det du behöver.|
| **SQL Server på virtuella Azure-datorer** |Ange ett fullständigt kvalificerat tjänstnamn, användar-ID och lösenord samt en databas som en anslutningssträng. Om du vill använda den här datakällan måste du tidigare ha installerat ett certifikat i det lokala arkiv som krypterar anslutningen. Instruktioner finns i [SQL VM-anslutning till Azure Cognitive Search](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md). <br/><br/>Tabellen eller vyn som visar raduppsättningen måste anges på sidan. Det här alternativet visas när anslutningen lyckats, med en listruta där du kan välja det du behöver. |
| [**Azure Cosmos DB**](search-howto-index-cosmosdb.md)|Kraven innefattar konto, databas och samling. Alla dokument i samlingen kommer att ingå i indexet. Du kan definiera en fråga som ska förenklas eller filtrera raduppsättningen eller lämna frågan tom. En fråga krävs inte i den här guiden.|
| [**Azure Blob-lagring**](search-howto-indexing-azure-blob-storage.md) |Kraven innefattar lagringskonto och en container. Om blob-namnet följer en virtuell namngivningskonvention i grupperingssyfte, kan du också ange den virtuella katalogdelen av namnet som en mapp under containern. Se [Indexera Blob Storage](search-howto-indexing-azure-blob-storage.md) för mer information. |
| [**Lagring av Azure-bord**](search-howto-indexing-azure-tables.md) |Kraven innefattar lagringskontot och ett tabellnamn. Du kan också ange en fråga för att hämta en delmängd av tabellerna. Se [Indexera Table Storage](search-howto-indexing-azure-tables.md) för mer information. |

## <a name="wizard-output"></a>Utskrift av guiden

Bakom kulisserna skapar, konfigurerar och anropar guiden följande objekt. När guiden har körts hittar du utdata på portalsidorna. Sidan Översikt för tjänsten innehåller listor över index, indexerare, datakällor och kunskaper. Indexdefinitioner kan visas i sin helhet JSON i portalen. För andra definitioner kan du använda [REST API](https://docs.microsoft.com/rest/api/searchservice/) för att hämta specifika objekt.

| Objekt | Beskrivning | 
|--------|-------------|
| [Datakälla](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Beständig anslutningsinformation till källdata, inklusive autentiseringsuppgifter. Ett datakällobjekt används uteslutande med indexerare. | 
| [Index](https://docs.microsoft.com/rest/api/searchservice/create-index) | Fysisk datastruktur som används för fulltextsökning och andra frågor. | 
| [Skillset](https://docs.microsoft.com/rest/api/searchservice/create-skillset) | En komplett uppsättning instruktioner för att manipulera, omvandla och forma innehåll, inklusive att analysera och extrahera information från bildfiler. Med undantag för mycket enkla och begränsade strukturer innehåller den en referens till en Cognitive Services-resurs som tillhandahåller berikning. Det kan också innehålla en definition av kunskapsarkiv.  | 
| [Indexerare](https://docs.microsoft.com/rest/api/searchservice/create-indexer)  | Ett konfigurationsobjekt som anger en datakälla, målindex, en valfri kompetens, valfritt schema och valfria konfigurationsinställningar för felbehållning och bas-64-kodning. |


## <a name="how-to-start-the-wizard"></a>Starta guiden

Guiden Importera data startas från kommandofältet på sidan Översikt över tjänsten.

1. Öppna söktjänstsidan från instrumentpanelen i [Azure-portalen](https://portal.azure.com)eller [hitta din tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) i tjänstlistan.

2. Klicka på Importera data högst upp på sidan **Serviceöversikt**.

   ![Kommandot Importera data i portalen](./media/search-import-data-portal/import-data-cmd2.png "Starta guiden Importera data")

Du kan också starta **Importera data** från andra Azure-tjänster, inklusive Azure Cosmos DB, Azure SQL Database och Azure Blob-lagring. Leta efter **Lägg till Azure Cognitive Search** i det vänstra navigeringsfönstret på sidan för tjänstöversikt.

<a name="index-definition"></a>

## <a name="how-to-edit-or-finish-an-index-schema-in-the-wizard"></a>Redigera eller avsluta ett indexschema i guiden

Guiden genererar ett ofullständigt index som fylls i med dokument som hämtats från indatakällan. För ett funktionellt index kontrollerar du att du har definierat följande element.

1. Är fältlistan klar? Lägg till nya fält som har missats och ta bort alla som inte tillför värde till en sökupplevelse eller som inte används i ett [filteruttryck](search-query-odata-filter.md) eller [en bedömningsprofil](index-add-scoring-profiles.md).

1. Är datatypen lämplig för inkommande data? Azure Cognitive Search stöder [EDM-datatyperna (Entity Data Model).](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) För Azure SQL-data finns det [mappningsdiagram](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#TypeMapping) som innehåller likvärdiga värden. Mer bakgrund finns [i Fältmappningar och omvandlingar](search-indexer-field-mappings.md).

1. Har du ett fält som kan fungera som *nyckel?* Det här fältet måste vara Edm.string och det måste unikt identifiera ett dokument. För relationsdata kan den mappas till en primärnyckel. För blobbar kan det `metadata-storage-path`vara . Om fältvärden innehåller blanksteg eller streck måste du ange alternativet **Bas-64 Kodnyckel** i steget **Skapa en indexerare** under **Avancerade alternativ**för att undertrycka valideringskontrollen för dessa tecken.

1. Ange attribut för att avgöra hur fältet används i ett index. 

   Ta dig tid med det här steget eftersom attribut avgör det fysiska uttrycket för fält i indexet. Om du vill ändra attribut senare, även programmässigt, måste du nästan alltid släppa och återskapa indexet. Grundläggande attribut som **Sökbara** och **Hämtningsbara** har en [försumbar inverkan på lagring](search-what-is-an-index.md#index-size). Om du aktiverar filter och använder förslagsföreläggare ökar lagringskraven. 
   
   + **Sökbar** möjliggör fulltextsökning. Varje fält som används i frågor i fri form eller i frågeuttryck måste ha det här attributet. Inverterade index skapas för varje fält som du markerar som **sökbart**.

   + **Hämtningsbar** returnerar fältet i sökresultaten. Varje fält som tillhandahåller innehåll till sökresultaten måste ha det här attributet. Om du anger det här fältet påverkas inte indexstorleken märkbart.

   + **Filterbar** gör att fältet kan refereras i filteruttryck. Varje fält som används i ett **$filter** uttryck måste ha det här attributet. Filteruttryck är för exakta matchningar. Eftersom textsträngarna förblir intakta krävs ytterligare lagringsutrymme för att hantera det fullständiga innehållet.

   + **Facetable** möjliggör fältet för fasterad navigering. Endast fält som också är markerade som **Filterbara** kan markeras som **Facetable**.

   + **Sorterbar** gör att fältet kan användas i en sortering. Varje fält som används i ett **$Orderby** uttryck måste ha det här attributet.

1. Behöver du [lexikal analys?](search-lucene-query-architecture.md#stage-2-lexical-analysis) För Edm.string-fält som är **sökbara**kan du ange en **Analysator** om du vill ha språkförbättrad indexering och frågor. 

   Standard är *Standard Lucene* men du kan välja *Microsoft English* om du vill använda Microsofts analysator för avancerad lexikal bearbetning, till exempel lösa oregelbundna substantiv och verbformer. Endast språkanalysatorer kan anges i portalen. Med hjälp av en anpassad analysator eller en icke-språkanalysator som Nyckelord, Mönster och så vidare, måste göras programmässigt. Mer information om analysatorer finns i [Lägga till språkanalysatorer](search-language-support.md).

1. Behöver du typeahead-funktioner i form av komplettera automatiskt eller föreslagna resultat? Markera kryssrutan **Föreslå** kryssrutan Föreslå om du vill aktivera [typeahead-frågeförslag och komplettera](index-add-suggesters.md) automatiskt i markerade fält. Förslagsföreare lägger till antalet tokeniserade termer i ditt index och förbrukar därmed mer lagringsutrymme.


## <a name="next-steps"></a>Nästa steg

Det bästa sättet att förstå fördelarna och begränsningarna med guiden är att gå igenom den. Följande snabbstart guidar dig genom varje steg.

> [!div class="nextstepaction"]
> [Skapa ett Azure Cognitive Search-index med Azure-portalen](search-get-started-portal.md)