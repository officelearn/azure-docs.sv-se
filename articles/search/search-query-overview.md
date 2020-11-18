---
title: Frågetyper och sammansättning
titleSuffix: Azure Cognitive Search
description: Grunderna för att skapa en Sök fråga i Azure Kognitiv sökning med parametrar för att filtrera, välja och sortera resultat.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 362f46290bbe2008f9fb862a8711577050050192
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94693261"
---
# <a name="query-types-and-composition-in-azure-cognitive-search"></a>Frågetyper och sammansättning i Azure Kognitiv sökning

I Azure Kognitiv sökning är en fråga en fullständig specifikation av en tur och retur-åtgärd. På begäran finns det parametrar som innehåller körnings instruktioner för motorn, samt parametrar som formar svaret som kommer tillbaka. Ospecificerad ( `search=*` ), utan matchnings villkor och använder null eller standard parametrar, en fråga körs mot alla sökbara fält som en fullständig texts ökning och returnerar en resultat uppsättning i valfri ordning.

Följande exempel är en representativ fråga som konstruerats i [REST API](/rest/api/searchservice/search-documents). Det här exemplet riktar sig till [hotell demonstrations indexet](search-get-started-portal.md) och inkluderar vanliga parametrar så att du kan få en uppfattning om hur en fråga ser ut.

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

+ **`queryType`** ställer in parsern, som är antingen [standardvärdet för enkel fråga](search-query-simple-examples.md) (optimal för full texts ökning) eller den [fullständiga Lucene-frågeuttrycket](search-query-lucene-examples.md) som används för avancerade fråge konstruktioner som reguljära uttryck, närhets sökning, fuzzy och jokertecken, för att ge några.

+ **`search`** innehåller matchnings villkor, vanligt vis hela termer eller fraser, men ofta åtföljs av booleska operatorer. Enstaka fristående villkor är *term* frågor. Quote-inneslutna frågor med flera delar är *fras* frågor. Sökningen kan vara odefinierad, som i **`search=*`** , men utan villkor att matcha på består resultat uppsättningen av godtyckligt valda dokument.

+ **`searchFields`** begränsar frågans körning till vissa fält. Alla fält som har attribut som *sökbara* i index schemat är en kandidat för den här parametern.

Svar är också formade av de parametrar som du inkluderar i frågan:

+ **`select`** anger vilka fält som ska returneras i svaret. Endast fält som har marker ATS som *hämtnings* bara i indexet kan användas i en SELECT-instruktion.

+ **`top`** Returnerar det angivna antalet dokument med optimal matchning. I det här exemplet returneras bara 10 träffar. Du kan använda Top och SKIP (visas inte) för att visa resultatet.

+ **`count`** visar hur många dokument i hela indexet som matchar övergripande, vilket kan vara mer än vad som returneras. 

+ **`orderby`** används om du vill sortera resultaten efter ett värde, till exempel en klassificering eller plats. Annars är standardvärdet att använda relevans poängen för att rangordna resultat.

I Azure Kognitiv sökning sker frågekörningen alltid mot ett index, autentiseras med hjälp av en API-nyckel som anges i begäran. I REST finns båda i begärandehuvuden.

### <a name="how-to-run-this-query"></a>Så här kör du den här frågan

Innan du skriver någon kod kan du använda specialverktyg för att lära dig syntaxen och experimentera med olika parametrar. Den snabbaste metoden är det inbyggda Portal verktyget, [Sök Utforskaren](search-explorer.md).

Om du har följt den här [snabb starten för att skapa hotell demonstrations indexet](search-get-started-portal.md)kan du klistra in frågesträngen i Explorers sökfält för att köra din första fråga: `search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

## <a name="how-query-operations-are-enabled-by-the-index"></a>Hur Query-åtgärder aktive ras av indexet

Index design och fråge design är nära kopplade i Azure Kognitiv sökning. Ett viktigt faktum att känna sig fram är att *index schemat*, med attribut på varje fält, bestämmer vilken typ av fråga som du kan skapa. 

Indexattribut i ett fält ange tillåtna åtgärder – om ett fält är *sökbart* i indexet, kan *hämtas* i resultat, *sorterbart*, *filtrerat* och så vidare. I exempel frågans sträng `"$orderby": "Rating"` fungerar endast eftersom fältet klassificering är markerat som *sorterbart* i index schemat. 

![Index definition för hotellet-exemplet](./media/search-query-overview/hotel-sample-index-definition.png "Index definition för hotellet-exemplet")

Skärm bilden ovan är en ofullständig lista över indexattribut för Hotels-exemplet. Du kan visa hela index schemat i portalen. Mer information om indexattribut finns i [skapa index REST API](/rest/api/searchservice/create-index).

> [!Note]
> Vissa fråge funktioner har Aktiver ATS för index i stället för per fält. Dessa funktioner omfattar: [synonym Maps](search-synonyms.md), [anpassade analys](index-add-custom-analyzers.md)verktyg, [förslags konstruktioner (för Autoavsluta och föreslagna frågor)](index-add-suggesters.md), [bedömnings logik för ranknings resultat](index-add-scoring-profiles.md).

## <a name="elements-of-a-query-request"></a>Element i en fråge förfrågan

Frågor dirigeras alltid till ett enda index. Du kan inte ansluta index eller skapa anpassade eller tillfälliga data strukturer som mål för frågor. 

Nödvändiga element i en förfrågan innehåller följande komponenter:

+ Samling av tjänst slut punkt och index dokument, uttryckt som en URL som innehåller fasta och användardefinierade komponenter: **`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`** (Endast REST) är nödvändigt eftersom mer än en version av API: n är tillgänglig hela tiden. 
+ **`api-key`**, autentiseras begäran av en fråge-eller admin-API-nyckel.
+ **`queryType`**, antingen enkel eller fullständig, som kan utelämnas om du använder den inbyggda enkla standard syntaxen.
+ **`search`** eller **`filter`** ger matchnings villkor, som kan anges om du vill utföra en tom sökning. Båda typerna av frågor beskrivs i termer av den enkla parsern, men även avancerade frågor kräver Sök parametern för att skicka komplexa frågeuttryck.

Alla andra Sök parametrar är valfria. En fullständig lista över attribut finns i [skapa index (rest)](/rest/api/searchservice/create-index). En närmare titt på hur parametrar används under bearbetningen finns i [hur full texts ökning fungerar i Azure kognitiv sökning](search-lucene-query-architecture.md).

## <a name="choose-apis-and-tools"></a>Välj API: er och verktyg

I följande tabell visas de API: er och verktyg baserade metoder för att skicka frågor.

| Metodik | Beskrivning |
|-------------|-------------|
| [Sökutforskaren (portal)](search-explorer.md) | Innehåller ett sökfält och alternativ för val av index och API-version. Resultat returneras som JSON-dokument. Rekommenderas för undersökning, testning och validering. <br/>[Läs mer.](search-get-started-portal.md#query-index) | 
| [Postman eller andra REST-verktyg](search-get-started-rest.md) | Webb test verktyg är ett utmärkt alternativ för att utforma REST-anrop. REST API stöder varje möjlig åtgärd i Azure Kognitiv sökning. I den här artikeln får du lära dig hur du ställer in en HTTP-begärans huvud och brödtext för att skicka förfrågningar till Azure Kognitiv sökning.  |
| [SearchClient (.NET)](/dotnet/api/azure.search.documents.searchclient) | Klient som kan användas för att skicka frågor till ett Azure Kognitiv sökning-index.  <br/>[Läs mer.](search-howto-dotnet-sdk.md)  |
| [Sök dokument (REST API)](/rest/api/searchservice/search-documents) | Hämta eller publicera metoder för ett index med hjälp av frågeparametrar för ytterligare indata.  |

## <a name="choose-a-parser-simple--full"></a>Välj en parser: enkel | fullständig

Azure Kognitiv sökning finns ovanpå Apache Lucene och ger dig ett val mellan två fråge tolkare för hantering av typiska och specialiserade frågor. Begär Anden som använder den enkla parsern formuleras med hjälp av den [enkla frågesyntaxen](query-simple-syntax.md), valt som standard för dess hastighet och effektivitet i text frågor med fri form. Den här syntaxen stöder ett antal vanliga Sök operatorer som operatörerna AND, OR, NOT, fras, suffix och prioritet.

Den [fullständiga Lucene-frågesyntaxen](query-Lucene-syntax.md#bkmk_syntax), som aktive ras när du lägger till `queryType=full` begäran, exponerar det vanligaste och lättfattliga programspecifika-frågespråket som utvecklats som en del av [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Fullständig syntax utökar den enkla syntaxen. Alla frågor som du skriver för den enkla syntaxen körs under hela Lucene-parsern. 

I följande exempel visas punkten: samma fråga, men med olika inställningar för queryType ger det olika resultat. I den första frågan `^3` `historic` behandlas efter som en del av Sök termen. Det översta resultatet för den här frågan är "Marquis Plaza & Suitess", som har *10 i beskrivningen*

```
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

Samma fråga som använder den fullständiga Lucene-parsern tolkar `^3` som en term förstärkning i fält. När du växlar parser ändras rangordningen, med resultat som innehåller den *historiska* termen flytta högst upp.

```
queryType=full&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Typer av frågor

Azure Kognitiv sökning stöder ett brett utbud av frågetyper. 

| Frågetyp | Användning | Exempel och mer information |
|------------|--------|-------------------------------|
| Texts ökning med fritext | Sök parameter och antingen parser| Fullständig texts ökning söker efter en eller flera termer i alla *sökbara* fält i ditt index och fungerar på samma sätt som du förväntar dig att en sökmotor som Google eller Bing fungerar. Exemplet i introduktionen är full texts ökning.<br/><br/>Full texts ökning underbörjar lexikal analys med hjälp av standard Lucene Analyzer (som standard) för att sänka alla villkor, ta bort stoppord som "The". Du kan åsidosätta standardvärdet med [icke-engelska analyserare](index-add-language-analyzers.md#language-analyzer-list) eller [specialiserade oberoende-analyser](index-add-custom-analyzers.md#AnalyzerTable) som ändrar lexikalisk analys. Ett exempel är ett [nyckelord](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) som behandlar hela innehållet i ett fält som en enskild token. Detta är användbart för data som post nummer, ID: n och vissa produkt namn. | 
| Filtrerad sökning | [OData filter-uttryck](query-odata-filter-orderby-syntax.md) och antingen parser | Filter frågor utvärderar ett booleskt uttryck över alla *filter* bara fält i ett index. Till skillnad från sökningen matchar en filter fråga det exakta innehållet i ett fält, inklusive Skift läges känslighet i sträng fält. En annan skillnad är att filter frågor uttrycks i OData-syntax. <br/>[Exempel på filter uttryck](search-query-simple-examples.md#example-3-filter-queries) |
| Geo-sökning | [EDM. GeographyPoint-typ](/rest/api/searchservice/supported-data-types) i fältet, filter uttrycket och antingen parsern | Koordinater som lagras i ett fält med en EDM. GeographyPoint används för "hitta nära mig" eller mappbaserade Sök kontroller. <br/>[Exempel på Geo-sökning](search-query-simple-examples.md#example-5-geo-search)|
| Intervalls ökning | filter uttryck och enkel parser | I Azure Kognitiv sökning skapas intervall frågor med hjälp av filter parametern. <br/>[Exempel på intervall filter](search-query-simple-examples.md#example-4-range-filters) | 
| [Sökning efter fält](query-lucene-syntax.md#bkmk_fields) | Sök parameter och fullständig parser | Bygg ett sammansatt frågeuttryck riktade mot ett enskilt fält. <br/>[Exempel på fält som söks](search-query-lucene-examples.md#example-2-fielded-search) |
| [Fuzzy-sökning](query-lucene-syntax.md#bkmk_fuzzy) | Sök parameter och fullständig parser | Matchningar på villkor som har en liknande konstruktion eller stavning. <br/>[Exempel på fuzzy-sökning](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [närhets sökning](query-lucene-syntax.md#bkmk_proximity) | Sök parameter och fullständig parser | Söker efter termer som är nära varandra i ett dokument. <br/>[Exempel på närhet](search-query-lucene-examples.md#example-4-proximity-search) |
| [term förstärkning](query-lucene-syntax.md#bkmk_termboost) | Sök parameter och fullständig parser | Rangordna ett dokument högre om det innehåller den ökade termen, i förhållande till andra som inte är det. <br/>[Exempel på term förstärkning](search-query-lucene-examples.md#example-5-term-boosting) |
| [sökning efter reguljära uttryck](query-lucene-syntax.md#bkmk_regex) | Sök parameter och fullständig parser | Matchningar baserat på innehållet i ett reguljärt uttryck. <br/>[Exempel på reguljära uttryck](search-query-lucene-examples.md#example-6-regex) |
|  [sökning med jokertecken eller prefix](query-lucene-syntax.md#bkmk_wildcard) | Sök parameter och fullständig parser | Matchar baserat på ett prefix och tilde ( `~` ) eller ett enskilt tecken ( `?` ). <br/>[Sök exempel i jokertecken](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="manage-search-results"></a>Hantera Sök Resultat 

Frågeresultat strömmas som JSON-dokument i REST API, men om du använder .NET-API: er är serialiseringen inbyggd. Du kan forma resultatet genom att ange parametrar i frågan och välja vissa fält för svaret.

Parametrar i frågan kan användas för att strukturera resultat uppsättningen på följande sätt:

+ Begränsa eller gruppera antalet dokument i resultatet (50 som standard)
+ Att välja fält som ska inkluderas i resultaten
+ Ange sorterings ordning
+ Lägg till träff markeringar för att dra uppmärksamheten till matchande termer i Sök resultatets brödtext

### <a name="tips-for-unexpected-results"></a>Tips för oväntade resultat

Ibland är ämnet och inte resultat strukturen oväntade. När frågans resultat inte är det du förväntar dig, kan du prova de här frågans ändringar för att se om resultaten förbättras:

+ Ändra **`searchMode=any`** (standard) om **`searchMode=all`** du vill kräva matchningar för alla kriterier i stället för något av villkoren. Detta gäller särskilt när booleska operatorer ingår i frågan.

+ Ändra frågans teknik om text-eller lexikalisk analys är nödvändig, men typen av fråga utesluter språklig bearbetning. I full texts ökning korrigeras text eller lexikala analyser automatiskt för stavnings fel, singular-pluraling-formulär och även oregelbundna verb eller substantiv. För vissa frågor, till exempel fuzzy eller jokertecken, är lexikalisk analys inte en del av pipelinen för frågekörning. I vissa fall har reguljära uttryck använts som en lösning. 

### <a name="paging-results"></a>Växla resultat
Med Azure Kognitiv sökning är det enkelt att implementera sid indelning för Sök resultat. Genom att använda **`top`** **`skip`** -och-parametrarna kan du smidigt utfärda Sök begär Anden som gör att du kan ta emot den totala uppsättningen Sök resultat i hanterbara, ordnade del mängder som gör det enkelt att snabbt aktivera lämpliga Sök gränssnitts metoder. När du tar emot dessa mindre delmängder med resultat kan du också se antalet dokument i den fullständiga uppsättningen sökresultat.

Du kan lära dig mer om sid öknings resultat i artikeln [så här hittar du sid Sök resultat i Azure kognitiv sökning](search-pagination-page-layout.md).

### <a name="ordering-results"></a>Ordna resultaten
När du tar emot resultat för en Sök fråga kan du begära att Azure Kognitiv sökning hanterar resultaten sorterade efter värden i ett särskilt fält. Som standard beställer Azure Kognitiv sökning Sök resultaten baserat på rangordningen för varje dokuments Sök resultat, som härleds från [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Om du vill att Azure Kognitiv sökning returnerar dina resultat sorterade efter ett annat värde än Sök poängen kan du använda **`orderby`** Sök parametern. Du kan ange värdet för **`orderby`** parametern för att inkludera fält namn och anrop till [**`geo.distance()` funktionen**](query-odata-filter-orderby-syntax.md) för geospatiala värden. Varje uttryck kan följas av `asc` för att indikera att resultat begärs i stigande ordning och **`desc`** för att indikera att resultat begärs i fallande ordning. Standardinställningen är stigande ordning.


### <a name="hit-highlighting"></a>Träffmarkering
I Azure Kognitiv sökning är det enkelt att framhäva den EXAKTA delen av Sök resultaten som matchar Sök frågan med **`highlight`** **`highlightPreTag`** parametrarna, och **`highlightPostTag`** . Du kan ange vilka *sökbara* fält som ska framhävas och ange de exakta sträng taggarna som ska läggas till i början och slutet av den matchade text som Azure kognitiv sökning returnerar.

## <a name="see-also"></a>Se även

+ [Hur full texts ökning fungerar i Azure Kognitiv sökning (arkitektur för frågekörning)](search-lucene-query-architecture.md)
+ [Sök Utforskaren](search-explorer.md)
+ [Så här frågar du i .NET](./search-get-started-dotnet.md)
+ [Fråga i REST](./search-get-started-powershell.md)