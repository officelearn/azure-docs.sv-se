---
title: Fråga typer och sammansättning i Azure Search | Microsoft Docs
description: Grunderna för att skapa en sökfråga i Azure Search, med parametrar för att filtrera, markera och sortera resultaten.
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/03/2018
ms.openlocfilehash: 098718293cda1699fb07e09fa81af94a95bbdeca
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39715166"
---
# <a name="query-types-and-composition-in-azure-search"></a>Frågetyper och sammansättning i Azure Search

Fråga sammansättning i Azure Search är en fullständig specifikation av en begäran: matchar villkoren, plus parametrar för att dirigera frågekörning och utforma svaret. En begäran anger vilka fält om du vill inkludera, vilka fält som ska returneras om det ska sortera eller filtrera, och så vidare. Angivet används körs en fråga mot alla sökbara fält som en fullständig text search-åtgärd returnerar en inte har poängsatts resultatuppsättningen i valfri ordning.

## <a name="introduction-by-example"></a>Introduktion med exempel

Exempel är användbara för att illustrera viktiga begrepp. Följande exempel formulerat med hjälp av den [REST-API för Search-dokument](https://docs.microsoft.com/rest/api/searchservice/search-documents), informerar både begäranden och svar. I Azure Search är Frågekörningen alltid mot ett index, autentiseras med en api-nyckel som tillhandahölls i begäran. 

```
{  
    "queryType": "simple" 
    "search": "seattle townhouse* +\"lake\"", 
    "searchFields": "description, city",  
    "count": "true", 
    "select": "listingId, street, status, daysOnMarket, description",
    "top": "10",
    "orderby": "listingId"
 } 
```
Det här exemplet visar flera viktiga delar av frågedefinitionen från parsern indata och att forma resultatet som en representativ fråga. Frågekörningen är alltid mot ett index, autentiseras med en api-nyckel som tillhandahölls i begäran. 

För att köra den här frågan använder [Sök explorer och fastigheter demo indexet](search-get-started-portal.md). Du kan klistra in den här frågesträngen i sökfältet i Utforskaren: `search=seattle townhouse +lake&searchFields=description, city&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&orderby=listingId`

**Söker i indexet**

+ Frågeparsern är ett alternativ kan anges via `queryType`. De flesta utvecklare använda standard [enkel parser](search-query-simple-examples.md) för fulltextsökning, men [fullständig Lucene](search-query-lucene-examples.md) parsning krävs för särskilda fråga formulär, till exempel fuzzy-sökning eller reguljära uttryck.
+ Matchningsvillkor i dokument i indexet har angetts via den `search` parametern. Sökningen kan vara Odefinierad, som i `search=*`, men mer sannolikt består av termer och fraser operatörer liknande vad som visas i exemplet.
+ Omfånget kan vara hela indexet eller specifika fält som visas i `searchFields`.

**Strukturera svaret**

Andra parametrar i det här exemplet ska även gälla för resultatet av frågan:

+ `count` Antal dokument som matchar frågan.
+ `select` begränsar de fält som returneras i svaret.
+ `top` begränsar rader eller dokument som returnerades i svaret. Standardvärdet är 50. exemplet minskar som till 10.
+ `orderby` sorterar resultaten efter ett fält.

**Aktivera åtgärder via indexattribut**

Design för indexet och fråga design är nära sammanlänkade i Azure Search. Medan visas inte här, en kritisk tidpunkt veta direkt är att den *indexschema*, bestämmer vilken typ av fråga som du kan skapa med attribut på varje fält. Indexattribut på en vilka tillåtna åtgärder – om ett fält är *sökbara* i indexet, *hämtningsbar* i resultatet, *sorterbar*, * filtrerbar*, och så vidare. I det här exemplet `"orderby": "listingId"` fungerar bara om listingId fältet är märkt som *sorterbar* i indexschemat. Läs mer om indexattribut [skapa Index REST API](https://docs.microsoft.com/rest/api/searchservice/create-index).

Tillåtna åtgärder på basis av per fält är bara ett sätt att indexdefinitionen informerar Frågekörningen. Andra funktioner som aktiverats i indexet är följande:

+ [Synonymer](https://docs.microsoft.com/rest/api/searchservice/synonym-map-operations)
+ [Text (lingvistiska)](https://docs.microsoft.com//rest/api/searchservice/language-support) och [anpassade analys](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)
+ [Förslagsställare konstruktioner](https://docs.microsoft.com/rest/api/searchservice/suggesters) som gör att Komplettera automatiskt och automatiska förslag
+ [Poängprofiler](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) som lägger till logik i rangordning sökresultat

Ovanstående funktionerna utövas vid körning av fråga, men är vanligtvis implementerade i koden som attribut i fältet i stället för parametrar i frågan.

<a name="types-of-queries"></a>

## <a name="types-of-queries-search-and-filter"></a>Typer av frågor: söka och filtrera

I det här grundläggande exemplet identifierades parametern search som sätt av vilka search villkor skickas till motorn. Det finns två typer av frågan i praktiken: `search` och `filter`. 

+ `search` frågor att söka efter en eller flera termer i alla *sökbara* fält i ditt index och fungerar på samma sätt som du förväntar dig en sökmotor som Google eller Bing fungerar. I exemplen används introduktion den `search` parametern.

+ `filter` frågor utvärderar ett booleskt uttryck över alla *filtrerbara* fält i ett index. Till skillnad från `search`, ett `filter` -frågan matchar det exakta innehållet i ett fält, inklusive skiftlägeskänslighet på strängfält.

Du kan använda Sök och filtrera tillsammans eller separat. Ett filter för fristående, utan en frågesträng är användbart när filteruttrycket kan fullständigt kvalificera dokument av intresse. Utan en frågesträng finns det inga lexikal eller språklig analys, ingen bedömning och inga rangordning. Observera att strängen är tom.

```
POST /indexes/nycjobs/docs/search?api-version=2017-11-11  
    {  
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "count": "true"
    }
```

Används tillsammans tillämpas filtret först på hela indexet och sedan utförs sökningen på resultatet av filtret. Filter kan därför vara en användbar teknik för att förbättra frågeprestanda eftersom de begränsar uppsättningen dokument som sökfrågan behöver bearbeta.

Syntaxen för filteruttryck är en delmängd av [OData-filterspråket](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search). För sökfrågor, kan du använda antingen den [förenklade syntaxen](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) eller [Lucene-frågesyntax](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search) som beskrivs nedan.


## <a name="choose-a-syntax-simple-or-full"></a>Välj en syntax: enkla eller fullständig

Azure Search finns på Apache Lucene och ger dig ett val mellan två fråga Parser för hantering av vanliga och specialiserade frågor. Vanliga sökförfrågningar formuleras med [enkla frågesyntaxen](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search). Den här syntaxen stöder ett antal vanliga sökoperatorer som AND-, eller inte, frasen, suffix och prioritetsoperatorer.

Den [Lucene-frågesyntax](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_syntax), aktiverat när du lägger till `queryType=full` på begäran, visar det mycket antagen och uttrycksfulla frågespråket som utvecklats som en del av [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Med den här frågesyntaxen kan specialiserade frågor:

+ [Fältbegränsade frågor](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fields)
+ [Fuzzy-sökning](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fuzzy)
+ [närhetssökning](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_proximity)
+ [termförstärkning](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_termboost)
+ [sökning med reguljära uttryck](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_regex)
+ [sökning med jokertecken](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_wildcard)

Booleska operatorer är huvudsakligen densamma i båda syntax, med ytterligare format i fullständiga Lucene:

+ [Booleska operatorer i enkel syntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search#operators-in-simple-search)
+ [Booleska operatorer i fullständiga Lucene-syntax](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_boolean)

## <a name="required-and-optional-elements"></a>Obligatoriska och valfria element

Frågor är alltid riktat mot ett index. Du kan inte ansluta till index eller skapa anpassade eller tillfälliga datastrukturer som ett mål för frågan. 

När du skickar sökförfrågningar till Azure Search finns det ett antal parametrar som du kan ange tillsammans med de ord som du skriver i sökrutan i programmet. Med dessa frågeparametrar kan du få djupare kontroll över [textsökningsupplevelsen](search-lucene-query-architecture.md).

Obligatoriska elementen på en frågebegäran om innehåller följande komponenter:

+ Tjänsten slutpunkt och index dokument samling, uttryckt som en URL `https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`.
+ API-version (endast REST), uttryckt i form av `api-version`
+ fråge- eller admin api-nyckel, uttryckt som `api-key`
+ frågesträng uttryckt i form av `search`, vilket kan vara okänt om du vill utföra en tom sökning. Du kan också skicka bara ett filteruttryck som `$filter`.
+ `queryType`, enkel eller fullständig, vilket kan utelämnas om du vill använda standard enkel syntax.

Alla andra search-parametrar är valfria.

## <a name="manage-search-results"></a>Hantera sökresultat 

Frågeresultat strömmas som JSON-dokument i REST-API, men om du använder .NET API: er, serialisering är inbyggd i. Du kan forma resultat genom att ange parametrar på frågan, att välja specifika fält för resultatet

Parametrar för frågan kan användas för att strukturera resultatmängd på följande sätt:

+ Att begränsa eller batchbearbetning antalet dokument i resultatet (50 som standard)
+ Välja fält som ska inkluderas i resultaten
+ Ange någon sorteringsordning
+ Att lägga till träffar markeringar för att uppmärksamma matchar villkoren i brödtexten i sökresultaten

### <a name="tips-for-unexpected-results"></a>Tips för oväntade resultat

Ibland kan är ämnet och inte strukturen för resultat oväntat. När frågan resultat är inte vad du förväntar dig att se, kan du prova följande fråga ändringar för att se om resultaten förbättra:

+ Ändra `searchMode=any` (standard) till `searchMode=all` att kräva matchningar på alla villkor i stället för något av villkoren. Detta gäller särskilt när booleska operatorer ingår frågan.

+ Ändra fråga tekniken om text eller lexikal analys krävs, men frågetypen utesluter språkliga bearbetning. I fulltextsökning korrigerar text eller lexikal analys automatiskt för stavfel, rapportanvändare plural ordformer, och även oregelbunden verb eller substantiv. För vissa frågor som fuzzy eller sökning med jokertecken, textanalys är inte en del av frågan parsning pipeline. Reguljära uttryck har använts som en lösning för vissa scenarier. 

### <a name="paging-results"></a>Växla resultat
I Azure Search kan du enkelt implementera sidindelning av sökresultaten. Genom att använda `top`- och `skip`-parametrarna kan du enkelt skicka sökförfrågningar som returnerar hela uppsättningen med sökresultat i hanterbara, ordnade delmängder som lägger grunden till bra sökgränssnittsmetoder. När du tar emot dessa mindre delmängder med resultat kan du också se antalet dokument i den fullständiga uppsättningen sökresultat.

Mer information om hur du använder sidindelning för sökresultat finns i artikeln [Sidindela sökresultat i Azure Search](search-pagination-page-layout.md).

### <a name="ordering-results"></a>Ordna resultaten
När du får resultat från en sökfråga kan du begära att Azure Search visar resultaten ordnade efter värden i ett visst fält. Som standard ordnar Azure Search sökresultaten baserat på rangordningen för varje dokuments sökpoäng, som härleds från [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Om du vill att Azure Search ska returnera resultaten ordnade efter ett annat värde än sökpoängen kan du använda `orderby`-sökparametern. Du kan ange värdet för `orderby`-parametern så att den tar med fältnamn och anrop till [`geo.distance()`-funktionen](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search) för geospatiala värden. Varje uttryck kan åtföljas av `asc` om resultaten ska visas i stigande ordning eller `desc` om de ska visas i fallande ordning. Standardinställningen är stigande ordning.


### <a name="hit-highlighting"></a>Träffmarkering
I Azure Search kan du enkelt framhäva exakt den del av sökresultatet som matchar sökfrågan med hjälp av parametrarna `highlight`, `highlightPreTag` och `highlightPostTag`. Du kan ange vilka *sökbara* fält vars matchade text ska framhävas samt ange de exakta strängtaggarna som ska läggas till i början och slutet av den matchade texten som Azure Search returnerar.

## <a name="apis-and-tools-for-testing"></a>API: er och verktyg för testning

I följande tabell visas de API: er och verktyg-baserade metoderna för att skicka frågor.

| Metod | Beskrivning |
|-------------|-------------|
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Klient som kan användas för att fråga en Azure Search-index.  <br/>[Läs mer.](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Söka efter dokument (REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | GET eller POST-metoder i ett index med hjälp av frågeparametrar för ytterligare indata.  |
| [Fiddler eller Postman andra HTTP-testverktyg](search-fiddler.md) | Beskriver hur du ställer in en huvudet i begäran och brödtext för att skicka frågor till Azure Search.  |
| [Sökutforskaren i Azure-portalen](search-explorer.md) | Innehåller ett sökfält och alternativ för val av index och api-versionen. Resultaten returneras som JSON-dokument. <br/>[Läs mer.](search-get-started-portal.md#query-index) | 

## <a name="see-also"></a>Se också

+ [Hur Fullständig textsökning fungerar i Azure Search (fråga parsning arkitektur](search-lucene-query-architecture.md)
+ [Sökutforskaren](search-explorer.md)
+ [Söka i .NET](search-query-dotnet.md)
+ [Hur du frågar efter resten](search-query-rest-api.md)
