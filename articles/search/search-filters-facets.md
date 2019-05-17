---
title: Facet-filter för söknavigering i appar – Azure Search
description: Filtrera efter användaridentitet för säkerhet, geografiska plats eller numeriska värden att minska sökresultat på frågor i Azure Search, en värdbaserad molnsöktjänst på Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 5/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 8dffc5b87aefe23953d3a74f1d96b5ee03e0315d
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597389"
---
# <a name="how-to-build-a-facet-filter-in-azure-search"></a>Hur du skapar ett facet-filter i Azure Search 

Aspektbaserad navigering används för automatiskt dirigerad filtrering på frågeresultaten i en app, där ditt program erbjuder UI-kontroller för gemensam sökning till grupper av dokument (till exempel kategorier eller varumärken) och Azure Search innehåller datastruktur för säkerhetskopiering i upplevelse. I den här artikeln du snabbt gå igenom de grundläggande stegen för att skapa en aspektbaserad navigeringsstruktur som backar upp sökupplevelsen som du vill ge. 

> [!div class="checklist"]
> * Välj fält för filtrering och fasettering
> * Ange attribut i fältet
> * Skapa index och läsa in data
> * Lägg till facet-filter till en fråga
> * Hantera resultat

Fasetter är dynamiska och returnerade på en fråga. Sök efter svar ta med sig aspektkategorier som används för att gå till resultaten. Om du inte är bekant med fasetterna är i följande exempel en illustration av en struktur för aspektbaserad navigering.

  ![](./media/search-filters-facets/facet-nav.png)

Nya för aspektbaserad navigering och vill ha mer information? Se [implementera aspektbaserad navigering i Azure Search](search-faceted-navigation.md).

## <a name="choose-fields"></a>Välj fält

Fasetter kan beräknas över enskilt värdefält samt samlingar. Fält som fungerar bäst i aspektbaserad navigering har låg kardinalitet: ett litet antal distinkta värden som upprepas under hela dokument i din sökkorpus (till exempel en lista över färger, länder/regioner eller varumärken). 

Fasettering är aktiverat på basis av fält i taget när du skapar indexet genom att ange den `facetable` attributet `true`. Bör Allmänt också ange den `filterable` attributet `true` för, till exempel fält så att ditt sökprogram kan filtrera på dessa fält baserat på fasetterna som användaren väljer. 

När du skapar ett index med hjälp av REST API, valfri [fälttyp](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) som eventuellt skulle kunna användas i aspektbaserad navigering är markerad som `facetable` som standard:

+ `Edm.String`
+ `Edm.DateTimeOffset`
+ `Edm.Boolean`
+ Numeriskt fälttyper: `Edm.Int32`, `Edm.Int64`, `Edm.Double`
+ Samlingar av ovanstående typer (till exempel `Collection(Edm.String)` eller `Collection(Edm.Double)`)

Du kan inte använda `Edm.GeographyPoint` eller `Collection(Edm.GeographyPoint)` fält i aspektbaserad navigering. Fasetter fungerar bäst på fält med låg kardinalitet. Det är ovanligt att alla två uppsättningar koordinater är lika med i den angivna datauppsättningen på grund av av lösningen på geo-koordinater. Fasetter har därför inte stöd för geo-koordinater. Du behöver ett fält för ort eller region i aspekten efter plats.

## <a name="set-attributes"></a>Ange attribut

Indexattribut som styr hur ett fält används läggs till enskilda fältdefinitioner i indexet. I följande exempel fält med låg kardinalitet, användbara för aspekter, som består av: `category` (hotell, motel, hostel), `tags`, och `rating`. Dessa fält har den `filterable` och `facetable` attribut set uttryckligen i följande exempel som illustration. 

> [!Tip]
> Som bästa praxis för prestanda och lagringsoptimering, inaktivera fasettering för fält som ska aldrig användas som ett fasettvärde. I synnerhet strängfält för unika värden, till exempel ett ID eller produkt-namn ska vara inställd på `"facetable": false` att förhindra användningen oavsiktlig (och ineffektiv) i aspektbaserad navigering.


```json
{
  "name": "hotels",  
  "fields": [
    { "name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false },
    { "name": "baseRate", "type": "Edm.Double" },
    { "name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
    { "name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "hotelName", "type": "Edm.String", "facetable": false },
    { "name": "category", "type": "Edm.String", "filterable": true, "facetable": true },
    { "name": "tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true },
    { "name": "parkingIncluded", "type": "Edm.Boolean",  "filterable": true, "facetable": true, "sortable": false },
    { "name": "smokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true, "sortable": false },
    { "name": "lastRenovationDate", "type": "Edm.DateTimeOffset" },
    { "name": "rating", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "location", "type": "Edm.GeographyPoint" }
  ]
}
```

> [!Note]
> Den här indexdefinitionen kopieras från [skapa ett Azure Search-index med REST API](https://docs.microsoft.com/azure/search/search-create-index-rest-api). Det är identiska förutom ytlig skillnader i fältdefinitioner. Den `filterable` och `facetable` attribut läggs till explicit på `category`, `tags`, `parkingIncluded`, `smokingAllowed`, och `rating` fält. I praktiken `filterable` och `facetable` aktiveras som standard på dessa fält när du använder REST-API. När du använder .NET SDK, måste dessa attribut aktiveras explicit.

## <a name="build-and-load-an-index"></a>Skapa och läsa in ett index

Ett mellanliggande (och kanske uppenbara) steg är att du behöver [skapa och Fyll i indexet](https://docs.microsoft.com/azure/search/search-create-index-dotnet#3---construct-index) innan utformningen av en fråga. Vi nämna det här steget för fullständighetens skull. Ett sätt att avgöra om indexet är tillgänglig är genom att kontrollera listan den [portal](https://portal.azure.com).

## <a name="add-facet-filters-to-a-query"></a>Lägg till facet-filter till en fråga

I programkoden, skapar du en fråga som anger alla delar av en giltig fråga, inklusive sökuttryck, fasetter, filter, bedömning profiler – allt används för att formulera en begäran. I följande exempel skapas en begäran som skapar aspekten navigering beroende på vilken typ av logi, klassificering och andra bekvämligheterna.

```csharp
var sp = new SearchParameters()
{
    ...
    // Add facets
    Facets = new[] { "category", "rating", "parkingIncluded", "smokingAllowed" }.ToList()
};
```

### <a name="return-filtered-results-on-click-events"></a>Returnera filtrerade resultat på klickar du på händelser

När användaren klickar på ett fasettvärde, bör hanteraren för click-händelse använda ett filteruttryck för att upptäcka användarens avsikt. Får en `category` aspekten, klicka på kategorin ”motel” implementeras med en `$filter` som väljer boende av den typen. När en användare klickar på ”motel” för att indikera att endast motell ska visas i nästa fråga som programmet skickar innehåller `$filter=category eq 'motel'`.

Följande kodavsnitt lägger till kategorin filtret om användaren väljer ett värde från kategori-aspekten.

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

Om användaren klickar på ett fasettvärde för en samling fält som `tags`, till exempel värdet ”pool” ditt program bör använda följande filter syntax: `$filter=tags/any(t: t eq 'pool')`

## <a name="tips-and-workarounds"></a>Tips och tillfälliga lösningar

### <a name="initialize-a-page-with-facets-in-place"></a>Initiera en sida med fasetter på plats

Om du vill initiera en sida med fasetter på plats kan du skicka en fråga som en del av initieringen av sidan att seeda sida med en inledande aspekten struktur.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Bevara en struktur för aspektbaserad navigering asynkront av filtrerade resultat

En av utmaningarna med aspekten navigering i Azure Search är att det finns fasetterna för aktuella resultaten. I praktiken är det vanligt att behålla en statisk uppsättning fasetter så att användaren kan navigera bakåt följa tillbaka steg för att utforska alternativa vägar via Sök efter innehåll. 

Även om det här är ett vanligt användningsfall, är det inte något struktur för aspektbaserad navigering innehåller för närvarande out-of the box. Utvecklare som vill statiska fasetter vanligtvis runt begränsningen genom att utfärda två filtrerade frågor: en begränsad till resultatet, den andra används för att skapa en statisk lista över fasetterna för navigering.

## <a name="see-also"></a>Se också

+ [Filter i Azure Search](search-filters.md)
+ [Skapa Index REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [Söka efter dokument REST-API](https://docs.microsoft.com/rest/api/searchservice/search-documents)
