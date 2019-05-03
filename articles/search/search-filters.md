---
title: Filter för att samla sökresultat i ett index – Azure Search
description: Filtrera efter användaridentitet för säkerhet, språk, geografiska plats eller numeriska värden att minska sökresultat på frågor i Azure Search, en värdbaserad molnsöktjänst på Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 49f971fb50d0a8a6a0dab09158f780206a4d32f1
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024834"
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

+ `$select` Parametern används för att ange vilka fält som ska ingå i en resultatuppsättning, effektivt trimmar svaret innan det skickas till det anropande programmet. Den här parametern inte förfina frågan eller minska mängden dokumentet, men om en detaljerad svar är vad den här parametern är ett alternativ för att tänka på. 

Läs mer om antingen parametern [söka efter dokument > begär > frågeparametrar](https://docs.microsoft.com/rest/api/searchservice/search-documents#request).


## <a name="filters-in-the-query-pipeline"></a>Filter i sökfrågans pipeline

När en fråga körs en filter-parser accepterar villkor som indata, konverterar uttrycket till atomiska booleska uttryck och skapar ett filterträd utvärderas sedan filtrerbara fält i ett index.  

Filtrering utförs innan sökning, kvalificera dig för vilka dokument som ska ingå i bearbetningen nedströms för dokumentet hämtning och relevans bedömning. Tillsammans med en söksträng, minskar filtret effektivt ytan på senare sökåtgärden. Om du använder enbart (till exempel när frågesträngen är tom där `search=*`), filtervillkoren är den enda inmatningen. 

## <a name="filter-definition"></a>Filterdefinitionen

Filter är OData-uttryck har uppvisat med hjälp av en [delmängd av OData V4-syntaxen stöds i Azure Search](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

Du kan ange ett filter för varje **search** igen, men själva filtret kan innehålla flera fält, flera villkor och om du använder en **ismatch** funktion, flera uttryck. Du kan ange predikat i valfri ordning i ett filteruttryck i flera delar. Det finns någon märkbar fördel prestanda om du försöker ändra predikat i en viss sekvens.

Hård gräns på ett filteruttryck är den maximala gränsen på begäran. Hela begäran, inklusive filter, kan vara högst 16 MB för INLÄGG eller 8 KB för GET. Mjuka gränser stämmer med antalet satser i filteruttrycket. En bra tumregel är att om du har hundratals satser du riskerar körs till gränsen. Vi rekommenderar att du designa programmet så att den inte genererar filter med obegränsad storlek.

I följande exempel representerar prototypical filterdefinitioner i flera API: er.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2019-05-06

# Option 2: Use filter for POST and pass it in the header
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

```

## <a name="filter-design-patterns"></a>Filtrera designmönster

I följande exempel visas flera designmönster för filter scenarier. Fler idéer finns [OData-uttryckssyntax > exempel](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples).

+ Fristående **$filter**, utan en frågesträng, användbart när filteruttrycket kan att kvalificera dokument av intresse. Utan en frågesträng finns det inga lexikal eller språklig analys, ingen bedömning och inga rangordning. Observera att strängen är tom.

   ```
   search=*&$filter=(baseRate ge 60 and baseRate lt 300) and accommodation eq 'Hotel' and city eq 'Nogales'
   ```

+ Kombinationen av frågesträngen och **$filter**, där filtret skapar delmängden och frågesträngen innehåller termen indata för fulltextsökning över filtrerade delmängd. Med hjälp av ett filter med en frågesträng är det vanligaste kod-mönstret.

   ```
   search=hotels ocean$filter=(baseRate ge 60 and baseRate lt 300) and city eq 'Los Angeles'
   ```

+ Sammansatt frågor, avgränsade med ”eller”, var och en med sin egen villkor (till exempel ”beagles” i ”hund”) eller ”siamese” i ”kategori”. ELLER hade uttryck utvärderas individuellt, med svar från var och en kombinerade till ett svar som skickas tillbaka till det anropande programmet. Det här designmönstret uppnås via search.ismatch-funktion. Du kan använda icke-bedömning-versionen (search.ismatch) eller bedömnings-versionen (search.ismatchscoring).

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'description') or category eq 'Luxury'
   ```

Följ upp med de här artiklarna för heltäckande vägledning på specifika användningsfall:

+ [Facet-filter](search-filters-facets.md)
+ [Språkfilter](search-filters-language.md)
+ [Säkerhetsoptimering](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Krav för fältet för filtrering

I REST API, Filtrerbart är *på* som standard. Filtrerbara fält öka indexstorleken på; Se till att ange `filterable=FALSE` för fält som du inte planerar att använda i ett filter. Läs mer om inställningar för fältdefinitioner [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

I .NET-SDK på filtrerbara är *av* som standard. API för att ställa in egenskapen filtrerbara är [IsFilterable](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). I exemplet nedan dess uppsättning på fältdefinition BaseRate.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="reindexing-requirements"></a>Omindexering krav

Om ett fält är icke-filtrerbara och du vill göra det filtrerbara, måste du lägga till ett nytt fält eller återskapa det befintliga fältet. Ändra en fältdefinition ändrar den fysiska strukturen i indexet. I Azure Search indexeras alla kunna komma sökvägar för snabba frågor hastighet, vilket kräver återskapning av en av datastrukturerna när fältdefinitioner ändras. 

Återskapa enskilda fält kan vara en låg inverkan åtgärd som kräver en sammanfogning som skickar befintliga dokumentnyckeln och associerade värden till indexet, lämna resten av varje dokument intakta. Om det uppstår ett återskapning krav, se [indexering åtgärder (ladda upp, sammanfoga, mergeOrUpload, ta bort)](search-what-is-data-import.md#indexing-actions) för en lista med alternativ.


## <a name="text-filter-fundamentals"></a>Grunderna i text-filter

Textfilter är giltiga för strängfält som du vill att hämta vissa godtycklig samling av dokument baserat på värdena i search-index.

För Textfilter som består av strängar, finns det ingen lexikal analys eller ordseparation, så jämförelserna gäller för exakta matchningar. Anta exempelvis att ett fält *f* innehåller ”solig dag” `$filter=f eq 'Sunny'`matchar inte, men `$filter=f eq 'Sunny day'` kommer. 

Språk-ID är skiftlägeskänsliga. Det finns inga lägre skiftläge alltid i övre ord: `$filter=f eq 'Sunny day'` kommer inte att hitta ”solig dag”.


| Metoden | Beskrivning | 
|----------|-------------|
| [search.in()](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | En funktion som tillhandahåller kommaavgränsad lista med strängar för ett visst fält. Strängarna utgör filtervillkoren som används på varje fält i omfånget för frågan. <br/><br/>`search.in(f, ‘a, b, c’)` semantiskt motsvarar `f eq ‘a’ or f eq ‘b’ or f eq ‘c’`, förutom att det körs mycket snabbare när listan över värden är stor.<br/><br/>Vi rekommenderar den **search.in** fungerar för [säkerhetsfilter](search-security-trimming-for-azure-search.md) och för eventuella filter som består av rå text som ska matchas på värden i ett visst fält. Den här metoden är utformat för hastighet. Du kan förvänta dig subsecond svarstiden för hundratusentals värden. Även om det finns ingen explicit gräns för hur många objekt som du kan skicka till funktionen, ökar svarstiden i proportion till antalet strängar som du anger. | 
| [search.ismatch()](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | En funktion som gör att du kan blanda fulltextsökning åtgärder med strikt booleska åtgärder i samma filteruttrycket. Det gör att flera kombinationer av fråga filter i en begäran. Du kan också använda den för en *innehåller* och filtrerar på en partiell sträng i en större sträng. |  
| [$filter = fältet operatorsträng](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Ett uttryck för användardefinierade består av fält, operatorer och värden. | 

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
