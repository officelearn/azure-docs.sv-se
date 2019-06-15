---
title: Filter för att samla sökresultat i ett index – Azure Search
description: Filtrera efter användaridentitet för säkerhet, språk, geografiska plats eller numeriska värden att minska sökresultat på frågor i Azure Search, en värdbaserad molnsöktjänst på Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 8676ad48bc4fab6149db00d778349ac1acd7223d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67062381"
---
# <a name="filters-in-azure-search"></a>Filter i Azure Search 

En *filter* innehåller kriterier för att välja dokumenten som används i en Azure Search-fråga. Ofiltrerade search innehåller alla dokument i indexet. Ett filter definierar en sökfråga till en delmängd av dokument. Ett filter kan till exempel begränsa fulltextsökning till bara dessa produkter med ett visst kaffemärke eller i färg, vid prispunkter överskrider ett visst tröskelvärde.

Vissa sökupplevelser införa filter krav som en del av implementeringen, men du kan använda filter när du vill begränsa sökningen med hjälp av *värdebaserad* kriterier (målgrupp letar till produkttyp ”böcker” kategori ” icke-fiction ”publicerats av” Simon och Schuster ”).

Om målet är i stället riktade sökning på specifika data från *strukturer* (målgrupp sökning till ett Kundrecensioner fält), alternativa metoder som beskrivs nedan.

## <a name="when-to-use-a-filter"></a>När du ska använda ett filter

Filter är grundläggande för flera sökupplevelser, inklusive ”hitta närheten”, aspektbaserad navigering och security filtrerar som visar endast de dokument som en användare har behörighet att se. Om du implementerar en av dessa upplevelser krävs ett filter. Det är kopplat till sökfrågan som tillhandahåller geoplats-koordinater kategorin aspekten som valts av användaren eller säkerhets-ID för begäranden.

Följande: scenarier

1. Använd ett filter för att dela upp ditt index baserat på datavärden i indexet. Du kan skapa ett filter för att uttryckligen välja dokument som uppfyller dina kriterier (i Seattle, condos, Hamnområde) med ett schema med stad, bostadsmarknaden typ och bekvämligheterna kan. 

   Fulltextsökning med samma indata ger ofta liknande resultat, men ett filter är mer exakt som det krävs en exakt matchning av filtervillkor mot innehåll i ditt index. 

2. Använd ett filter om sökupplevelsen levereras med ett filter-krav:

   * [Aspektbaserad navigering](search-faceted-navigation.md) använder ett filter för att skicka tillbaka den aspekt kategori som valts av användaren.
   * GEO-sökning använder ett filter för att skicka koordinaterna för den aktuella platsen i ”Sök närheten” appar. 
   * Säkerhetsfilter skicka säkerhetsidentifierare som villkor, där en matchning i indexet fungerar som proxy för åtkomsträttigheter till dokumentet.

3. Använd ett filter om du vill sökvillkor i ett numeriskt fält. 

   Numeriska fält är hämtningsbara i dokumentet och kan visas i sökresultaten, men de är inte sökbara (beroende på fulltextsökning) individuellt. Om du behöver urvalskriterier baserat på numeriska data kan du använda ett filter.

### <a name="alternative-methods-for-reducing-scope"></a>Alternativa metoder för att minska omfånget

Om du vill ha en begränsad inverkan i sökresultaten är filter inte ditt enda alternativ. Dessa alternativ kan vara ett bättre alternativ, beroende på ditt mål:

 + `searchFields` Frågeparametern heller sökningen till specifika fält. Om ditt index tillhandahåller olika fält för engelska och spanska beskrivningar, kan du till exempel använda searchFields och ange vilka fält som ska användas för textsökning. 

+ `$select` Parametern används för att ange vilka fält som ska ingå i en resultatuppsättning, effektivt trimmar svaret innan det skickas till det anropande programmet. Den här parametern inte förfina frågan eller minska mängden dokumentet, men om ett mindre svar är vad den här parametern är ett alternativ för att tänka på. 

Läs mer om antingen parametern [söka efter dokument > begär > frågeparametrar](https://docs.microsoft.com/rest/api/searchservice/search-documents#request).


## <a name="how-filters-are-executed"></a>Hur filter körs

När en fråga körs en filter-parser accepterar villkor som indata, konverterar uttrycket till atomiska booleska uttryck som visas som ett träd och sedan utvärderar filterträdet över filtrerbara fält i ett index.

Filtrering utförs på tillsammans med sökning, kvalificera dig för vilka dokument som ska ingå i bearbetningen nedströms för dokumentet hämtning och relevans bedömning. Tillsammans med en söksträng, minskar filtret effektivt återkallande uppsättning senare sökåtgärden. Om du använder enbart (till exempel när frågesträngen är tom där `search=*`), filtervillkoren är den enda inmatningen. 

## <a name="defining-filters"></a>Definiera filter

Filter är OData-uttryck har uppvisat med hjälp av en [delmängd av OData V4-syntaxen stöds i Azure Search](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

Du kan ange ett filter för varje **search** igen, men själva filtret kan innehålla flera fält, flera villkor och om du använder en **ismatch** funktion, flera uttryck i fulltextsökning. Du kan ange predikat i valfri ordning (beroende på regler för operatorer) i ett filteruttryck i flera delar. Det finns någon märkbar fördel prestanda om du försöker ändra predikat i en viss sekvens.

En av begränsningar i ett filteruttryck är den maximala storleksgränsen för begäran. Hela begäran, inklusive filter, kan vara högst 16 MB för INLÄGG eller 8 KB för GET. Det finns också en gräns för antalet satser i filteruttrycket. En bra tumregel är att om du har hundratals satser du riskerar körs till gränsen. Vi rekommenderar att du designa programmet så att den inte genererar filter med obegränsad storlek.

I följande exempel representerar prototypical filterdefinitioner i flera API: er.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2019-05-06

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2019-05-06
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "baseRate lt 150",
            Select = new[] { "hotelId", "description" }
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>Filtrera användningsmönster

I följande exempel visas flera användningsmönster för filter scenarier. Fler idéer finns [OData-uttryckssyntax > exempel](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples).

+ Fristående **$filter**, utan en frågesträng, användbart när filteruttrycket kan att kvalificera dokument av intresse. Utan en frågesträng finns det inga lexikal eller språklig analys, ingen bedömning och inga rangordning. Observera att strängen är bara en asterisk, vilket innebär att ”matcha alla dokument”.

   ```
   search=*&$filter=(baseRate ge 60 and baseRate lt 300) and accommodation eq 'Hotel' and city eq 'Nogales'
   ```

+ Kombinationen av frågesträngen och **$filter**, där filtret skapar delmängden och frågesträngen innehåller termen indata för fulltextsökning över filtrerade delmängd. Använda ett filter med en frågesträng är det vanligaste användningsmönstret.

   ```
   search=hotels ocean$filter=(baseRate ge 60 and baseRate lt 300) and city eq 'Los Angeles'
   ```

+ Sammansatt frågor, avgränsade med ”eller”, var och en med sin egen villkor (till exempel ”beagles” i ”hund”) eller ”siamese” i ”kategori”. Uttryck i kombination med `or` utvärderas individuellt med summan av dokument som matchar varje uttryck som skickas tillbaka i svaret. Det här användningsmönstret uppnås via den `search.ismatchscoring` funktion. Du kan också använda icke-bedömning-version `search.ismatch`.

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'description') or category eq 'Luxury'
   ```

  Det är också möjligt att kombinera fulltextsökning via `search.ismatchscoring` med filter med `and` i stället för `or`, men detta har samma funktioner med hjälp av den `search` och `$filter` parametrar i en sökbegäran. Till exempel ger följande två frågor samma resultat:

  ```
  $filter=search.ismatchscoring('pool') and rating ge 4

  search=pool&$filter=rating ge 4
  ```

Följ upp med de här artiklarna för heltäckande vägledning på specifika användningsfall:

+ [Facet-filter](search-filters-facets.md)
+ [Språkfilter](search-filters-language.md)
+ [Säkerhetsoptimering](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Krav för fältet för filtrering

I REST API, Filtrerbart är *på* som standard för enkel fält. Filtrerbara fält öka indexstorleken på; Se till att ange `"filterable": false` för fält som du inte planerar att använda i ett filter. Läs mer om inställningar för fältdefinitioner [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

I .NET-SDK på filtrerbara är *av* som standard. Du kan göra ett fält filtrerbara genom att ange den [IsFilterable egenskapen](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet) av motsvarande [fältet](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field?view=azure-dotnet) objekt till `true`. Du kan också göra detta deklarativt med hjälp av den [IsFilterable attributet](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). I exemplet nedan attributet är inställt på den `BaseRate` egenskapen för en modellklass som mappar till indexdefinitionen.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Gör ett befintligt fält filtrerbara

Du kan inte ändra befintliga fält så att de blir filtrerbara. I stället måste du lägga till ett nytt fält eller återskapa indexet. Mer information om när ett index eller sedan hämtar nytt fält finns i [återskapar ett Azure Search-index](search-howto-reindex.md).

## <a name="text-filter-fundamentals"></a>Grunderna i text-filter

Textfilter matcha strängfält mot literala strängar som du anger i filtret. Till skillnad från fulltextsökning finns det ingen lexikal analys eller radbrytningar för Textfilter, så jämförelserna gäller för exakta matchningar. Anta exempelvis att ett fält *f* innehåller ”solig dag” `$filter=f eq 'Sunny'` matchar inte, men `$filter=f eq 'sunny day'` kommer. 

Språk-ID är skiftlägeskänsliga. Det finns inga lägre skiftläge alltid i övre ord: `$filter=f eq 'Sunny day'` kommer inte att hitta ”solig dag”.

### <a name="approaches-for-filtering-on-text"></a>Metoder för att filtrera efter text

| Metoden | Beskrivning | När du ska använda detta |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | En funktion som matchar ett fält mot en avgränsad lista med strängar. | Rekommenderas för [säkerhetsfilter](search-security-trimming-for-azure-search.md) och eventuella filter som där många rå text-värden måste matchas med ett strängfält. Den **search.in** funktionen är avsedd för hastighet och är mycket snabbare än att uttryckligen jämföra fältet mot varje sträng med hjälp av `eq` och `or`. | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | En funktion som gör att du kan blanda fulltextsökning åtgärder med strikt booleska åtgärder i samma filteruttrycket. | Använd **search.ismatch** (eller motsvarande bedömnings, **search.ismatchscoring**) när du vill att flera kombinationer av Sök-filter i en begäran. Du kan också använda den för en *innehåller* och filtrerar på en partiell sträng i en större sträng. |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | Ett uttryck för användardefinierade består av fält, operatorer och värden. | Använd det här alternativet när du vill hitta exakta matchningar mellan ett strängfält och ett strängvärde. |

## <a name="numeric-filter-fundamentals"></a>Numeriska filter grunderna

Numeriska fält är inte `searchable` i samband med fulltextsökning. Endast strängar är föremål för fulltextsökning. Exempel: Om du anger 99,99 som en sökterm du inte får tillbaka objekt priset $99,99. I stället visas objekt som har många 99 i anslutningssträng-fälten i dokumentet. Därför, om du har numeriska data kan antas att du ska använda dem för filter, inklusive intervall, fasetter, grupper och så vidare. 

Dokument som innehåller numeriska fält (pris, storlek, SKU, -ID) ange dessa värden i sökresultaten om fältet är märkt `retrievable`. Den här punkten är att textsökning själva inte gäller för numeriskt fälttyper.

## <a name="next-steps"></a>Nästa steg

Försök först **Sökutforskaren** i portalen för att skicka frågor med **$filter** parametrar. Den [real-egendom-exempelindex](search-get-started-portal.md) ger intressanta resultat för följande filtrerade frågor när du klistrar in dem i sökfältet:

```
# Geo-filter returning documents within 5 kilometers of Redmond, Washington state
# Use $count=true to get a number of hits returned by the query
# Use $select to trim results, showing values for named fields only
# Use search=* for an empty query string. The filter is the sole input

search=*&$count=true&$select=description,city,postCode&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5

# Numeric filters use comparison like greater than (gt), less than (lt), not equal (ne)
# Include "and" to filter on multiple fields (baths and bed)
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=baths gt 3 and beds gt 4

# Text filters can also use comparison operators
# Wrap text in single or double quotes and use the correct case
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=city gt 'Seattle'
```

Om du vill arbeta med fler exempel finns i [OData-Filter uttryckssyntax > exempel](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples).

## <a name="see-also"></a>Se också

+ [Hur Fullständig textsökning fungerar i Azure Search](search-lucene-query-architecture.md)
+ [Söka efter dokument REST-API](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [Enkel frågesyntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Lucene-frågesyntax](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Datatyper som stöds](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
