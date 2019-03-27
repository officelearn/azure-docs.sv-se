---
title: Frågetyper och sammansättning – Azure Search
description: Grunderna för att skapa en sökfråga i Azure Search, med parametrar för att filtrera, markera och sortera resultaten.
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/25/2019
ms.custom: seodec2018
ms.openlocfilehash: cfc9b44963f6880e97859bc7ab77bff12d258471
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58500176"
---
# <a name="how-to-compose-a-query-in-azure-search"></a>Hur du skriver en fråga i Azure Search

I Azure Search är en fråga en fullständig specifikation av en fram och åter igen. Parametrar på begäran ger matchningsvillkor för att söka efter dokument i ett index, köra instruktioner för motor- och direktiv för att forma svaret. 

En fråga är en omfattande konstruktion som specificerar vilka fält som omfattade, hur du söker efter, vilka fält som ska gå tillbaka till sorterings- eller filter och så vidare. Angivet används körs en fråga mot alla sökbara fält som en fullständig text search-åtgärd returnerar en inte har poängsatts resultatuppsättningen i valfri ordning.

## <a name="apis-and-tools-for-testing"></a>API: er och verktyg för testning

I följande tabell visas de API: er och verktyg-baserade metoderna för att skicka frågor.

| Metod | Beskrivning |
|-------------|-------------|
| [Sökutforskaren (portal)](search-explorer.md) | Innehåller ett sökfält och alternativ för val av index och api-versionen. Resultaten returneras som JSON-dokument. <br/>[Läs mer.](search-get-started-portal.md#query-index) | 
| [Postman eller Fiddler](search-fiddler.md) | Testverktyg för webb-är ett utmärkt alternativ för att utforma REST-anrop. REST API har stöd för varje möjlig åtgärd i Azure Search. I den här artikeln lär du dig hur du ställer in en HTTP-frågehuvudet och brödtext för att skicka begäranden till Azure Search.  |
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Klient som kan användas för att fråga en Azure Search-index.  <br/>[Läs mer.](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Söka efter dokument (REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | GET eller POST-metoder i ett index med hjälp av frågeparametrar för ytterligare indata.  |

## <a name="a-first-look-at-query-requests"></a>En första titt på frågebegäranden

Exempel är användbara för introducerar nya begrepp. Som en representativ fråga konstrueras i den [REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)använder den här exempel mål den [fastigheter demo index](search-get-started-portal.md) och innehåller gemensamma parametrar.

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

+ **`queryType`** Anger parsern, som i Azure Search kan vara den [standard enklare frågeparsern](search-query-simple-examples.md) (optimala för fulltextsökning), eller [fullständig frågeparser (Lucene)](search-query-lucene-examples.md) används för avancerad fråga konstruktioner som reguljära uttryck , närhetssökning, fuzzy och sökning med jokertecken, t.ex.

+ **`search`** innehåller matchningen villkor, vanligtvis text men ofta tillsammans med booleska operatorer. Enda fristående är *termen* frågor. Omges av citattecken flerdelade frågorna är *diskussionsämne* frågor. Sökning kan vara Odefinierad, som i **`search=*`**, men mer sannolikt består av termer och fraser operatörer som liknar det som visas i exemplet.

+ **`searchFields`** är valfritt, används för att begränsa Frågekörningen specifika fält.

Svaren är också formade genom de parametrar som du inkluderar i frågan. I det här exemplet resultatmängden består av fält som anges i den **`select`** instruktionen. Endast de översta 10 träffarna som returneras i den här frågan, men **`count`** berättas hur många dokument som matchar den totala. I den här frågan sorteras raderna efter daysOnMarket.

I Azure Search är Frågekörningen alltid mot ett index, autentiseras med en api-nyckel som tillhandahölls i begäran. I REST finns både i begärandehuvuden.

### <a name="how-to-run-this-query"></a>Hur du kör den här frågan

Om du vill köra den här frågan använder [Sök explorer och fastigheter demo indexet](search-get-started-portal.md). 

Du kan klistra in den här frågesträngen i sökfältet i Utforskaren: `search=seattle townhouse +lake&searchFields=description, city&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket`

## <a name="how-query-operations-are-enabled-by-the-index"></a>Hur frågeåtgärder aktiveras av indexet

Design för indexet och fråga design är nära sammanlänkade i Azure Search. En grundläggande fakta veta direkt är att den *indexschema*, bestämmer vilken typ av fråga som du kan skapa med attribut på varje fält. 

Indexattribut i ett fält ange tillåtna åtgärder – om ett fält är *sökbara* i indexet, *hämtningsbar* i resultatet, *sorterbar*,  *filtrerbar*, och så vidare. I frågesträngen exempel `"$orderby": "daysOnMarket"` fungerar bara eftersom fältet daysOnMarket har markerats som *sorterbar* i indexschemat. 

![Index-definition för fastigheter exemplet](./media/search-query-overview/realestate-sample-index-definition.png "Index-definition för fastigheter")

Skärmbilden ovan är en lista över indexattribut för fastigheter. Du kan visa hela indexschemat i portalen. Läs mer om indexattribut [skapa Index REST API](https://docs.microsoft.com/rest/api/searchservice/create-index).

> [!Note]
> Vissa frågefunktioner aktiveras index hela snarare än på basis av per fält. Funktionerna omfattar: [synonymen mappar](search-synonyms.md), [anpassade analysverktyg](index-add-custom-analyzers.md), [förslagsställare konstruktioner (för automatisk komplettering och föreslagna frågor)](index-add-suggesters.md), [bedömning logik för att rangordna resultat](index-add-scoring-profiles.md).

## <a name="elements-of-a-query-request"></a>Element i en fråga

Frågor är alltid riktat mot ett index. Du kan inte ansluta till index eller skapa anpassade eller tillfälliga datastrukturer som ett mål för frågan. 

Obligatoriska elementen på en frågebegäran om innehåller följande komponenter:

+ Slutpunkten och index dokument insamling av webbtjänstens, uttryckt i form av en URL som innehåller fast och användardefinierade komponenter: **`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`** (Endast REST) är nödvändigt eftersom fler än en version av API: et är tillgänglig vid alla tidpunkter. 
+ **`api-key`**, en fråga eller admin api-nyckel, autentiserar begäran till din tjänst.
+ **`queryType`**, enkel eller fullständig, vilket kan utelämnas om du använder inbyggda standard enkel syntax.
+ **`search`** eller **`filter`** ger matchningen villkor, vilket kan vara okänt om du vill utföra en tom sökning. Båda frågetyperna av diskuteras när det gäller enkel parsern, men även avancerade frågor kräver parametern search för att skicka komplexa frågeuttryck.

Alla andra search-parametrar är valfria. En fullständig lista över attribut finns [skapa Index (REST)](https://docs.microsoft.com/rest/api/searchservice/create-index). En närmare titt på hur parametrar används under bearbetning, se [hur Fullständig textsökning fungerar i Azure Search](search-lucene-query-architecture.md).

## <a name="choose-a-parser-simple--full"></a>Välj en parser: enkel | fullständig

Azure Search finns på Apache Lucene och ger dig ett val mellan två fråga Parser för hantering av vanliga och specialiserade frågor. Begäranden som använder enkel parsern formuleras med hjälp av den [enkla frågesyntaxen](query-simple-syntax.md), valda som standard för vilken hastighet och effektivitet i fri form textfrågor. Den här syntaxen stöder ett antal vanliga sökoperatorer som AND-, eller inte, frasen, suffix och prioritetsoperatorer.

Den [fullständig Lucene-frågesyntax](query-Lucene-syntax.md#bkmk_syntax), aktiverat när du lägger till `queryType=full` på begäran, visar det mycket antagen och uttrycksfulla frågespråket som utvecklats som en del av [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Fullständig syntax utökar enkel syntax. Alla frågor som du skriver för enkel syntax körs under den fullständiga Lucene-parsern. 

I följande exempel visas punkten: samma fråga, men med olika queryType inställningar, ger olika resultat. I den första frågan i `^3` behandlas som en del av söktermen.

```
queryType=simple&search=mountain beach garden ranch^3&searchFields=description&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket
```

Samma fråga med hjälp av den fullständiga Lucene-parsern tolkar i fältet förstärkningen på ”gren”, vilket ökar sökrangordningen resultat som innehåller den specifika perioden.

```
queryType=full&search=mountain beach garden ranch^3&searchFields=description&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Typer av frågor

Azure Search har stöd för ett brett utbud av frågetyper. 

| Frågetyp | Användning | Mer information och exempel |
|------------|--------|-------------------------------|
| Fri form textsökning | Sökparameter och antingen parser| Fulltextsökning söker igenom en eller flera termer i alla *sökbara* fält i ditt index och fungerar på samma sätt som du förväntar dig en sökmotor som Google eller Bing fungerar. Exemplet i inledningen är fulltextsökning.<br/><br/>Fulltextsökning genomgår textanalys med analysverktyget från Lucene (som standard) till gemena alla villkor, ta bort stoppord som ”och”. Du kan åsidosätta standardinställningen med [icke-engelska analysverktyg](index-add-language-analyzers.md#language-analyzer-list) eller [specialiserade språkoberoende analysverktyg](index-add-custom-analyzers.md#AnalyzerTable) som ändrar textanalys. Ett exempel är [nyckelordet](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) som behandlar allt i ett fält som en enskild token. Detta är användbart för data som postnummer, ID: N och vissa produktnamn. | 
| Filtrerade sökning | [OData-filteruttryck](query-odata-filter-orderby-syntax.md) och antingen parser | Filterfrågor utvärderar ett booleskt uttryck över alla *filtrerbara* fält i ett index. Till skillnad från sökning matchar en filterfråga det exakta innehållet i ett fält, inklusive skiftlägeskänslighet på strängfält. En annan skillnaden är att filterfrågor uttrycks i OData-syntax. <br/>[Uttryck-filter, exempel](search-query-simple-examples.md#example-3-filter-queries) |
| Geo-sökning | [Typ av Edm.GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) på fältet och filteruttrycket antingen parser | Koordinaterna som lagras i ett fält med en Edm.GeographyPoint är används för ”hitta en nära mig” eller kartbaserade Genomsök kontroller. <br/>[Exempel på GEO-sökning](search-query-simple-examples.md#example-5-geo-search)|
| Sök efter intervall | filteruttrycket och enkel parser | I Azure Search skapas intervallfrågor med hjälp av Filterparametern. <br/>[Intervallet filter, exempel](search-query-simple-examples.md#example-4-range-filters) | 
| [Trafik fältet filtrering](query-lucene-syntax.md#bkmk_fields) | Sökparameter och fullständig parser | Skapa ett sammansatt frågeuttryck som riktar in sig på ett fält. <br/>[Trafik fältet filtrering exempel](search-query-lucene-examples.md#example-2-intra-field-filtering) |
| [Fuzzy-sökning](query-lucene-syntax.md#bkmk_fuzzy) | Sökparameter och fullständig parser | Matchningar på villkor att ha en liknande konstruktion eller stavning. <br/>[Fuzzy-sökning-exempel](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [närhetssökning](query-lucene-syntax.md#bkmk_proximity) | Sökparameter och fullständig parser | Söker efter termer som är nära varandra i ett dokument. <br/>[Närhet search-exempel](search-query-lucene-examples.md#example-4-proximity-search) |
| [termförstärkning](query-lucene-syntax.md#bkmk_termboost) | Sökparameter och fullständig parser | Rangordnar ett dokument som är högre om den innehåller förbättrat termen, i förhållande till andra inte stöds. <br/>[Termen boosting-exempel](search-query-lucene-examples.md#example-5-term-boosting) |
| [sökning med reguljära uttryck](query-lucene-syntax.md#bkmk_regex) | Sökparameter och fullständig parser | Matchingar baserat på innehållet i ett reguljärt uttryck. <br/>[Reguljärt uttryck exempel](search-query-lucene-examples.md#example-6-regex) |
|  [sökning med jokertecken eller prefix](query-lucene-syntax.md#bkmk_wildcard) | Sökparameter och fullständig parser | Matchar baserat på ett prefix och tilde (`~`) eller valfritt tecken (`?`). <br/>[Jokertecken search-exempel](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="manage-search-results"></a>Hantera sökresultat 

Frågeresultat strömmas som JSON-dokument i REST-API, men om du använder .NET API: er, serialisering är inbyggd i. Du kan forma resultat genom att ange parametrar på frågan, att välja specifika fält för svaret.

Parametrar för frågan kan användas för att strukturera resultatmängd på följande sätt:

+ Att begränsa eller batchbearbetning antalet dokument i resultatet (50 som standard)
+ Välja fält som ska inkluderas i resultaten
+ Ange någon sorteringsordning
+ Att lägga till träffar markeringar för att uppmärksamma matchar villkoren i brödtexten i sökresultaten

### <a name="tips-for-unexpected-results"></a>Tips för oväntade resultat

Ibland kan är ämnet och inte strukturen för resultat oväntat. När frågan resultat är inte vad du förväntar dig att se, kan du prova följande fråga ändringar för att se om resultaten förbättra:

+ Ändra **`searchMode=any`** (standard) till **`searchMode=all`** att kräva matchningar på alla villkor i stället för något av villkoren. Detta gäller särskilt när booleska operatorer ingår frågan.

+ Ändra fråga tekniken om text eller lexikal analys krävs, men frågetypen utesluter språkliga bearbetning. I fulltextsökning, text eller lexikal analys autocorrects för stavfel, rapportanvändare plural ordformer, och även oregelbunden verb eller substantiv. För vissa frågor som fuzzy eller sökning med jokertecken, textanalys är inte en del av frågan parsning pipeline. Reguljära uttryck har använts som en lösning för vissa scenarier. 

### <a name="paging-results"></a>Växla resultat
I Azure Search kan du enkelt implementera sidindelning av sökresultaten. Med hjälp av den **`top`** och **`skip`** parametrar, kan du enkelt skicka sökförfrågningar som gör att du kan ta emot den fullständiga uppsättningen sökresultat i hanterbara, ordnade delmängder som enkelt aktivera bra search UI-metoder. När du tar emot dessa mindre delmängder med resultat kan du också se antalet dokument i den fullständiga uppsättningen sökresultat.

Mer information om hur du använder sidindelning för sökresultat finns i artikeln [Sidindela sökresultat i Azure Search](search-pagination-page-layout.md).

### <a name="ordering-results"></a>Ordna resultaten
När du får resultat från en sökfråga kan du begära att Azure Search visar resultaten ordnade efter värden i ett visst fält. Som standard ordnar Azure Search sökresultaten baserat på rangordningen för varje dokuments sökpoäng, som härleds från [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Om du vill att Azure Search ska returnera resultaten ordnade efter ett annat värde än sökpoängen kan du använda den **`orderby`** sökparametern. Du kan ange värdet för den **`orderby`** parametern att inkludera fältnamn och anrop till den [  **`geo.distance()` funktionen** ](query-odata-filter-orderby-syntax.md) för geospatiala värden. Varje uttryck kan åtföljas av `asc` att ange att resultaten har begärt i stigande ordning, och **`desc`** att ange att resultaten har begärt i fallande ordning. Standardinställningen är stigande ordning.


### <a name="hit-highlighting"></a>Träffmarkering
I Azure Search betonar exakt den del av sökresultatet som matchar sökfrågan är ett enkelt sätt med hjälp av den **`highlight`**, **`highlightPreTag`**, och **`highlightPostTag`** parametrar. Du kan ange vilka *sökbara* fält vars matchade text ska framhävas samt ange de exakta strängtaggarna som ska läggas till i början och slutet av den matchade texten som Azure Search returnerar.

## <a name="see-also"></a>Se också

+ [Hur Fullständig textsökning fungerar i Azure Search (fråga parsning arkitektur)](search-lucene-query-architecture.md)
+ [Sökutforskaren](search-explorer.md)
+ [Söka i .NET](search-query-dotnet.md)
+ [Hur du frågar efter resten](search-create-index-rest-api.md)
