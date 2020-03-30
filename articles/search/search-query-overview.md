---
title: Frågetyper och sammansättning
titleSuffix: Azure Cognitive Search
description: Grunderna för att skapa en sökfråga i Azure Cognitive Search, med hjälp av parametrar för att filtrera, välja och sortera resultat.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 902f3628235cc8a4524ddc4dd8a5327592fe47e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282827"
---
# <a name="query-types-and-composition-in-azure-cognitive-search"></a>Frågetyper och sammansättning i Azure Cognitive Search

I Azure Cognitive Search är en fråga en fullständig specifikation av en rundtursåtgärd. Parametrar på begäran ger matchningskriterier för att hitta dokument i ett index, vilka fält som ska inkluderas eller uteslutas, körningsinstruktioner som skickas till motorn och direktiv för att forma svaret. Ospecificerad (`search=*`), körs en fråga mot alla sökbara fält som en fulltextsökning, vilket returnerar en omålad resultatuppsättning i godtycklig ordning.

Följande exempel är en representativ fråga som är konstruerad i [REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents). Det här exemplet är inriktat på [hotell demoindex](search-get-started-portal.md) och innehåller vanliga parametrar.

```
{
    "queryType": "simple" 
    "search": "+New York +restaurant",
    "searchFields": "Description, Address/City, Tags",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

+ **`queryType`** anger tolken, som antingen är [standardjusteraren för enkel frågetolkare](search-query-simple-examples.md) (optimal för fulltextsökning) eller den [fullständiga Lucene-frågetolken](search-query-lucene-examples.md) som används för avancerade frågekonstruktioner som reguljära uttryck, närhetssökning, fuzzy- och jokerteckensökning, för att nämna några.

+ **`search`** ger matchningskriterierna, vanligtvis text men ofta tillsammans med booleska operatorer. Enkla fristående termer är *termfrågor.* Offertslutna frågor i flera delar är *nyckelfrasfrågor.* Sökningen kan vara odefinierad, som i **`search=*`**, men mer troligt består av termer, fraser och operatorer som liknar det som visas i exemplet.

+ **`searchFields`** begränsar frågekörningen till specifika fält. Alla fält som tillskrivs som *sökbara* i indexschemat är en kandidat för den här parametern.

Svaren formas också av de parametrar som du inkluderar i frågan. I exemplet består resultatuppsättningen av fält **`select`** som anges i utdraget. Endast fält som är markerade som *hämtningsbara* kan användas i en $select-sats. Dessutom returneras **`top`** bara de 10 träffarna i **`count`** den här frågan, samtidigt som du talar om för dig hur många dokument som matchar totalt, vilket kan vara mer än vad som returneras. I den här frågan sorteras rader efter Betyg i fallande ordning.

I Azure Cognitive Search är frågekörning alltid emot ett index, autentiserat med hjälp av en api-nyckel som finns i begäran. I REST anges båda i begäranden.

### <a name="how-to-run-this-query"></a>Så här kör du den här frågan

Om du vill köra den här frågan använder du [Sökutforskaren och hotelldemoindexet](search-get-started-portal.md). 

Du kan klistra in frågesträngen i utforskarens sökfält:`search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

## <a name="how-query-operations-are-enabled-by-the-index"></a>Så här aktiveras frågeåtgärder av indexet

Indexdesign och frågedesign är tätt kopplade i Azure Cognitive Search. Ett viktigt faktum att veta på framsidan är att *indexschemat*, med attribut på varje fält, bestämmer vilken typ av fråga du kan skapa. 

Indexattribut på ett fält anger de tillåtna operationerna - om ett fält är *sökbart* i indexet, *kan hämtas* i resultat, *sorterbara,* *filterbara*och så vidare. I exempelfrågesträngen fungerar `"$orderby": "Rating"` bara eftersom fältet Klassificering är markerat som *sorterbart* i indexschemat. 

![Indexdefinition för hotellprovet](./media/search-query-overview/hotel-sample-index-definition.png "Indexdefinition för hotellprovet")

Skärmbilden ovan är en ofullständig lista över indexattribut för hotellexemplet. Du kan visa hela indexschemat i portalen. Mer information om indexattribut finns i [Skapa REST-API för index](https://docs.microsoft.com/rest/api/searchservice/create-index).

> [!Note]
> Vissa frågefunktioner är aktiverade indexomfattande i stället för per fält. Dessa funktioner inkluderar: [synonymkartor](search-synonyms.md), [anpassade analysatorer](index-add-custom-analyzers.md), [förslagsförkonstruerar konstruerar (för automatisk komplettering och föreslagna frågor)](index-add-suggesters.md), [bedömningslogik för rangordningsresultat](index-add-scoring-profiles.md).

## <a name="elements-of-a-query-request"></a>Element i en frågebegäran

Frågor är alltid riktade till ett enda index. Du kan inte koppla index eller skapa anpassade eller tillfälliga datastrukturer som ett frågemål. 

Obligatoriska element på en frågebegäran innehåller följande komponenter:

+ Insamling av tjänstslutpunkt och indexdokument, uttryckt som en URL som innehåller fasta och användardefinierade komponenter:**`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`**(ENDAST REST) är nödvändig eftersom mer än en version av API:et alltid är tillgänglig. 
+ **`api-key`** autentiserar, antingen en fråga eller administratörs-api-nyckel, begäran till din tjänst.
+ **`queryType`**, antingen enkel eller fullständig, som kan utelämnas om du använder den inbyggda standardsyntaxen för enkel standard.
+ **`search`** eller **`filter`** ger matchningsvillkor, som kan vara ospecificerade om du vill utföra en tom sökning. Båda frågetyperna diskuteras i termer av den enkla tolken, men även avancerade frågor kräver sökparametern för att skicka komplexa frågeuttryck.

Alla andra sökparametrar är valfria. Den fullständiga listan över attribut finns i [Skapa index (REST)](https://docs.microsoft.com/rest/api/searchservice/create-index). Mer information om hur parametrar används under bearbetningen finns i [Hur fulltextsökning fungerar i Azure Cognitive Search](search-lucene-query-architecture.md).

## <a name="choose-apis-and-tools"></a>Välj API:er och verktyg

I följande tabell visas API:er och verktygsbaserade metoder för att skicka frågor.

| Metodik | Beskrivning |
|-------------|-------------|
| [Sökutforskaren (portal)](search-explorer.md) | Innehåller ett sökfält och alternativ för val av index- och api-version. Resultaten returneras som JSON-dokument. Rekommenderas för utforskning, testning och validering. <br/>[Läs mer.](search-get-started-portal.md#query-index) | 
| [Brevbärare eller andra REST-verktyg](search-get-started-postman.md) | Verktyg för webbtestning är ett utmärkt val för att formulera REST-samtal. REST API stöder alla möjliga åtgärder i Azure Cognitive Search. I den här artikeln kan du läsa om hur du konfigurerar ett HTTP-begärandehuvud och brödtext för att skicka begäranden till Azure Cognitive Search.  |
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Klient som kan användas för att fråga ett Azure Cognitive Search-index.  <br/>[Läs mer.](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Sökdokument (REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | GET- eller POST-metoder på ett index med hjälp av frågeparametrar för ytterligare indata.  |

## <a name="choose-a-parser-simple--full"></a>Välj en tolk: enkel | Full

Azure Cognitive Search sitter ovanpå Apache Lucene och ger dig ett val mellan två frågetolkare för hantering av typiska och specialiserade frågor. Begäranden som använder den enkla tolken formuleras med hjälp av den [enkla frågesyntaxen](query-simple-syntax.md), markerad som standard för dess hastighet och effektivitet i frihandstextfrågor. Den här syntaxen stöder ett antal vanliga sökoperatorer, inklusive operatorerna OCH, ELLER, NOT, fras, suffix och prioritet.

Den [fullständiga Lucene-frågesyntaxen](query-Lucene-syntax.md#bkmk_syntax) `queryType=full` , aktiverad när du lägger till i begäran, exponerar det allmänt antagna och uttrycksfulla frågespråk som utvecklats som en del av [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Fullständig syntax utökar den enkla syntaxen. Alla frågor som du skriver för den enkla syntaxen körs under den fullständiga Lucene-tolken. 

Följande exempel illustrerar punkten: samma fråga, men med olika queryType-inställningar ger olika resultat. I den första `^3` frågan `historic` behandlas efterbehandlas som en del av söktermen. Det högst rankade resultatet för denna fråga är "Marquis Plaza & Suites", som har *havet* i sin beskrivning

```
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

Samma fråga med den fullständiga Lucene-tolktolken `^3` som en in-field term booster. Byta parsers ändrar rangordningen, med resultat som innehåller termen *historiska* flytta till toppen.

```
queryType=full&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Typer av frågor

Azure Cognitive Search stöder ett brett spektrum av frågetyper. 

| Frågetyp | Användning | Exempel och mer information |
|------------|--------|-------------------------------|
| Gratis formulär text sökning | Sökparameter och antingen tolk| Fulltextsökning söker efter ett eller flera termer i alla *sökbara* fält i indexet och fungerar som du förväntar dig att en sökmotor som Google eller Bing fungerar. Exemplet i inledningen är fulltextsökning.<br/><br/>Fulltextsökning genomgår textanalys med hjälp av standard Lucene-analysatorn (som standard) för att gemenera alla termer, ta bort stoppord som "the". Du kan åsidosätta standardinställningen med [icke-engelska analysatorer](index-add-language-analyzers.md#language-analyzer-list) eller [specialiserade språkagnostiska analysatorer](index-add-custom-analyzers.md#AnalyzerTable) som ändrar textanalys. Ett exempel är [nyckelord](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) som behandlar hela innehållet i ett fält som en enda token. Detta är användbart för data som postnummer, ID och vissa produktnamn. | 
| Filtrerad sökning | [OData-filteruttryck](query-odata-filter-orderby-syntax.md) och antingen tolk | Filterfrågor utvärderar ett booleskt uttryck över alla *filterbara* fält i ett index. Till skillnad från sökning matchar en filterfråga det exakta innehållet i ett fält, inklusive skiftlägeskänslighet i strängfält. En annan skillnad är att filterfrågor uttrycks i OData-syntax. <br/>[Exempel på filteruttryck](search-query-simple-examples.md#example-3-filter-queries) |
| Geo-sökning | [Edm.GeographyPoint-typ](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) på fältet, filteruttryck och antingen tolk | Koordinater som lagras i ett fält med en Edm.GeographyPoint används för "sök nära mig" eller kartbaserade sökkontroller. <br/>[Exempel på geosökning](search-query-simple-examples.md#example-5-geo-search)|
| Sökning efter område | filteruttryck och enkel tolk | I Azure Cognitive Search skapas intervallfrågor med filterparametern. <br/>[Exempel på områdesfilter](search-query-simple-examples.md#example-4-range-filters) | 
| [Sökning i fält](query-lucene-syntax.md#bkmk_fields) | Sökparameter och fullständig tolk | Skapa ett sammansatt frågeuttryck som riktar sig till ett enda fält. <br/>[Exempel på fältsökning](search-query-lucene-examples.md#example-2-fielded-search) |
| [fuzzy sökning](query-lucene-syntax.md#bkmk_fuzzy) | Sökparameter och fullständig tolk | Matchar på termer som har en liknande konstruktion eller stavning. <br/>[Exempel på fuzzy-sökning](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [närhet sökning](query-lucene-syntax.md#bkmk_proximity) | Sökparameter och fullständig tolk | Söker efter termer som är nära varandra i ett dokument. <br/>[Exempel på närhetssökning](search-query-lucene-examples.md#example-4-proximity-search) |
| [term öka](query-lucene-syntax.md#bkmk_termboost) | Sökparameter och fullständig tolk | Rangordnar ett dokument högre om det innehåller den marknadsförda termen, i förhållande till andra som inte gör det. <br/>[Exempel på termhöjande](search-query-lucene-examples.md#example-5-term-boosting) |
| [sökning i reguljära uttryck](query-lucene-syntax.md#bkmk_regex) | Sökparameter och fullständig tolk | Matchningar baserat på innehållet i ett reguljärt uttryck. <br/>[Exempel på reguljärt uttryck](search-query-lucene-examples.md#example-6-regex) |
|  [jokertecken eller prefixsökning](query-lucene-syntax.md#bkmk_wildcard) | Sökparameter och fullständig tolk | Matchningar baserade på ett prefix och tilde (`~`) eller ett enda tecken (`?`). <br/>[Exempel på jokerteckensökning](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="manage-search-results"></a>Hantera sökresultat 

Frågeresultat strömmas som JSON-dokument i REST API, men om du använder .NET API: er, serialisering är inbyggd. Du kan forma resultat genom att ange parametrar i frågan och välja specifika fält för svaret.

Parametrar i frågan kan användas för att strukturera resultatuppsättningen på följande sätt:

+ Begränsa eller batchas antalet dokument i resultaten (50 som standard)
+ Välja fält som ska inkluderas i resultaten
+ Ställa in en sorteringsordning
+ Lägga till träffhöjdpunkter för att uppmärksamma matchande termer i sökresultatens brödtext

### <a name="tips-for-unexpected-results"></a>Tips för oväntade resultat

Ibland är ämnet och inte resultatens struktur oväntad. När frågeresultat inte är vad du förväntar dig att se kan du prova dessa frågeändringar för att se om resultaten förbättras:

+ Ändra **`searchMode=any`** (standard) **`searchMode=all`** om du vill kräva matchningar på alla villkor i stället för något av villkoren. Detta gäller särskilt när booleska operatorer ingår i frågan.

+ Ändra frågetekniken om text eller lexikal analys är nödvändig, men frågetypen utesluter språklig bearbetning. I fulltextsökning, text eller lexikal analys autokorrigeringar för stavfel, singular-plural ordformer, och även oregelbundna verb eller substantiv. För vissa frågor, till exempel fuzzy- eller jokerteckensökning, är textanalys inte en del av frågetolkarpipel. I vissa scenarier har reguljära uttryck använts som en lösning. 

### <a name="paging-results"></a>Växla resultat
Azure Cognitive Search gör det enkelt att implementera växling av sökresultat. Genom att **`top`** **`skip`** använda parametrarna och kan du smidigt utfärda sökbegäranden som gör att du kan få den totala uppsättningen sökresultat i hanterbara, ordnade delmängder som enkelt möjliggör bra sökgränssnittspraxis. När du tar emot dessa mindre delmängder med resultat kan du också se antalet dokument i den fullständiga uppsättningen sökresultat.

Du kan läsa mer om växlingsresultat i artikeln [Så här sidsökresultat i Azure Cognitive Search](search-pagination-page-layout.md).

### <a name="ordering-results"></a>Ordna resultaten
När du tar emot resultat för en sökfråga kan du begära att Azure Cognitive Search betjänar de resultat som sorterats efter värden i ett visst fält. Som standard beställer Azure Cognitive Search sökresultaten baserat på rangordningen för varje dokuments sökresultat, som härleds från [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Om du vill att Azure Cognitive Search ska returnera dina resultat som **`orderby`** beställts efter ett annat värde än sökresultatet kan du använda sökparametern. Du kan ange värdet **`orderby`** för parametern för att inkludera fältnamn och anrop till [** `geo.distance()` funktionen**](query-odata-filter-orderby-syntax.md) för geospatiala värden. Varje uttryck kan `asc` följas av att ange att resultat **`desc`** begärs i stigande ordning och för att ange att resultat begärs i fallande ordning. Standardinställningen är stigande ordning.


### <a name="hit-highlighting"></a>Träffmarkering
I Azure Cognitive Search görs den exakta delen av sökresultaten som matchar **`highlight`** **`highlightPreTag`** sökfrågan **`highlightPostTag`** enkelt genom att använda , och parametrar. Du kan ange vilka *sökbara* fält som ska få sin matchade text markerad samt ange de exakta strängtaggarna som ska läggas till i början och slutet av den matchade texten som Azure Cognitive Search returnerar.

## <a name="see-also"></a>Se även

+ [Så här fungerar fulltextsökning i Azure Cognitive Search (frågetolningsarkitektur)](search-lucene-query-architecture.md)
+ [Sök explorer](search-explorer.md)
+ [Fråga i .NET](search-query-dotnet.md)
+ [Så här frågar du i REST](search-create-index-rest-api.md)
