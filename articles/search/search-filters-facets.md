---
title: Facet-filter för söknavigering i appar – Azure Search
description: Filtrera efter användaridentitet för säkerhet, geografiska plats eller numeriska värden att minska sökresultat på frågor i Azure Search, en värdbaserad molnsöktjänst på Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 10/13/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: ec87bdadc0e7f77cdeebb16403758026fd956c30
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939862"
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

Fasettering är aktiverat på basis av fält i taget när du skapar index, genom att ange följande attribut till TRUE: `filterable`, `facetable`. Endast filtrerbara fält kan fasetteras.

Alla [fälttyp](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) som eventuellt skulle kunna användas i aspektbaserad navigering har markerats som ”fasettbar”:

+ Edm.String
+ Edm.DateTimeOffset
+ Edm.Boolean
+ Edm.Collections
+ Numeriskt fälttyper: Edm.Int32, Edm.Int64, Edm.Double

Du kan inte använda Edm.GeographyPoint i aspektbaserad navigering. Fasetter skapas från läsbar text eller siffror. Fasetter har därför inte stöd för geo-koordinater. Du behöver ett fält för ort eller region i aspekten efter plats.

## <a name="set-attributes"></a>Ange attribut

Indexattribut som styr hur ett fält används läggs till enskilda fältdefinitioner i indexet. I följande exempel fält med låg kardinalitet, användbara för aspekter, som består av: kategori (hotell, motel, hostel), bekvämligheterna och betyg. 

I .NET-API har attribut som filtrerande anges uttryckligen. Fasettering och filtrering är aktiverat som standard, vilket innebär att du behöver bara att uttryckligen ange attribut när du vill inaktivera dem i REST-API. Även om du inte behöver tekniskt, visar vi uppgift i exemplet nedan REST för instruktioner. 

> [!Tip]
> Som bästa praxis för prestanda och lagringsoptimering, inaktivera fasettering för fält som ska aldrig användas som ett fasettvärde. I synnerhet strängfält för singleton-värden, till exempel ett ID eller produkt-namn ska vara inställd på ”Fasettbar”: false för att förhindra att deras oavsiktliga (och ineffektiv) använda i aspektbaserad navigering.


```http
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String", "filterable": true, "facetable": true},
        {"name": "tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true},
        {"name": "parkingIncluded", "type": "Edm.Boolean",  "filterable": true, "facetable": true, "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true, "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32", "filterable": true, "facetable": true},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

> [!Note]
> Den här indexdefinitionen kopieras från [skapa ett Azure Search-index med REST API](https://docs.microsoft.com/azure/search/search-create-index-rest-api). Det är identiska förutom ytlig skillnader i fältdefinitioner. Attribut för Filtrerbart och fasettbart fält läggs till explicit på kategori, taggar, parkingIncluded, smokingAllowed och klassificering fält. Du får Filtrerbart och fasettbart fält för ledig på Edm.String och Edm.Boolean Edm.Int32 fälttyper i praktiken. 

## <a name="build-and-load-an-index"></a>Skapa och läsa in ett index

Ett mellanliggande (och kanske uppenbara) steg är att du behöver [skapa och Fyll i indexet](https://docs.microsoft.com/azure/search/search-create-index-dotnet#3---construct-index) innan utformningen av en fråga. Vi nämna det här steget för fullständighetens skull. Ett sätt att avgöra om indexet är tillgänglig är genom att kontrollera listan den [portal](https://portal.azure.com).

## <a name="add-facet-filters-to-a-query"></a>Lägg till facet-filter till en fråga

I programkoden, skapar du en fråga som anger alla delar av en giltig fråga, inklusive sökuttryck, fasetter, filter, bedömning profiler – allt används för att formulera en begäran. I följande exempel skapas en begäran som skapar aspekten navigering beroende på vilken typ av logi, klassificering och andra bekvämligheterna.

```csharp
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "category", "rating", "parkingIncluded", "smokingAllowed" },
};
```

### <a name="return-filtered-results-on-click-events"></a>Returnera filtrerade resultat på klickar du på händelser

Filteruttrycket hanterar click-händelse på aspektvärdet. Klicka på kategorin ”motel” med en kategori aspekten kan implementeras via en `$filter` som väljer boende av den typen. När en användare klickar på ”motell” för att indikera att endast motell ska visas i nästa fråga som programmet skickar innehåller $filter = kategori eq 'motell'.

Följande kodavsnitt lägger till kategorin filtret om användaren väljer ett värde från kategori-aspekten.

```csharp
if (categoryFacet != "")
  filter = "category eq '" + categoryFacet + "'";
```
Med hjälp av REST-API, begäran skulle vara uppvisat som `$filter=category eq 'c1'`. Om du vill göra en flervärdesfält för kategori, använder du följande syntax: `$filter=category/any(c: c eq 'c1')`

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
