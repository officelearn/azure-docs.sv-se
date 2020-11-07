---
title: Filtrera efter Sök Resultat
titleSuffix: Azure Cognitive Search
description: Filtrera efter användarens säkerhets identitet, språk, Geo-Location eller numeriska värden för att minska Sök resultaten för frågor i Azure Kognitiv sökning, en värdbaserad Sök tjänst i molnet på Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 6d83e5c39f97db49e2cc9b77cc806cff0a1fa6de
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94355992"
---
# <a name="filters-in-azure-cognitive-search"></a>Filter i Azure Kognitiv sökning 

Ett *filter* innehåller kriterier för att välja dokument som används i en Azure kognitiv sökning-fråga. Ofiltrerad sökning innehåller alla dokument i indexet. Ett filter omfångerar en Sök fråga till en delmängd av dokumenten. Ett filter kan till exempel begränsa full texts ökningen till enbart de produkter som har ett specifikt märke eller en viss färg, vid pris punkter över ett visst tröskelvärde.

Vissa Sök upplevelser har filter krav som en del av implementeringen, men du kan använda filter när som helst när du vill begränsa sökningen med hjälp av *värdebaserade* villkor (omfattnings sökning till produkt typ "böcker" för kategorin "icke-fiktion" Publicerad av "Simon & Schuster").

Om målet i stället är riktad sökning efter specifika data *strukturer* (omfattnings sökning i ett kund gransknings fält) finns det alternativa metoder, som beskrivs nedan.

## <a name="when-to-use-a-filter"></a>När du ska använda ett filter

Filter är grundläggande för flera Sök upplevelser, inklusive "hitta nära mig", fasett-navigering och säkerhets filter som endast visar de dokument som en användare får se. Om du implementerar något av dessa upplevelser krävs ett filter. Det är filtret som är kopplat till Sök frågan som tillhandahåller koordinaterna för geolokalisering, den aspekt kategori som användaren har valt eller säkerhets-ID: t för beställaren.

Exempel scenarier är följande:

1. Använd ett filter för att segmentera ditt index baserat på data värden i indexet. Med tanke på ett schema med stad, typ av bostad och bekvämligheterna kan du skapa ett filter för att explicit välja dokument som uppfyller dina kriterier (i Seattle, Condos, Waterfront). 

   Full texts ökning med samma indata ger ofta liknande resultat, men ett filter är mer exakt eftersom det kräver en exakt matchning av filter termen mot innehåll i ditt index. 

2. Använd ett filter om Sök funktionen innehåller ett filter krav:

   * [Fasettisk navigering](search-faceted-navigation.md) använder ett filter för att skicka tillbaka den aspekt kategori som valts av användaren.
   * Geo-search använder ett filter för att skicka koordinater för den aktuella platsen i "hitta nära mig"-appar. 
   * Säkerhets filter skickar säkerhets identifierare som filter villkor, där en matchning i indexet fungerar som en proxy för åtkomst behörighet till dokumentet.

3. Använd ett filter om du vill använda Sök villkor i ett numeriskt fält. 

   Numeriska fält kan hämtas i dokumentet och kan visas i Sök resultat, men de är inte sökbara (undersöknings texts ökning) individuellt. Om du behöver urvals villkor baserat på numeriska data använder du ett filter.

### <a name="alternative-methods-for-reducing-scope"></a>Alternativa metoder för att minska omfattningen

Om du vill ha en begränsad inverkan i dina Sök resultat kan du inte välja filter. De här alternativen kan vara en bättre anpassning, beroende på ditt mål:

 + `searchFields` pegs Sök i vissa fält. Om ditt index t. ex. innehåller separata fält för engelska och spanska beskrivningar, kan du använda searchFields för att ange vilka fält som ska användas för full texts ökning. 

+ `$select` parameter används för att ange vilka fält som ska ingå i en resultat uppsättning, vilket effektivt kan rensa svaret innan det skickas till det anropande programmet. Den här parametern kan inte förfina frågan eller minska dokument samlingen, men om ett mindre svar är ditt mål är den här parametern ett alternativ att tänka på. 

Mer information om någon av parametrarna finns i [Sök efter dokument > begäran > frågeparametrar](/rest/api/searchservice/search-documents#query-parameters).


## <a name="how-filters-are-executed"></a>Hur filter körs

Vid tidpunkten i frågan accepterar en filter tolkare villkor som inmatade, konverterar uttrycket till atomiska booleska uttryck som visas som ett träd och utvärderar filter trädet över filter bara fält i ett index.

Filtrering sker i tandem med sökning och kvalificerar vilka dokument som ska inkluderas i underordnad bearbetning för dokument hämtning och relevans-poäng. När de kombineras med en Sök sträng minskar filtret åter kallelse uppsättningen för efterföljande Sök åtgärder. När den används enskilt (till exempel när frågesträngen är tom där `search=*` ) är filter villkoret den enda ingången. 

## <a name="defining-filters"></a>Definiera filter
Filter är OData-uttryck, som har ledats med en [delmängd av OData v4-syntax som stöds i Azure kognitiv sökning](/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

Du kan ange ett filter för varje **Sök** åtgärd, men själva filtret kan innehålla flera fält, flera kriterier och om du använder en **ismatch** -funktion, flera full texts öknings uttryck. I ett filter uttryck med flera delar kan du ange predikat i valfri ordning (enligt reglerna för Operator prioritet). Det finns ingen märkbar vinst i prestanda om du försöker arrangera om predikat i en viss sekvens.

En av gränserna för ett filter uttryck är den maximala storleks gränsen för begäran. Hela förfrågan, inklusive filtret, kan vara högst 16 MB för POST eller 8 KB för GET. Det finns också en gräns för antalet satser i filter uttrycket. En lämplig tumregel är att om du har hundratals satser kan du vara utsatt för att kunna köra gränsen. Vi rekommenderar att du utformar ditt program på ett sådant sätt att det inte genererar filter av obegränsad storlek.

Följande exempel representerar prototyp filter definitioner i flera API: er.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?api-version=2020-06-30&search=*&$filter=Rooms/any(room: room/BaseRate lt 150.0)&$select=HotelId, HotelName, Rooms/Description, Rooms/BaseRate

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2020-06-30
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

## <a name="filter-usage-patterns"></a>Filtrera användnings mönster

I följande exempel visas flera användnings mönster för filter scenarier. Fler idéer finns i [syntax för OData-uttryck > exempel](./search-query-odata-filter.md#examples).

+ Fristående **$filter** , utan en frågesträng, användbart när filter uttrycket fullständigt kvalificerar dokument av intresse. Utan en frågesträng finns det ingen lexikalisk eller språklig analys, ingen poäng och ingen rangordning. Observera att Sök strängen bara är en asterisk, vilket innebär "matcha alla dokument".

   ```
   search=*&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Honolulu'
   ```

+ En kombination av frågesträng och **$filter** , där filtret skapar delmängd och frågesträngen innehåller term inmatningar för full texts ökning över den filtrerade del mängden. Genom att lägga till villkor (promenads avstånds biograf) införs Sök resultat i resultaten, där dokument som bäst matchar villkoren rangordnas högre. Att använda ett filter med en frågesträng är det vanligaste användnings mönstret.

   ```
  search=walking distance theaters&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Seattle'&$count=true
   ```

+ Sammansatta frågor, separerade med "eller", var och en med sina egna filter villkor (till exempel "Beagles" i "hund" eller "Siamese" i "katt"). Uttryck som kombineras med `or` utvärderas individuellt, med en union av dokument som matchar varje uttryck som skickas tillbaka i svaret. Det här användnings mönstret uppnås via `search.ismatchscoring` funktionen. Du kan också använda den icke-bedömnings versionen `search.ismatch` .

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'Description') or Category eq 'Luxury'&$count=true
   ```

  Det är också möjligt att kombinera full texts ökning via `search.ismatchscoring` med filter som använder `and` i stället för `or` , men det är detsamma som att använda `search` `$filter` parametrarna och i en Sök förfrågan. Följande två frågor ger till exempel samma resultat:

  ```
  $filter=search.ismatchscoring('pool') and Rating ge 4

  search=pool&$filter=Rating ge 4
  ```

Följ upp med de här artiklarna för utförlig vägledning om speciella användnings fall:

+ [Facet-filter](search-filters-facets.md)
+ [Språkfilter](search-filters-language.md)
+ [Säkerhetsoptimering](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Fält krav för filtrering

I REST API är filtrerings bara *aktiverat* som standard för enkla fält. Filter bara fält ökar index storleken. var noga med att ange `"filterable": false` för fält som du inte planerar att använda i ett filter. Mer information om inställningar för fält definitioner finns i [skapa index](/rest/api/searchservice/create-index).

I .NET SDK är filtrerings funktionen *avstängd* som standard. Du kan göra ett fält filter bara genom att ange [egenskapen IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) för motsvarande [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) -objekt till `true` . I exemplet nedan anges attributet i `BaseRate` egenskapen för en modell klass som mappar till index definitionen.

```csharp
[IsFilterable, IsSortable, IsFacetable]
public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Göra ett befintligt fält filter bara

Du kan inte ändra befintliga fält så att de filtreras. I stället måste du lägga till ett nytt fält eller återskapa indexet. Mer information om hur du återskapar ett index eller fyller i fält finns i [så här återskapar du ett Azure kognitiv sökning-index](search-howto-reindex.md).

## <a name="text-filter-fundamentals"></a>Grundläggande text filter

Text filter matchar sträng fält mot literala strängar som du anger i filtret. Till skillnad från full texts ökning finns det ingen lexikal analys eller ord brytning för text filter, så jämförelser är enbart för exakta matchningar. Anta till exempel att ett fält med *f* innehåller "solig Day", `$filter=f eq 'Sunny'` inte matchar, men `$filter=f eq 'sunny day'` kommer att. 

Text strängar är Skift läges känsliga. Det finns inget lägre Skift läge för de övre bokstäver orden: `$filter=f eq 'Sunny day'` "solig Day" hittas inte.

### <a name="approaches-for-filtering-on-text"></a>Metoder för filtrering av text

| Metod | Description | När du ska använda detta |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | En funktion som matchar ett fält mot en avgränsad lista med strängar. | Rekommenderas för [säkerhets filter](search-security-trimming-for-azure-search.md) och för alla filter där många rå text värden måste matchas med ett sträng fält. Funktionen **search.in** är utformad för snabbhet och är mycket snabbare än att explicit jämföra fältet mot varje sträng med `eq` och `or` . | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | En funktion som gör att du kan blanda full texts öknings åtgärder med strikta booleska filter åtgärder i samma filter uttryck. | Använd **search. ismatch** (eller dess bedömnings motsvarighet, **search. ismatchscoring** ) när du vill ha flera Sök filter kombinationer i en begäran. Du kan också använda det för a *innehåller* filter för att filtrera på en delvis sträng inom en större sträng. |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | Ett användardefinierat uttryck bestående av fält, operatorer och värden. | Använd det här när du vill hitta exakta matchningar mellan ett sträng fält och ett sträng värde. |

## <a name="numeric-filter-fundamentals"></a>Numeriska filter grunderna

Numeriska fält ingår inte `searchable` i kontexten för full texts ökning. Endast strängar omfattas av full texts ökning. Om du till exempel anger 99,99 som Sök villkor får du inte tillbaka dina objekt pris till $99,99. I stället visas objekt med nummer 99 i sträng fält i dokumentet. Om du däremot har numeriska data, är det att du kommer att använda dem för filter, inklusive intervall, ansikte, grupper och så vidare. 

Dokument som innehåller numeriska fält (pris, storlek, SKU, ID) tillhandahåller dessa värden i Sök resultat om fältet är markerat `retrievable` . Punkten här är att ingen fullständig texts ökning är tillämplig på numeriska fält typer.

## <a name="next-steps"></a>Nästa steg

Prova först att **söka i Utforskaren** i portalen för att skicka frågor med **$filter** parametrar. [Indexet för fastighets-och-exempel](search-get-started-portal.md) ger intressanta resultat för följande filtrerade frågor när du klistrar in dem i Sök fältet:

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

Mer information om hur du arbetar med fler exempel finns i [syntax för OData filter Expression > exempel](./search-query-odata-filter.md#examples).

## <a name="see-also"></a>Se även

+ [Så här fungerar fulltextsökning i Azure Cognitive Search](search-lucene-query-architecture.md)
+ [REST API för dokumentsökning](/rest/api/searchservice/search-documents)
+ [Enkel frågesyntax](/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Lucene-frågesyntax](/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Datatyper som stöds](/rest/api/searchservice/supported-data-types)