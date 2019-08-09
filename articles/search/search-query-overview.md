---
title: Frågetyper och sammansättning – Azure Search
description: Grunderna för att skapa en Sök fråga i Azure Search med parametrar för att filtrera, välja och sortera resultat.
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/13/2019
ms.custom: seodec2018
ms.openlocfilehash: c2d40865857bc3ea8367199ed29e0220a0e7c557
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882597"
---
# <a name="how-to-compose-a-query-in-azure-search"></a>Så här skapar du en fråga i Azure Search

I Azure Search är en fråga en fullständig specifikation av en tur och retur-åtgärd. Parametrar på begäran ger matchnings villkor för att hitta dokument i ett index, körnings instruktioner för motorn och direktiv för att forma svaret. 

En fråge förfrågan är en omfattande konstruktion där du anger vilka fält som är omfång, hur du söker, vilka fält som ska returneras, om du vill sortera eller filtrera och så vidare. Ospecificerad, en fråga körs mot alla sökbara fält som en fullständig texts öknings åtgärd och returnerar en resultat uppsättning i valfri ordning.

## <a name="apis-and-tools-for-testing"></a>API: er och verktyg för testning

I följande tabell visas de API: er och verktyg baserade metoder för att skicka frågor.

| Metod | Beskrivning |
|-------------|-------------|
| [Sök Utforskaren (portal)](search-explorer.md) | Innehåller ett sökfält och alternativ för val av index och API-version. Resultat returneras som JSON-dokument. <br/>[Läs mer.](search-get-started-portal.md#query-index) | 
| [Postman eller Fiddler](search-get-started-postman.md) | Webb test verktyg är ett utmärkt alternativ för att utforma REST-anrop. REST API stöder varje möjlig åtgärd i Azure Search. I den här artikeln får du lära dig hur du ställer in en HTTP-begärans huvud och brödtext för att skicka begär anden till Azure Search.  |
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Klient som kan användas för att fråga ett Azure Search-index.  <br/>[Läs mer.](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Sök dokument (REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | Hämta eller publicera metoder för ett index med hjälp av frågeparametrar för ytterligare indata.  |

## <a name="a-first-look-at-query-requests"></a>En första titt på fråge förfrågningar

Exempel är användbara för att introducera nya begrepp. Som en representativ fråga som skapats i [REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents), riktar detta exempel sig på [fastighets demonstrations indexet](search-get-started-portal.md) och inkluderar gemensamma parametrar.

```
{
    "queryType": "simple" 
    "search": "seattle townhouse* +\"lake\"",
    "searchFields": "description, city",
    "count": "true",
    "select": "listingId, street, status, daysOnMarket, description",
    "top": "10",
    "orderby": "daysOnMarket"
}
```

+ **`queryType`** ställer in parsern, som i Azure Search kan vara den [enkla](search-query-simple-examples.md) standardparsern (optimal för full texts ökning) eller den [fullständiga Lucene-frågeuttrycket](search-query-lucene-examples.md) som används för avancerade fråge konstruktioner som reguljära uttryck, närhets sökning, fuzzy och jokertecken Sök, för att ge några.

+ **`search`** innehåller matchnings villkor, vanligt vis text men ofta tillsammans med booleska operatorer. Enstaka fristående villkor är *term* frågor. Quote – inneslutna frågor med flera delar är *viktiga fras* frågor. Sökningen kan vara odefinierad, som i **`search=*`** , men mer sannolikt består av termer, fraser och operatorer som liknar vad som visas i exemplet.

+ **`searchFields`** är valfritt, används för att begränsa frågekörningen till vissa fält.

Svar är också formade av de parametrar som du inkluderar i frågan. I exemplet består resultat uppsättningen av fält som anges i **`select`** instruktionen. Endast de 10 översta träffarna returneras i den här frågan, **`count`** men visar hur många dokument som matchar övergripande. I den här frågan sorteras rader efter daysOnMarket.

I Azure Search är frågekörningen alltid mot ett index, autentiserad med hjälp av en API-nyckel som anges i begäran. I REST finns båda i begärandehuvuden.

### <a name="how-to-run-this-query"></a>Så här kör du den här frågan

Använd [Sök Utforskaren och fastighets demo indexet](search-get-started-portal.md)för att köra den här frågan. 

Du kan klistra in den här frågesträngen i Explorer-Sök fältet:`search=seattle townhouse +lake&searchFields=description, city&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket`

## <a name="how-query-operations-are-enabled-by-the-index"></a>Hur Query-åtgärder aktive ras av indexet

Index design och fråge design är tätt kopplade till Azure Search. Ett viktigt faktum att känna sig fram är att *index schemat*, med attribut på varje fält, bestämmer vilken typ av fråga som du kan skapa. 

Indexattribut i ett fält ange tillåtna åtgärder – om ett fält är sökbart i indexet, kan *hämtas* i resultat, sorterbart, *filtrerat*och så vidare. I exempel frågans sträng `"$orderby": "daysOnMarket"` fungerar endast eftersom fältet daysOnMarket är markerat som *sorterbart* i index schemat. 

![Index definition för det riktiga fastighets exemplet](./media/search-query-overview/realestate-sample-index-definition.png "Index definition för det riktiga fastighets exemplet")

Skärm bilden ovan är en ofullständig lista över indexattribut för fastighets exemplet. Du kan visa hela index schemat i portalen. Mer information om indexattribut finns i [skapa index REST API](https://docs.microsoft.com/rest/api/searchservice/create-index).

> [!Note]
> Vissa fråge funktioner har Aktiver ATS för index i stället för per fält. Dessa funktioner omfattar: [synonym Maps](search-synonyms.md), [anpassade analys](index-add-custom-analyzers.md)verktyg, [förslags konstruktioner (för Autoavsluta och föreslagna frågor)](index-add-suggesters.md), [bedömnings logik för ranknings resultat](index-add-scoring-profiles.md).

## <a name="elements-of-a-query-request"></a>Element i en fråge förfrågan

Frågor dirigeras alltid till ett enda index. Du kan inte ansluta index eller skapa anpassade eller tillfälliga data strukturer som mål för frågor. 

Nödvändiga element i en förfrågan innehåller följande komponenter:

+ Samling av tjänst slut punkt och index dokument, uttryckt som en URL som innehåller fasta och användardefinierade komponenter: **`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`** (Endast REST) är nödvändigt eftersom mer än en version av API: n är tillgänglig hela tiden. 
+ **`api-key`** , autentiseras begäran av en fråge-eller admin-API-nyckel.
+ **`queryType`** , antingen enkel eller fullständig, som kan utelämnas om du använder den inbyggda enkla standard syntaxen.
+ **`search`** eller **`filter`** ger matchnings villkor, som kan anges om du vill utföra en tom sökning. Båda typerna av frågor beskrivs i termer av den enkla parsern, men även avancerade frågor kräver Sök parametern för att skicka komplexa frågeuttryck.

Alla andra Sök parametrar är valfria. En fullständig lista över attribut finns i [skapa index (rest)](https://docs.microsoft.com/rest/api/searchservice/create-index). En närmare titt på hur parametrar används under bearbetningen finns i [hur full texts ökning fungerar i Azure Search](search-lucene-query-architecture.md).

## <a name="choose-a-parser-simple--full"></a>Välj en parser: enkel | fullständig

Azure Search finns ovanpå Apache Lucene och ger dig möjlighet att välja mellan två fråge tolkare för att hantera typiska och specialiserade frågor. Begär Anden som använder den enkla parsern formuleras med hjälp av den [enkla frågesyntaxen](query-simple-syntax.md), valt som standard för dess hastighet och effektivitet i text frågor med fri form. Den här syntaxen stöder ett antal vanliga Sök operatorer som operatörerna AND, OR, NOT, fras, suffix och prioritet.

Den [fullständiga Lucene-frågesyntaxen](query-Lucene-syntax.md#bkmk_syntax), som aktive ras `queryType=full` när du lägger till begäran, exponerar det vanligaste och lättfattliga programspecifika-frågespråket som utvecklats som en del av [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Fullständig syntax utökar den enkla syntaxen. Alla frågor som du skriver för den enkla syntaxen körs under hela Lucene-parsern. 

I följande exempel visas punkten: samma fråga, men med olika inställningar för queryType ger det olika resultat. I den första frågan behandlas den `^3` som en del av Sök termen.

```
queryType=simple&search=mountain beach garden ranch^3&searchFields=description&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket
```

Samma fråga som använder den fullständiga Lucene-tolken tolkar fält höjningen på "Ranch", vilket ökar Sök rankningen för resultat som innehåller den aktuella termen.

```
queryType=full&search=mountain beach garden ranch^3&searchFields=description&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Typer av frågor

Azure Search stöder en mängd olika frågetyper. 

| Frågetyp | Användning | Exempel och mer information |
|------------|--------|-------------------------------|
| Texts ökning med fritext | Sök parameter och antingen parser| Fullständig texts ökning söker efter en eller flera termer i alla sökbara fält i ditt index och fungerar på samma sätt som du förväntar dig att en sökmotor som Google eller Bing fungerar. Exemplet i introduktionen är full texts ökning.<br/><br/>Full texts ökning underbörjar text analys med hjälp av standard Lucene Analyzer (som standard) till gemener alla villkor, ta bort stoppord som "The". Du kan åsidosätta standardvärdet med [icke-engelska analyserare](index-add-language-analyzers.md#language-analyzer-list) eller [specialiserade oberoende-analyser](index-add-custom-analyzers.md#AnalyzerTable) som ändrar text analyser. Ett exempel är ett [nyckelord](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) som behandlar hela innehållet i ett fält som en enskild token. Detta är användbart för data som post nummer, ID: n och vissa produkt namn. | 
| Filtrerad sökning | [OData filter-uttryck](query-odata-filter-orderby-syntax.md) och antingen parser | Filter frågor utvärderar ett booleskt uttryck över alla *filter* bara fält i ett index. Till skillnad från sökningen matchar en filter fråga det exakta innehållet i ett fält, inklusive Skift läges känslighet i sträng fält. En annan skillnad är att filter frågor uttrycks i OData-syntax. <br/>[Exempel på filter uttryck](search-query-simple-examples.md#example-3-filter-queries) |
| Geo-sökning | [EDM. GeographyPoint-typ](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) i fältet, filter uttrycket och antingen parsern | Koordinater som lagras i ett fält med en EDM. GeographyPoint används för "hitta nära mig" eller mappbaserade Sök kontroller. <br/>[Exempel på Geo-sökning](search-query-simple-examples.md#example-5-geo-search)|
| Intervalls ökning | filter uttryck och enkel parser | I Azure Search skapas intervall frågor med hjälp av filter parametern. <br/>[Exempel på intervall filter](search-query-simple-examples.md#example-4-range-filters) | 
| [Sökning efter fält](query-lucene-syntax.md#bkmk_fields) | Sök parameter och fullständig parser | Bygg ett sammansatt frågeuttryck riktade mot ett enskilt fält. <br/>[Exempel på fält som söks](search-query-lucene-examples.md#example-2-fielded-search) |
| [Fuzzy-sökning](query-lucene-syntax.md#bkmk_fuzzy) | Sök parameter och fullständig parser | Matchningar på villkor som har en liknande konstruktion eller stavning. <br/>[Exempel på fuzzy-sökning](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [närhets sökning](query-lucene-syntax.md#bkmk_proximity) | Sök parameter och fullständig parser | Söker efter termer som är nära varandra i ett dokument. <br/>[Exempel på närhet](search-query-lucene-examples.md#example-4-proximity-search) |
| [term förstärkning](query-lucene-syntax.md#bkmk_termboost) | Sök parameter och fullständig parser | Rangordna ett dokument högre om det innehåller den ökade termen, i förhållande till andra som inte är det. <br/>[Exempel på term förstärkning](search-query-lucene-examples.md#example-5-term-boosting) |
| [sökning efter reguljära uttryck](query-lucene-syntax.md#bkmk_regex) | Sök parameter och fullständig parser | Matchningar baserat på innehållet i ett reguljärt uttryck. <br/>[Exempel på reguljära uttryck](search-query-lucene-examples.md#example-6-regex) |
|  [sökning med jokertecken eller prefix](query-lucene-syntax.md#bkmk_wildcard) | Sök parameter och fullständig parser | Matchar baserat på ett prefix och tilde (`~`) eller ett enskilt tecken`?`(). <br/>[Sök exempel i jokertecken](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="manage-search-results"></a>Hantera Sök Resultat 

Frågeresultat strömmas som JSON-dokument i REST API, men om du använder .NET-API: er är serialiseringen inbyggd. Du kan forma resultatet genom att ange parametrar i frågan och välja vissa fält för svaret.

Parametrar i frågan kan användas för att strukturera resultat uppsättningen på följande sätt:

+ Begränsa eller gruppera antalet dokument i resultatet (50 som standard)
+ Att välja fält som ska inkluderas i resultaten
+ Ange sorterings ordning
+ Lägg till träff markeringar för att dra uppmärksamheten till matchande termer i Sök resultatets brödtext

### <a name="tips-for-unexpected-results"></a>Tips för oväntade resultat

Ibland är ämnet och inte resultat strukturen oväntade. När frågans resultat inte är det du förväntar dig, kan du prova de här frågans ändringar för att se om resultaten förbättras:

+ Ändra **`searchMode=any`** (standard **`searchMode=all`** ) om du vill kräva matchningar för alla kriterier i stället för något av villkoren. Detta gäller särskilt när booleska operatorer ingår i frågan.

+ Ändra frågans teknik om text-eller lexikalisk analys är nödvändig, men typen av fråga utesluter språklig bearbetning. I full texts ökning korrigeras text eller lexikala analyser automatiskt för stavnings fel, singular-pluraling-formulär och även oregelbundna verb eller substantiv. För vissa frågor, till exempel fuzzy eller jokertecken, är text analyser inte en del av pipelinen för analys av frågor. I vissa fall har reguljära uttryck använts som en lösning. 

### <a name="paging-results"></a>Växla resultat
I Azure Search kan du enkelt implementera sidindelning av sökresultaten. Genom att använda **`top`** - **`skip`** och-parametrarna kan du smidigt utfärda Sök begär Anden som gör att du kan ta emot den totala uppsättningen Sök resultat i hanterbara, ordnade del mängder som gör det enkelt att snabbt aktivera lämpliga Sök gränssnitts metoder. När du tar emot dessa mindre delmängder med resultat kan du också se antalet dokument i den fullständiga uppsättningen sökresultat.

Mer information om hur du använder sidindelning för sökresultat finns i artikeln [Sidindela sökresultat i Azure Search](search-pagination-page-layout.md).

### <a name="ordering-results"></a>Ordna resultaten
När du får resultat från en sökfråga kan du begära att Azure Search visar resultaten ordnade efter värden i ett visst fält. Som standard ordnar Azure Search sökresultaten baserat på rangordningen för varje dokuments sökpoäng, som härleds från [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Om du vill Azure Search returnera dina resultat sorterade efter ett annat värde än Sök poängen, kan du använda **`orderby`** Sök parametern. Du kan ange värdet för **`orderby`** parametern för att inkludera fält namn och anrop [ **`geo.distance()` till funktionen**](query-odata-filter-orderby-syntax.md) för geospatiala värden. Varje uttryck kan följas av `asc` för att indikera att resultat begärs i stigande ordning och **`desc`** för att indikera att resultat begärs i fallande ordning. Standardinställningen är stigande ordning.


### <a name="hit-highlighting"></a>Träffmarkering
I Azure Search är det enkelt **`highlight`** att framhäva den EXAKTA delen av Sök resultaten som matchar Sök frågan med parametrarna, **`highlightPreTag`** och **`highlightPostTag`** . Du kan ange vilka *sökbara* fält vars matchade text ska framhävas samt ange de exakta strängtaggarna som ska läggas till i början och slutet av den matchade texten som Azure Search returnerar.

## <a name="see-also"></a>Se också

+ [Hur full texts ökning fungerar i Azure Search (arkitektur för att parsa frågor)](search-lucene-query-architecture.md)
+ [Sök Utforskaren](search-explorer.md)
+ [Så här frågar du i .NET](search-query-dotnet.md)
+ [Fråga i REST](search-create-index-rest-api.md)
