---
title: Filter i Azure Search | Microsoft Docs
description: Filtrera efter användaridentitet för säkerhet, språk, geografiska plats eller numeriska värden att minska sökresultat på frågorna i Azure Search värdbaserade moln search-tjänsten på Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: heidist
ms.openlocfilehash: 9f891dbe3f051f2fb5bfd242830f3c30abede487
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="filters-in-azure-search"></a>Filter i Azure Search 

En *filter* innehåller kriterier för att välja dokument som används i en Azure Search-fråga. Ofiltrerade sökningen omfattar alla dokument i indexet. Ett filter scope i en fråga till en delmängd av dokument. Ett filter kan till exempel begränsa fulltextsökning till enbart de produkter som har ett visst kaffemärke eller färg, vid prispunkter överskrider ett visst tröskelvärde.

Vissa Sök upplevelser införa filter krav som en del av implementeringen, men du kan använda filter när du vill begränsa sökningen med *värde baserat* kriterier (målgrupp söka till produkttyp ”books” efter kategori ” icke-fiktion ”publicerats av” Simon & Schuster ”).

Om målet är i stället riktade sökning på specifika data *strukturer* (målgrupp söka till ett Kundrecensioner fält) alternativa metoder som beskrivs nedan.

## <a name="when-to-use-a-filter"></a>När du ska använda ett filter

Filtren är grundläggande för flera Sök-upplevelser, inklusive ”Sök närheten” fasetterad navigering och säkerhet filtrerar som visar endast de dokument som en användare har behörighet att se. Om du använder någon av dessa upplevelser krävs ett filter. Den är kopplad till frågan som tillhandahåller koordinaterna geolokalisering kategorin aspekten som valts av användaren eller säkerhets-ID för begäranden.

Följande: scenarier

1. Använd filter för att dela upp ditt index baserat på värden i indexet. Du kan skapa ett filter som uttryckligen väljer dokument som uppfyller dina kriterier (i Seattle, condos, Hamnområde) ges ett schema med ort, housing typ och erbjuder. 

  Fulltextsökning med samma indata ger ofta liknande resultat, men ett filter är mer exakta eftersom det krävs en exakt matchning filter har löpt ut mot innehållet i ditt index. 

2. Använd ett filter om sökupplevelsen medföljer ett filter-krav:

 * [Fasetterad navigering](search-faceted-navigation.md) filter används för att skicka tillbaka kategorin aspekten som användaren har valt.
 * GEO-sökningen använder ett filter för att skicka koordinaterna för den aktuella platsen i ”Sök närheten” appar. 
 * Säkerhetsfilter överföra säkerhetsidentifierare som filtervillkor, där en matchning i indexet fungerar som proxy för åtkomsträttigheter till dokumentet.

3. Använd ett filter om du vill sökvillkor på ett numeriskt fält. 

  Numeriska fält är strängfält i dokumentet och kan visas i sökresultaten, men de är inte sökbar (omfattas fulltextsökning) individuellt. Om du behöver urvalskriterier baserat på numeriska data kan du använda ett filter.

### <a name="alternative-methods-for-reducing-scope"></a>Alternativa metoder för att minska omfånget

Om du vill ha en begränsad effekten i sökresultaten är filter inte ditt enda alternativ. Dessa alternativ kan vara en passar bättre, beroende på ditt mål:

 + `searchFields` Frågeparametern heller sökningen till specifika fält. Indexet innehåller olika fält för engelska och spanska beskrivningar, till exempel använda searchFields som mål vilka fält som ska användas för textsökning. 

+ `$select` används för att ange vilka fält som ska inkluderas i en resultatuppsättning, effektivt trimning svaret innan det skickas till det anropande programmet. Den här parametern inte förfina frågan eller minska mängden dokumentet, men om detaljerade svar är målet kan den här parametern är ett alternativ att överväga. 

Mer information om antingen parametern finns [Sök dokument > begär > fråga parametrar](https://docs.microsoft.com/rest/api/searchservice/search-documents#request).


## <a name="filters-in-the-query-pipeline"></a>Filter i pipeline för frågan

Frågan när en filter-parser accepterar villkoren som indata, konverterar uttrycket till atomiska booleska uttryck och skapar ett filterträd utvärderas sedan över filtrera fält i ett index.  

Filtrering inträffar före sökning, kvalificera vilka dokument som ska ingå i nedströms bearbetning för hämtning av dokument och relevans bedömningen. När ihop med en söksträng minskar filtret effektivt ytan på efterföljande search-åtgärd. Om du använder enbart (till exempel när frågesträngen är tom var `search=*`), filtervillkoren är den enda indata. 

## <a name="filter-definition"></a>Filterdefinition

Filter är OData-uttryck, Ledad med hjälp av en [delmängd av OData V4-syntax som stöds i Azure Search](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

Du kan ange ett filter för varje **Sök** åtgärden, men själva filtret kan innehålla flera fält, flera villkor, och om du använder en **ismatch** funktion, flera uttryck. Du kan ange predikat i valfri ordning i flera delar filteruttrycket. Det finns någon märkbar fördel prestanda om du försöker ändra predikat i en viss sekvens.

Hård gräns på ett filteruttryck är den maximala gränsen på begäran. Hela begäran, inklusive filtret, kan innehålla högst 16 MB för POST eller 8 KB för GET. Mjuka gränser stämmer med antalet satser i filteruttrycket. En bra tumregel är att om du har hundratals satser är i fara som körs i gränsen. Vi rekommenderar att du skapar ditt program så att den inte genererar filter obegränsad storlek.

I följande exempel representerar prototypical filterdefinitioner i flera API: er.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2017-11-11

# Option 2: Use filter for POST and pass it in the header
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2017-11-11
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

Följande exempel visar flera designmönster för scenarier med filtret. Fler idéer finns [syntaxen för OData-uttryck > exempel](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#bkmk_examples).

+ Fristående **$filter**, utan en frågesträng användbart när filteruttrycket kan fullständigt kvalificerat dokument av intresse. Utan en frågesträng finns inga lexikala eller språkliga analys, ingen bedömningen och inga rangordning. Observera att strängen är tom.

   ```
   search=*&$filter=(baseRate ge 60 and baseRate lt 300) and accommodation eq 'Hotel' and city eq 'Nogales'
   ```

+ Kombinationen av frågesträngen och **$filter**, där filtret skapas delmängden och frågesträngen innehåller termen indata för textsökning över filtrerade delmängd. Använda ett filter med en frågesträng är de vanligaste kod mönstret.

   ```
   search=hotels ocean$filter=(baseRate ge 60 and baseRate lt 300) and city eq 'Los Angeles'
   ```

+ Sammansatt frågor, avgränsade med ”eller”, var och en med sin egen filtervillkor (till exempel ' beagles' i 'hund ”) eller 'siamese' i 'cat'. ELLER hade uttryck utvärderas individuellt, med svar från var och en kombineras till ett svar som skickas tillbaka till det anropande programmet. Det här designmönstret uppnås genom search.ismatch-funktionen. Du kan använda den icke-bedömningen versionen (search.ismatch) eller den bedömningsprofil (search.ismatchscoring).

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'description') or category eq 'Luxury'
   ```

Följa upp dessa artiklar för vägledningen specifika användningsområden:

+ [Facet-filter](search-filters-facets.md)
+ [Språkfilter](search-filters-language.md)
+ [Säkerhetsoptimering](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Fältet krav för filtrering

I REST-API filtrera är *på* som standard. Filtrera fält öka Indexstorlek. Se till att ange `filterable=FALSE` för fält som du inte planerar att faktiskt används i ett filter. Läs mer om inställningar för fältdefinitioner [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

I .NET-SDK på Filtrera är *av* som standard. API för att ställa in egenskapen Filtrera är [IsFilterable](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). I exemplet nedan, dess uppsättning på BaseRate fältdefinition.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="reindexing-requirements"></a>Indexeringen krav

Om ett fält är icke-filtrera och du vill göra det filtrera, måste du lägga till ett nytt eller återskapa det befintliga fältet. Ändra en fältdefinition av ändrar den fysiska strukturen i indexet. I Azure Search indexeras alla sökvägar för tillåten åtkomst för snabb fråga hastighet, vilket kräver en återskapning av datastrukturer när fältdefinitioner ändras. 

Återskapa enskilda fält kan vara en låg påverkan-åtgärd som kräver en sammanfogning som skickar befintliga Dokumentnyckel och associerade värden till index, medan resten av varje dokument som är kvar. Om du får ett återskapa krav, se följande länkar för anvisningar:

 + [Indexering åtgärder med hjälp av .NET SDK](https://docs.microsoft.com/azure/search/search-import-data-dotnet#decide-which-indexing-action-to-use)
 + [Indexering åtgärder med hjälp av REST-API](https://docs.microsoft.com/azure/search/search-import-data-rest-api#decide-which-indexing-action-to-use)

## <a name="text-filter-fundamentals"></a>Texten filter grunderna

Textfilter är giltiga för strängfält som du vill dra vissa godtycklig samling för dokument baserat på värdena i Sök Kristi.

För Textfilter som består av strängar, finns det ingen lexikala analys eller radbrytning, så att jämförelser för exakt matchar. Anta exempelvis att ett fält *f* innehåller ”skiner” `$filter=f eq 'Sunny'`matchar inte, men `$filter=f eq 'Sunny day'` kommer. 

Textsträngar är skiftlägeskänsliga. Det finns inga nedre skiftläge alltid i övre ord: `$filter=f eq 'Sunny day'` kommer inte att hitta ' skiner ''.


| Metoden | Beskrivning | 
|----------|-------------|
| [Search.in()](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | En funktion som tillhandahåller en kommaavgränsad lista med strängar för ett visst fält. Strängarna utgör filtervillkoren som används på varje fält i omfånget för frågan. <br/><br/>`search.in(f, ‘a, b, c’)` motsvarar semantiskt `f eq ‘a’ or f eq ‘b’ or f eq ‘c’`, förutom att körs snabbare när listan över värden är stor.<br/><br/>Vi rekommenderar den **search.in** fungerar för [säkerhetsfilter](search-security-trimming-for-azure-search.md) och för eventuella filter som består av rådata text som ska matchas på värdena i ett visst fält. Den här metoden är avsedd för hastighet. Du kan förvänta dig subsecond svarstid för hundratals till tusentals värden. När det finns ingen uttrycklig gräns för antalet objekt som du kan skicka till funktionen, ökar svarstiden i förhållande till antalet strängar som du anger. | 
| [Search.ismatch()](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | En funktion som du kan blanda fulltextsökning åtgärder med strikt booleska åtgärder i samma filteruttrycket. Den gör att flera kombinationer av fråga filter i en begäran. Du kan också använda den för en *innehåller* och filtrerar på en partiell sträng inom en större sträng. |  
| [$filter = fältet operatorsträng](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Ett användardefinierat uttryck består av fält, operatorer och värden. | 

## <a name="numeric-filter-fundamentals"></a>Grunderna i numeriska filter

Numeriska fält är inte `searchable` i kontexten för textsökning. Endast strängar regleras fulltextsökning. Till exempel om du anger 99,99 som en sökterm kommer inte du gå tillbaka kostar $99,99 objekt. I stället visas objekt som har många 99 i anslutningssträng-fälten i dokumentet. Om du har numeriska data är antagandet därför att du ska använda dem för filter, inklusive intervall, facets, grupper och så vidare. 

Dokument som innehåller numeriska fält (pris, storlek, SKU, -ID) ange dessa värden i sökresultaten om fältet är markerat `retrievable`. Det här är att textsökning själva inte gäller för numeriska fälttyp.

## <a name="next-steps"></a>Nästa steg

Först och försök **Sök explorer** i portalen för att skicka frågor med **$filter** parametrar. Den [real-egendom-sample index](search-get-started-portal.md) innehåller intressanta resultat för följande filtrerade frågor när du klistra in dem i sökfältet:

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

Om du vill arbeta med fler exempel finns [syntaxen för OData-filtret uttryck > exempel](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#bkmk_examples).

## <a name="see-also"></a>Se också

+ [Hur full textsökning fungerar i Azure Search](search-lucene-query-architecture.md)
+ [Sök dokument REST-API](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [Enkel frågesyntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Lucene-frågesyntax](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Datatyper som stöds](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
