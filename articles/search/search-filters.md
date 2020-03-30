---
title: Filtrera på sökresultat
titleSuffix: Azure Cognitive Search
description: Filtrera efter användarsäkerhetsidentitet, språk, geografisk plats eller numeriska värden för att minska sökresultaten på frågor i Azure Cognitive Search, en värdbaserad molnsöktjänst på Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 03333e853a2ab7606ebe60cc3f68bcb5facfbdb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191007"
---
# <a name="filters-in-azure-cognitive-search"></a>Filter i Azure Cognitive Search 

Ett *filter* innehåller villkor för att välja dokument som används i en Azure Cognitive Search-fråga. Ofiltrerad sökning innehåller alla dokument i indexet. Ett filter scopes en sökfråga till en delmängd av dokument. Ett filter kan till exempel begränsa fulltextsökningen till just de produkter som har ett visst märke eller en viss färg, till prispunkter över ett visst tröskelvärde.

Vissa sökupplevelser ställer filterkrav som en del av implementeringen, men du kan använda filter när du vill begränsa sökningen med hjälp av *värdebaserade* kriterier (omfångssökning av sök till produkttyp "böcker" för kategorin "facklitteratur" publicerad av "Simon & Schuster").

Om ditt mål i stället är riktad sökning på specifika *datastrukturer* (omfångssökning till ett kundgranskningsfält) finns det alternativa metoder som beskrivs nedan.

## <a name="when-to-use-a-filter"></a>När ska ett filter användas

Filter är grundläggande för flera sökupplevelser, inklusive "hitta nära mig", fasterad navigering och säkerhetsfilter som visar endast de dokument som en användare får se. Om du implementerar någon av dessa upplevelser krävs ett filter. Det är filtret som är kopplat till sökfrågan som tillhandahåller geolokaliseringskoordinaterna, aspektkategorin som valts av användaren eller frågeställarens säkerhets-ID.

Exempel på scenarier är följande:

1. Använd ett filter för att segmentera indexet baserat på datavärden i indexet. Med tanke på ett schema med stad, bostäder typ och bekvämligheter, kan du skapa ett filter för att uttryckligen välja dokument som uppfyller dina kriterier (i Seattle, lägenheter, vattnet). 

   Fulltextsökning med samma indata ger ofta liknande resultat, men ett filter är mer exakt eftersom det kräver en exakt matchning av filtertermen mot innehållet i indexet. 

2. Använd ett filter om sökupplevelsen levereras med ett filterkrav:

   * [Fasterad navigering](search-faceted-navigation.md) använder ett filter för att skicka tillbaka den aspektkategori som användaren har valt.
   * Geo-search använder ett filter för att skicka koordinater för den aktuella platsen i "hitta nära mig"-appar. 
   * Säkerhetsfilter skickar säkerhetsidentifierare som filtervillkor, där en matchning i indexet fungerar som en proxy för åtkomsträttigheter till dokumentet.

3. Använd ett filter om du vill ha sökvillkor i ett numeriskt fält. 

   Numeriska fält kan hämtas i dokumentet och kan visas i sökresultaten, men de är inte sökbara (med förbehåll för fulltextsökning) individuellt. Om du behöver urvalskriterier baserat på numeriska data använder du ett filter.

### <a name="alternative-methods-for-reducing-scope"></a>Alternativa metoder för att minska omfattningen

Om du vill ha en förträngningseffekt i sökresultaten är filter inte ditt enda val. Dessa alternativ kan passa bättre, beroende på ditt mål:

 + `searchFields`frågeparameterpinnar söker efter specifika fält. Om indexet till exempel innehåller separata fält för engelska och spanska beskrivningar kan du använda sökfält för att rikta in dig på vilka fält som ska användas för fulltextsökning. 

+ `$select`parametern används för att ange vilka fält som ska inkluderas i en resultatuppsättning, vilket effektivt trimmar svaret innan det skickas till det anropande programmet. Den här parametern förfinar inte frågan eller minskar dokumentsamlingen, men om ett mindre svar är ditt mål är den här parametern ett alternativ att överväga. 

Mer information om någon av parametrarna finns i [Sök dokument > Begäran > Frågeparametrar](/rest/api/searchservice/search-documents#query-parameters).


## <a name="how-filters-are-executed"></a>Så här utförs filter

Vid frågetid accepterar en filtertolkare villkor som indata, konverterar uttrycket till atomära booleska uttryck som representeras som ett träd och utvärderar sedan filterträdet över filterbara fält i ett index.

Filtrering sker tillsammans med sökning, kvalificera vilka dokument som ska inkluderas i nedströmsbearbetning för dokumenthämtning och relevansbedömning. När filtret parkopplas med en söksträng minskar filtret effektivt återkallelseuppsättningen för den efterföljande sökåtgärden. När det används ensamt (till exempel `search=*`när frågesträngen är tom där) är filtervillkoren den enda indata. 

## <a name="defining-filters"></a>Definiera filter
Filter är OData-uttryck, artikulerade med hjälp av en [delmängd av OData V4-syntax som stöds i Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

Du kan ange ett filter för varje **sökåtgärd,** men själva filtret kan innehålla flera fält, flera villkor och om du använder en **ismatch-funktion,** flera fulltextsökuttryck. I ett filteruttryck i flera delar kan du ange predikater i valfri ordning (i enlighet med reglerna för operatorprioritet). Det finns ingen märkbar vinst i prestanda om du försöker ordna predikat i en viss sekvens.

En av gränserna för ett filteruttryck är den maximala storleksgränsen för begäran. Hela begäran, inklusive filtret, kan vara högst 16 MB för POST eller 8 KB för GET. Det finns också en gräns för antalet satser i filteruttrycket. En bra tumregel är att om du har hundratals klausuler, är du riskerar att köra in i gränsen. Vi rekommenderar att du utformar ditt program på ett sådant sätt att det inte genererar filter av obegränsad storlek.

Följande exempel representerar prototypiska filterdefinitioner i flera API:er.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?api-version=2019-05-06&search=*&$filter=Rooms/any(room: room/BaseRate lt 150.0)&$select=HotelId, HotelName, Rooms/Description, Rooms/BaseRate

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2019-05-06
{
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate lt 150.0)",
    "select": "HotelId, HotelName, Rooms/Description, Rooms/BaseRate"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(room: room/BaseRate lt 150.0)",
            Select = new[] { "HotelId", "HotelName", "Rooms/Description" ,"Rooms/BaseRate"}
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>Filtrera användningsmönster

Följande exempel illustrerar flera användningsmönster för filterscenarier. Fler idéer finns i [OData-uttryckssyntax > Exempel](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples).

+ Fristående **$filter**, utan en frågesträng, användbart när filteruttrycket kan kvalificera dokument av intresse fullt ut. Utan en frågesträng finns det ingen lexikal eller språklig analys, ingen bedömning och ingen rangordning. Observera att söksträngen bara är en asterisk, vilket betyder "matcha alla dokument".

   ```
   search=*&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Honolulu'
   ```

+ Kombination av frågesträng och **$filter**, där filtret skapar delmängden, och frågesträngen innehåller termen indata för fulltextsökning över den filtrerade delmängden. Tillägget av termer (gångavstånd teatrar) introducerar sökresultat i resultaten, där dokument som bäst matchar villkoren rankas högre. Att använda ett filter med en frågesträng är det vanligaste användningsmönstret.

   ```
  search=walking distance theaters&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Seattle'&$count=true
   ```

+ Sammansatta frågor, åtskilda av "eller", var och en med sina egna filterkriterier (t.ex. "beagles" i "hund" eller "siames" i "katt"). Uttryck i `or` kombination med utvärderas individuellt, med en union av dokument som matchar varje uttryck skickas tillbaka i svaret. Detta användningsmönster uppnås `search.ismatchscoring` genom funktionen. Du kan också använda den `search.ismatch`version som inte gör mål.

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'Description') or Category eq 'Luxury'&$count=true
   ```

  Det är också möjligt att kombinera `search.ismatchscoring` fulltextsökning via med filter `and` som använder istället för `or`, men detta är funktionellt likvärdigt med att använda `search` och `$filter` parametrar i en sökbegäran. Följande två frågor ger till exempel samma resultat:

  ```
  $filter=search.ismatchscoring('pool') and Rating ge 4

  search=pool&$filter=Rating ge 4
  ```

Följ upp med dessa artiklar för omfattande vägledning om specifika användningsfall:

+ [Facet-filter](search-filters-facets.md)
+ [Språkfilter](search-filters-language.md)
+ [Säkerhetsoptimering](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Fältkrav för filtrering

I REST API är filterbar som standard *aktiverat* för enkla fält. Filterbara fält ökar indexstorleken. Se till `"filterable": false` att ange för fält som du inte planerar att använda i ett filter. Mer information om inställningar för fältdefinitioner finns i [Skapa index](https://docs.microsoft.com/rest/api/searchservice/create-index).

I .NET SDK är det filterbara *inaktiverat* som standard. Du kan göra ett fält filterbart genom att ange [egenskapen IsFilterable](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet) för motsvarande [fältobjekt](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field?view=azure-dotnet) på `true`. Du kan också göra detta deklarativt med attributet [IsFilterable](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). I exemplet nedan anges attributet `BaseRate` på egenskapen för en modellklass som mappar till indexdefinitionen.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Göra ett befintligt fält filterbart

Du kan inte ändra befintliga fält så att de kan filtreras. I stället måste du lägga till ett nytt fält eller återskapa indexet. Mer information om hur du återskapar ett index eller kontrollerar om fält finns i [Så här återskapar du ett Azure Cognitive Search-index](search-howto-reindex.md).

## <a name="text-filter-fundamentals"></a>Grunderna i textfilter

Textfilter matchar strängfält mot bokstavliga strängar som du anger i filtret. Till skillnad från fulltextsökning finns det ingen lexikal analys eller ordbrytning för textfilter, så jämförelser är endast för exakta matchningar. Anta till exempel att ett fält *f* `$filter=f eq 'Sunny'` innehåller "solig `$filter=f eq 'sunny day'` dag", inte matchar, men kommer. 

Textsträngar är skiftlägeskänsliga. Det finns ingen nedre hölje av versaler ord: `$filter=f eq 'Sunny day'` kommer inte att hitta "solig dag".

### <a name="approaches-for-filtering-on-text"></a>Metoder för filtrering på text

| Metod | Beskrivning | När du ska använda detta |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | En funktion som matchar ett fält mot en avgränsad lista med strängar. | Rekommenderas för [säkerhetsfilter](search-security-trimming-for-azure-search.md) och för alla filter där många råtextvärden måste matchas med ett strängfält. Funktionen **search.in** är utformad för hastighet och är mycket snabbare än `eq` att `or`uttryckligen jämföra fältet mot varje sträng med och . | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | En funktion som gör att du kan blanda fulltextsökningsåtgärder med strikt booleska filteråtgärder i samma filteruttryck. | Använd **search.ismatch** (eller dess poängmotsvarighet, **search.ismatchscoring)** när du vill ha flera kombinationer av sökfilter i en begäran. Du kan också använda den för ett *innehållsfilter* för att filtrera på en partiell sträng i en större sträng. |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | Ett användardefinierat uttryck som består av fält, operatorer och värden. | Använd detta när du vill hitta exakta matchningar mellan ett strängfält och ett strängvärde. |

## <a name="numeric-filter-fundamentals"></a>Grundläggande om numeriskt filter

Numeriska fält `searchable` är inte i samband med fulltextsökning. Endast strängar är föremål för fulltextsökning. Om du till exempel anger 99,99 som sökterm får du inte tillbaka artiklar som prissätts till 99,99 USD. I stället visas objekt som har siffran 99 i strängfält i dokumentet. Om du har numeriska data är antagandet att du kommer att använda dem för filter, inklusive intervall, faser, grupper och så vidare. 

Dokument som innehåller numeriska fält (pris, storlek, SKU, ID) anger `retrievable`dessa värden i sökresultaten om fältet är markerat . Poängen här är att själva fulltextsökningen inte är tillämplig på numeriska fälttyper.

## <a name="next-steps"></a>Nästa steg

Försök först **söka utforskare** i portalen för att skicka frågor med **$filter** parametrar. [Fastighetsmäklare-exempelindex ger](search-get-started-portal.md) intressanta resultat för följande filtrerade frågor när du klistrar in dem i sökfältet:

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

Mer information om hur du arbetar med fler exempel finns i [OData filteruttryckssyntax > exempel](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples).

## <a name="see-also"></a>Se även

+ [Så här fungerar fulltextsökning i Azure Cognitive Search](search-lucene-query-architecture.md)
+ [REST API för dokumentsökning](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [Enkel frågesyntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Lucene-frågesyntax](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Datatyper som stöds](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
