---
title: Fråga grunderna i Azure Search | Microsoft Docs
description: Grunderna för att skapa en sökfråga i Azure Search, med parametrar för att filtrera, markera och sortera resultaten.
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/27/2018
ms.openlocfilehash: 4650ad89850f32ae5e83a7ac1cd5eac096b180ed
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366454"
---
# <a name="query-fundamentals-in-azure-search"></a>Fråga grunderna i Azure Search

En frågedefinition i Azure Search är en fullständig specifikation av en begäran som innehåller följande delar: tjänsten URL-slutpunkt, målindex, api-nyckel som används för att autentisera begäran, api-versionen och en frågesträng. 

Fråga sträng sammansättning består av de parametrar som definierats i [API för webbsökning dokument](https://docs.microsoft.com/rest/api/searchservice/search-documents). Den viktigaste är den **search =** parametern, som kan vara odefinierad (`search=*`) men mer sannolikt består av termer och fraser operatörer som liknar följande exempel:

```
"search": "seattle townhouse +\"lake\""
```

Andra parametrar används för att dirigera frågebearbetning eller förbättra svaret. Exempel på hur du använder parametrarna är omfång sökningen till specifika fält, ställa in ett Sökläge för att anpassa precisionsåterkallningsdiagram till bias och lägga till ett antal så att du kan hålla reda på resultat. 

```
{  
    "search": "seattle townhouse* +\"lake\"",  
    "searchFields": "description, city",  
    "searchMode": "all",
    "count": "true", 
    "queryType": "simple" 
 } 
```

Även om frågedefinitionen är grundläggande, är indexschemat lika viktigt i hur den anger tillåtna åtgärder på basis av fält i taget. Under utvecklingen av index fastställa attribut på fält tillåtna åtgärder. Till exempel en förutsättning för fulltextsökning och inkluderas i sökresultat, ett fält måste markeras som både *sökbara* och *hämtningsbar*.

När en fråga körs är körningen alltid mot ett index, autentiseras med en api-nyckel som tillhandahölls i begäran. Du kan inte ansluta till index eller skapa anpassade eller tillfälliga datastrukturer som ett mål för frågan.  

Frågeresultat strömmas som JSON-dokument i REST-API, men om du använder .NET API: er, serialisering är inbyggd i. Du kan forma resultat genom att ange parametrar på frågan, att välja specifika fält för resultatet

Sammanfattningsvis större delen av frågebegäran anger omfång och åtgärder: vilka fält som ska ingå i sökning, vilket fält som ska inkluderas i resultatet anger att sortera eller filtrera, och så vidare. Angivet används körs en fråga mot alla sökbara fält som en fullständig text search-åtgärd returnerar en inte har poängsatts resultatuppsättningen i valfri ordning.

<a name="types-of-queries"></a>

## <a name="types-of-queries-search-and-filter"></a>Typer av frågor: söka och filtrera

I Azure Search har du många alternativ för att skapa extremt kraftfulla frågor. De två viktigaste typerna av frågor som du använder är `search` och `filter`. 

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

Syntaxen för filteruttryck är en delmängd av [OData-filterspråket](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search). För sökfrågor kan du antingen använda [den förenklade syntaxen](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) eller [Lucene-frågesyntaxen](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search) som beskrivs nedan.


## <a name="choose-a-syntax-simple-or-full"></a>Välj en syntax: enkla eller fullständig

Azure Search finns på Apache Lucene och ger dig ett val mellan två fråga Parser för hantering av vanliga och specialiserade frågor. Vanliga sökförfrågningar formuleras med [enkla frågesyntaxen](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search). Den här syntaxen stöder ett antal vanliga sökoperatorer som AND-, eller inte, frasen, suffix och prioritetsoperatorer.

Den [Lucene-frågesyntax](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_syntax), aktiverat när du lägger till **queryType = full** på begäran, visar det mycket populära och uttrycksfulla frågespråket som utvecklats som en del av [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Med den här frågesyntaxen kan specialiserade frågor:

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

När du skickar sökförfrågningar till Azure Search finns det ett antal parametrar som du kan ange tillsammans med de ord som du skriver i sökrutan i programmet. Med dessa frågeparametrar kan du få djupare kontroll över [textsökningsupplevelsen](search-lucene-query-architecture.md).

Obligatoriska elementen på en frågebegäran om innehåller följande komponenter:

+ Tjänsten slutpunkt och index dokument samling, uttryckt som en URL `https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`.
+ API-version (endast REST), uttryckt i form av **api-version =**
+ fråge- eller admin api-nyckel, uttryckt som **api-nyckel =**
+ frågesträng uttryckt i form av **search =**, vilket kan vara okänt om du vill utföra en tom sökning. Du kan också skicka bara ett filteruttryck som **$filter =**.
+ **queryType =**, enkel eller fullständig, vilket kan utelämnas om du vill använda standard enkel syntax.

Alla andra search-parametrar är valfria.

## <a name="apis-and-tools-for-testing"></a>API: er och verktyg för testning

I följande tabell visas de API: er och verktyg-baserade metoderna för att skicka frågor.

| Metod | Beskrivning |
|-------------|-------------|
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Klient som kan användas för att fråga en Azure Search-index.  <br/>[Läs mer.](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Söka efter dokument (REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | GET eller POST-metoder i ett index med hjälp av frågeparametrar för ytterligare indata.  |
| [Fiddler eller Postman andra HTTP-testverktyg](search-fiddler.md) | Beskriver hur du ställer in en huvudet i begäran och brödtext för att skicka frågor till Azure Search.  |
| [Sökutforskaren i Azure-portalen](search-explorer.md) | Innehåller ett sökfält och alternativ för val av index och api-versionen. Resultaten returneras som JSON-dokument. <br/>[Läs mer.](search-get-started-portal.md#query-index) | 

## <a name="manage-search-results"></a>Hantera sökresultat

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

## <a name="see-also"></a>Se också

+ [Hur Fullständig textsökning fungerar i Azure Search (fråga parsning arkitektur](search-lucene-query-architecture.md)
+ [Sökutforskaren](search-explorer.md)
+ [Söka i .NET](search-query-dotnet.md)
+ [Hur du frågar efter resten](search-query-rest-api.md)
