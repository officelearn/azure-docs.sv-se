---
title: "Begränsningsaspekten filter i Azure Search | Microsoft Docs"
description: "Filtervillkor av användaridentitet för säkerhet, språk, geografiska plats eller numeriska värden att minska sökresultat på frågorna i Azure Search värdbaserade moln search-tjänsten på Microsoft Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
ms.assetid: 
ms.service: search
ms.devlang: 
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/13/2017
ms.author: heidist
ms.openlocfilehash: 5b4d88cb9c9662fe45de8c11534232a2905cf5a4
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="how-to-build-a-facet-filter-in-azure-search"></a>Hur du skapar ett filter för aspekten i Azure Search 

Fasetterad navigering används för automatisk dirigerad filtrering på resultatet av frågan i en sökning-app, där programmet ger UI-kontroller för målgrupp sökning till grupper av dokument (till exempel kategorier eller märken) och Azure Search innehåller datastrukturen tillbaka den upplevelse. Granska de grundläggande stegen för att skapa en navigeringsstruktur för fasetterad säkerhetskopierar sökinställningar som du vill ge snabbt i den här artikeln. 

> [!div class="checklist"]
> * Välj fält för att filtrera och faceting
> * Ange attribut i fältet
> * Skapa index och Läs in data
> * Lägga till filter för aspekten för en fråga
> * Hantera resultat

Facets är dynamiska och returnerade på en fråga. Sökningssvar ta med sig aspekten kategorier som används för att gå till resultaten. Om du inte är bekant med facets är följande exempel en illustration av en aspekten navigeringsstruktur.

  ![](./media/search-filters/facet-nav.png)

Ny till fasetterad navigering och vill mer information? Se [implementera fasetterad navigering i Azure Search](search-faceted-navigation.md).

## <a name="choose-fields"></a>Välj fält

Facets kan beräknas över enskilt värde samt som samlingar. Fält som fungerar bäst i fasetterad navigeringen har låg kardinalitet: ett litet antal distinkta värden som upprepas under hela dokument i din sökning Kristi (till exempel en lista över färger, land eller märken). 

Faceting är aktiverat på grund av fältet när du skapar index, genom att ange följande attribut till TRUE: `filterable`, `facetable`. Endast filtrerbara fält kan fasetteras.

Alla [fälttyp](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) som eventuellt kan användas i fasetterad navigering har markerats som ”facetable”:

+ Edm.String
+ Edm.DateTimeOffset
+ Edm.Boolean
+ Edm.Collections (se [så aspekten komplexa datatyper](#facet-complex-fields) senare i den här artikeln.)
+ Numeriska fälttyp: Edm.Int32, Edm.Int64, Edm.Double

Du kan inte använda Edm.GeographyPoint i fasetterad navigeringsfältet. Facets konstrueras från läsbar text eller siffror. Därför stöds facets inte för geo-koordinater. Du behöver en stad eller region fältet till aspekten per plats.

## <a name="set-attributes"></a>Ange attribut

Index-attribut som styr hur ett fält används läggs till enskilda fältdefinitioner i indexet. I följande exempel fält med låg kardinalitet användbart för faceting, som består av: kategori (hotell, översikt, hostel), erbjuder eller klassificeringar. 

Filtrera attribut måste anges explicit i .NET-API. Faceting och filtrering är aktiverade som standard, vilket innebär att du bara behöver att explicit ange attribut när du vill inaktivera dem i REST-API. Även om det inte är tekniskt nödvändigt kan visar vi uppgift i följande exempel REST i instruktions syfte. 

> [!Tip]
> Som bästa praxis för prestanda och lagringsoptimering, inaktivera faceting för fält som ska aldrig användas som en säkerhetsåtgärd. I synnerhet strängfält för singleton-värden, till exempel ett ID eller produkt-namn ska vara inställd på ”Facetable”: false för att förhindra att deras oavsiktliga (och ineffektiv) används i fasetterad navigeringsfältet.


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
> Den här indexdefinitionen kopieras från [skapa ett Azure Search-index med hjälp av REST-API](https://docs.microsoft.com/azure/search/search-create-index-rest-api). Det är identiska förutom ytliga skillnader i fältdefinitioner. Filtrera och facetable attributen läggs uttryckligen på kategorin taggar, parkingIncluded, smokingAllowed och klassificering av fält. I praktiken kan du hämta filtrera och facetable för ledigt på Edm.String och Edm.Boolean Edm.Int32 fälttyp. 

## <a name="build-and-load-an-index"></a>Skapa och läsa in ett index

Ett mellanliggande (och kanske uppenbara) steg är att du behöver [bygga och Fyll i indexet](https://docs.microsoft.com/azure/search/search-create-index-dotnet#create-the-index) innan du utforma en fråga. Vi har nämnt det här steget för fullständighetens skull. Ett sätt att avgöra om indexet är tillgänglig är genom att kontrollera listan över index den [portal](https://portal.azure.com).

## <a name="add-facet-filters-to-a-query"></a>Lägga till filter för aspekten för en fråga

Skapa en fråga som anger alla delar av en giltig fråga, inklusive sökuttryck, facets, filter, bedömningen profiler – allt används för att formulera en begäran i programkoden. I följande exempel skapas en begäran som skapar aspekten navigering baserat på vilken typ av logi, klassificering och andra faciliteter.

```csharp
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "category", "rating", "parkingIncluded", "smokingAllowed" },
};
```

### <a name="return-filtered-results-on-click-events"></a>Returnera filtrerade resultat på på händelser

Filteruttrycket hanterar klickningshändelsen på aspektvärdet. Klicka på kategorin ”översikt” ges en kategori-begränsningsaspekt implementeras via en `$filter` som väljer anpassningar av den typen. När en användare klickar på ”motell” för att indikera att endast motell ska visas i nästa fråga programmet skickar innehåller $filter = kategori eq 'motell'.

Följande kodavsnitt lägger till kategorin filtret om en användare väljer ett värde från kategori-begränsningsaspekt.

```csharp
if (categoryFacet != "")
  filter = "category eq '" + categoryFacet + "'";
```
Med hjälp av REST-API, skulle begäran att Ledad som `$filter=category eq 'c1'`. Om du vill göra kategori ett flervärdesfält, använder du följande syntax:`$filter=category/any(c: c eq 'c1')`

## <a name="tips-and-workarounds"></a>Tips och lösningar

### <a name="initialize-a-page-with-facets-in-place"></a>Initiera en sida av aspekter på plats

Om du vill initiera en sida av aspekter på plats kan du skicka en fråga som en del av sidan initiering som startvärde för sidan med en inledande aspekten struktur.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Bevara en aspekten navigeringsstruktur asynkront filtrerade resultat

En av utmaningarna med aspekten navigering i Azure Search är att det finns facets för aktuella resultat. I praktiken är det vanligt att behålla en statisk uppsättning facets så att användaren kan navigera i omvänd, följa tillbaka steg för att utforska alternativa sökvägar genom Sök efter innehåll. 

Detta är ett vanligt användningsfall, det är inte något aspekten navigeringsstrukturen innehåller för närvarande out box. Utvecklare som vill statiska facets vanligtvis kringgå begränsningen genom att utfärda två filtrerade frågor: en begränsad till resultaten, den andra används för att skapa en statisk lista över facets för navigering.

## <a name="see-also"></a>Se även

+ [Filter i Azure Search](search-filters.md)
+ [Skapa Index REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [Sök dokument REST-API](https://docs.microsoft.com/rest/api/searchservice/search-documents)

