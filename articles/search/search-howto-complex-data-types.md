---
title: Hur komplexa datatyper – Azure Search
description: Kapslade eller hierarkiska datastrukturer kan utformas i ett Azure Search-index med datatyperna ComplexType och samlingar.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: seodec2018
ms.openlocfilehash: 397b3ea7fee67e25cd160f6b529a660e18c44046
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024743"
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>Hur komplexa datatyper i Azure Search

Externa datauppsättningar som används för att fylla ett Azure Search-index ibland omfattar hierarkisk eller kapslad underordnade strukturer. Exemplen kan inkludera flera platser och telefonnummer för en enda kund, flera färger och storlekar för en enda SKU flera författare till en enda bok, och så vidare. Modellering villkor, du kan se dessa strukturer kallas *komplexa datatyper*, *sammansatt datatyper*, *sammansatta datatyper*, eller *sammanställd datatyper*. I Azure Search i den här artikeln är en komplex typ ett fält som innehåller underordnade objekt (underordnade fält) som kan vara enkla eller komplexa. Detta liknar en strukturerad datatyp i ett programmeringsspråk. Komplexa fält kan vara enkel fält, som representerar ett enda objekt i dokumentet eller en samling som representerar en matris med objekt

Azure Search har inbyggt stöd för komplexa typer och samlingar. Tillsammans kan dessa typer du modellera nästan alla kapslad JSON-strukturen i ett Azure Search-index. I tidigare versioner av Azure Search API: er endast förenklas raden anger kunde importeras. I den senaste versionen kan ditt index nu bättre motsvara källdata. Med andra ord om dina källdata har komplexa typer, kan ditt index ha komplexa typer också.

För att komma igång, rekommenderar vi den [Hotels datauppsättning](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md), du kan läsa in i den **dataimport** guiden i Azure-portalen. Guiden identifierar komplexa typer i källan och föreslår ett indexschema baserat på identifierade strukturer.

> [!Note]
> Stöd för komplexa typer som är allmänt tillgängligt i `api-version=2019-05-06`. 
>
> Om din search-lösning bygger på tidigare lösningar av Flat datauppsättningar i en samling, bör du ändra ditt index med komplexa typer som stöds i den senaste API-versionen. Mer information om hur du uppgraderar API-versioner finns i [uppgradera till den senaste REST API-versionen](search-api-migration.md) eller [uppgradera till den senaste versionen av .NET SDK](search-dotnet-sdk-migration.md).

## <a name="example-of-a-complex-structure"></a>Exempel på en komplex struktur

Följande JSON-dokumentet består av enkla och komplexa fält. Komplex fält som `Address` och `Rooms`, har underordnade fält. `Address` har en enda uppsättning värden för fälten underordnade, eftersom det är ett enda objekt i dokumentet. Däremot `Rooms` har flera uppsättningar med värden för dess underordnade fält, en för varje objekt i samlingen.

```json
{
    "HotelId": "1",
    "HotelName": "Secret Point Motel",
    "Description": "Ideally located on the main commercial artery of the city in the heart of New York.",
    "Address": {
        "StreetAddress": "677 5th Ave",
        "City": "New York",
        "StateProvince": "NY"
    },
    "Rooms": [
        {
            "Description": "Budget Room, 1 Queen Bed (Cityside)",
            "Type": "Budget Room",
            "BaseRate": 96.99,
        },
        {
            "Description": "Deluxe Room, 2 Double Beds (City View)",
            "Type": "Deluxe Room",
            "BaseRate": 150.99,
        },
    ]
}
```

## <a name="creating-complex-fields"></a>Skapa komplexa fält

Som med alla indexdefinitionen, du kan använda portalen [REST API](https://docs.microsoft.com/rest/api/searchservice/create-index), eller [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) att skapa ett schema som innehåller komplexa typer. 

I följande exempel visar ett JSON-indexschema med enkel fält, samlingar och komplexa typer. Observera att inom en komplex typ varje underordnade fält har en typ och kan ha attribut, precis som översta fält. Schemat motsvarar ovanstående exempeldata. `Address` är ett komplext fält som inte är en samling (ett hotell har en adress). `Rooms` är en komplex samling fält (ett hotell har många rum).

<!---
For indexes used in a [push-model data import](search-what-is-data-import.md) strategy, where you are pushing a JSON data set to an Azure Search index, you can only have the basic syntax shown here: single complex types like `Address`, or a `Collection(Edm.ComplexType)` like `Rooms`. You cannot have complex types nested inside other complex types in an index used for push-model data ingestion.

Indexers are a different story. When defining an indexer, in particular one used to build a knowledge store, your index can have nested complex types. An indexer is able to hold a chain of complex data structures in-memory, and when it includes a skillset, it can support highly complex data forms. For more information and an example, see [How to get started with Knowledge Store](knowledge-store-howto.md).
-->

```json
{
    "name": "hotels",
    "fields": [
        {   "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true    },
        {   "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        {   "name": "Address", "type": "Edm.ComplexType",
            "fields": [{
                    "name": "StreetAddress",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true  },
                {
                    "name": "City",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "StateProvince",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                }
            ]
        },
        {
            "name": "Rooms",
            "type": "Collection(Edm.ComplexType)",
            "fields": [{
                    "name": "Description",
                    "type": "Edm.String",
                    "searchable": true,
                    "analyzer": "en.lucene"
                },
                {
                    "name": "Type",
                    "type": "Edm.String",
                    "searchable": true
                },
                {
                    "name": "BaseRate",
                    "type": "Edm.Double",
                    "filterable": true,
                    "facetable": true
                },
            ]
        }
    ]
}
```
## <a name="updating-complex-fields"></a>Uppdaterar komplexa fält

Alla de [omindexering regler](search-howto-reindex.md) som gäller för fält i allmänhet fortfarande gäller för komplexa fält. Omformulera några av de viktigaste reglerna här, lägger till ett fält inte kräver återskapning ett index, men de flesta ändringarna gör.

### <a name="structural-updates-to-the-definition"></a>Strukturella uppdateringar av definitionen

Du kan lägga till nya underordnade fält till ett komplext fält när som helst utan att behöva återskapa ett index. Till exempel att lägga till ”postnummer” till `Address` eller ”Bekvämligheterna” till `Rooms` tillåts, precis som att lägga till ett högsta fält i ett index. Befintliga dokument har ett null-värde för nya fält tills du uttryckligen fylla i fälten genom att uppdatera dina data.

Observera att inom en komplex typ varje underordnade fält har en typ och kan ha attribut, precis som översta fält

### <a name="data-updates"></a>Datauppdateringar

Uppdatering av befintliga dokument i ett index med åtgärden för att ladda upp fungerar på samma sätt för komplexa och enkelt fält – alla fält har ersatts. Dock fungerar merge (eller mergeOrUpload när tillämpas på ett befintligt dokument) inte samma mellan alla fält. Mer specifikt har merge inte möjlighet att koppla element i en samling. Det här gäller för samlingar av primitiva typer, samt komplexa samlingar. Om du vill uppdatera en samling behöver du att hämta fullständig insamling-värdet gör ändringar, och sedan inkludera den nya samlingen i indexet API-begäran.


## <a name="searching-complex-fields"></a>Söka komplexa fält

Formaliafri sökuttryck fungerar som förväntat med komplexa typer. Om det matchar något sökbara fält eller underordnade fält var som helst i ett dokument, är en matchning med själva dokumentet. 

Frågor få mer nyanserade när du har flera villkor och operatörer och vissa termer har fältnamnen som anges, precis som du kan göra med den [Lucene syntax](query-lucene-syntax.md). Exempelvis kan den här frågan försöker matcha två villkor, ”Portland” och ”OR” mot två underordnade fält i adressfältet:

```json
search=Address/City:Portland AND Address/State:OR
```

Frågor så här är uncorrelated för textsökning (till skillnad från filter, där frågor via underordnade fält av en komplex samling kan korreleras med hjälp av någon eller alla, liknande en korrelerad underfråga i SQL). Det innebär att dokument som innehåller ”Portland, Maine” samt ”Portland, Oregon” och andra städer i Oregon returneras Lucene frågan ovan. Det beror på att varje villkor utvärderas mot alla värden för det angivna fältet i hela dokumentet, så det finns inga begreppet en ”underordnad dokumentet”. 

 

## <a name="selecting-complex-fields"></a>Välja komplexa fält

Den `$select` används för att välja vilka fält som returneras i sökresultaten. För att använda den här parametern för att välja specifika underordnade fält på ett fält i komplexa måste innehålla fälten överordnade och underordnade avgränsade med ett snedstreck (`/`).

```json
$select=HotelName, Address/City, Rooms/BaseRate
```

Fält måste markeras som hämtningsbara i indexet om du vill ha dem i sökresultaten. Endast fält som är markerade som hämtningsbara kan användas i en `$select` instruktionen. 


## <a name="filter-facet-and-sort-complex-fields"></a>Filter, aspekten och sortera komplexa fält

Samma [OData sökvägssyntaxen](query-odata-filter-orderby-syntax.md) används för att filtrera och fielded sökningar kan också användas för aspekter, sortering och välja fält i en sökbegäran. För komplexa typer tillämpas reglerna som styr vilka underordnade fält kan markeras som kan sorteras eller fasettbara. 

### <a name="faceting-sub-fields"></a>Fasettering underordnade fält 

Alla underordnade fält kan markeras som fasettbar om det inte är av typen `Edm.GeographyPoint` eller `Collection(Edm.GeographyPoint)`. 

När antalet dokument som returneras för aspektbaserad navigeringsstruktur är antalen i förhållande till det överordnade dokumentet (ett hotell) inte att kapslade dokument inom en komplex samling (rum). Anta exempelvis att ett hotell har 20 rum av typen ”suite”. Beroende av den här aspekten parametern `facet=Rooms/Type`, antalet aspekten tas en för det överordnade dokumentet (hotell) och inte mellanliggande underordnade dokument (rum). 

### <a name="sorting-complex-fields"></a>Sortera komplexa fält

Sorteringsåtgärder gäller dokument (hotell) och inte underordnade dokument (rum). När du har en komplex typ-samling, till exempel rum, är det viktigt att tänka på att du inte kan sortera rum alls. I själva verket kan du sortera efter en samling. 

Sorteringsåtgärder fungerar om fälten är enkelvärdesattribut, om som en enkel fältreferens eller som ett underordnade fält i en komplex typ. Till exempel `$orderby=Address/ZipCode` komplex typ är sorterbar eftersom det är endast ett postnummer per hotell. 

Omformulera reglerna kring sortering i ett indexfält måste vara markerad som Filtrerbart och sorterbart som ska användas i en `$orderby` instruktionen. 

## <a name="next-steps"></a>Nästa steg

 Prova den [Hotels datauppsättning](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md) i den **dataimport** guiden. Du måste Cosmos DB-anslutningsinformationen som angavs i viktigt att komma åt data. 
 
 Med den informationen till hands är första steget i guiden att skapa en ny Azure Cosmos DB-datakälla. Ytterligare på i guiden när du kommer till indexsidan mål du kommer att se ett index med komplexa typer. Skapa och läsa in det här indexet och sedan köra frågor för att förstå den nya strukturen.

> [!div class="nextstepaction"]
> [Snabbstart: hanteringsportalens guide för import, indexering och frågor](search-get-started-portal.md)