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
ms.date: 06/13/2019
ms.custom: seodec2018
ms.openlocfilehash: e7e6ddefd13d669c949389bc4fad85fb6cff4d3a
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621367"
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>Hur komplexa datatyper i Azure Search

Externa datauppsättningar som används för att fylla ett Azure Search-index kan finnas i olika former. Ibland innehåller de hierarkisk eller kapslad underordnade strukturer. Exemplen kan inkludera flera adresser för en enda kund, flera färger och storlekar för en enda SKU flera författare till en enda bok, och så vidare. Modellering villkor, du kan se dessa strukturer kallas *komplexa*, *sammansatt*, *sammansatta*, eller *sammanställd* datatyper. Termen som använder Azure Search för det här konceptet är **komplex typ**. I Azure Search komplexa typer modelleras med **komplexa fält**. Ett komplext fält är ett fält som innehåller underordnade (underordnade fält) som kan vara av olika datatyper, inklusive andra komplexa typer. Detta fungerar på liknande sätt som strukturerade datatyper i ett programmeringsspråk.

Komplexa fält representerar ett enskilt objekt i dokumentet eller en matris med objekt, beroende på datatypen. Fält av typen `Edm.ComplexType` representerar enstaka objekt, medan fält av typen `Collection(Edm.ComplexType)` representerar matriser av objekt.

Azure Search har inbyggt stöd för komplexa typer och samlingar. Dessa typer kan du modellera nästan alla JSON-strukturen i ett Azure Search-index. I tidigare versioner av Azure Search API: er endast förenklas raden anger kunde importeras. I den senaste versionen kan ditt index nu bättre motsvara källdata. Med andra ord om dina källdata har komplexa typer, kan ditt index ha komplexa typer också.

För att komma igång, rekommenderar vi den [Hotels datauppsättning](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md), du kan läsa in i den **dataimport** guiden i Azure-portalen. Guiden identifierar komplexa typer i källan och föreslår ett indexschema baserat på identifierade strukturer.

> [!Note]
> Stöd för komplexa typer som är allmänt tillgängligt i `api-version=2019-05-06`. 
>
> Om din search-lösning bygger på tidigare lösningar av Flat datauppsättningar i en samling, bör du ändra ditt index med komplexa typer som stöds i den senaste API-versionen. Mer information om hur du uppgraderar API-versioner finns i [uppgradera till den senaste REST API-versionen](search-api-migration.md) eller [uppgradera till den senaste versionen av .NET SDK](search-dotnet-sdk-migration-version-9.md).

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
      "BaseRate": 96.99
    },
    {
      "Description": "Deluxe Room, 2 Double Beds (City View)",
      "Type": "Deluxe Room",
      "BaseRate": 150.99
    },
  ]
}
```

## <a name="creating-complex-fields"></a>Skapa komplexa fält

Som med alla indexdefinitionen, du kan använda portalen [REST API](https://docs.microsoft.com/rest/api/searchservice/create-index), eller [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) att skapa ett schema som innehåller komplexa typer. 

I följande exempel visar ett JSON-indexschema med enkel fält, samlingar och komplexa typer. Observera att inom en komplex typ varje underordnade fält har en typ och kan ha attribut, precis som översta fält. Schemat motsvarar ovanstående exempeldata. `Address` är ett komplext fält som inte är en samling (ett hotell har en adress). `Rooms` är en komplex samling fält (ett hotell har många rum).

<!---
For indexes used in a [push-model data import](search-what-is-data-import.md) strategy, where you are pushing a JSON data set to an Azure Search index, you can only have the basic syntax shown here: single complex types like `Address`, or a `Collection(Edm.ComplexType)` like `Rooms`. You cannot have complex types nested inside other complex types in an index used for push-model data ingestion.

Indexers are a different story. When defining an indexer, in particular one used to build a knowledge store, your index can have nested complex types. An indexer is able to hold a chain of complex data structures in-memory, and when it includes a skillset, it can support highly complex data forms. For more information and an example, see [How to get started with knowledge store](knowledge-store-howto.md).
-->

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true }
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

Uppdatering av befintliga dokument i ett index med det `upload` åtgärden fungerar på samma sätt för komplexa och enkelt fält – alla fält har ersatts. Dock `merge` (eller `mergeOrUpload` när tillämpas på ett befintligt dokument) fungerar inte samma mellan alla fält. Mer specifikt `merge` stöder inte koppla samman element i en samling. Den här begränsningen finns för samlingar av primitiva typer och komplexa samlingar. Om du vill uppdatera en samling behöver du att hämta fullständig insamling-värdet gör ändringar och sedan inkludera den nya samlingen i indexet API-begäran.

## <a name="searching-complex-fields"></a>Söka komplexa fält

Formaliafri sökuttryck fungerar som förväntat med komplexa typer. Om det matchar något sökbara fält eller underordnade fält var som helst i ett dokument, är en matchning med själva dokumentet.

Frågor få mer nyanserade när du har flera villkor och operatörer och vissa termer har fältnamnen som anges, precis som du kan göra med den [Lucene syntax](query-lucene-syntax.md). Exempelvis kan den här frågan försöker matcha två villkor, ”Portland” och ”OR” mot två underordnade fält i adressfältet:

    search=Address/City:Portland AND Address/State:OR

Frågor som det här är *uncorrelated* för fulltextsökning, till skillnad från filter. I filter, frågor via underordnade fält av en komplex samling kopplas ihop med intervallet variabler i [ `any` eller `all` ](search-query-odata-collection-operators.md). Lucene-frågan ovan returnerar dokument som innehåller både ”Portland, Maine” och ”Portland, Oregon”, tillsammans med andra städer i Oregon. Detta inträffar eftersom varje villkor gäller för alla värden i dess fält i hela dokumentet, så det ingen finns av ett ”aktuella underordnade dokument”. Mer information om detta finns i [förstå OData samling filter i Azure Search](search-query-understand-collection-filters.md).

## <a name="selecting-complex-fields"></a>Välja komplexa fält

Den `$select` används för att välja vilka fält som returneras i sökresultaten. För att använda den här parametern för att välja specifika underordnade fält på ett fält i komplexa måste innehålla fälten överordnade och underordnade avgränsade med ett snedstreck (`/`).

    $select=HotelName, Address/City, Rooms/BaseRate

Fält måste markeras som hämtningsbara i indexet om du vill ha dem i sökresultaten. Endast fält som är markerade som hämtningsbara kan användas i en `$select` instruktionen.

## <a name="filter-facet-and-sort-complex-fields"></a>Filter, aspekten och sortera komplexa fält

Samma [OData sökvägssyntaxen](query-odata-filter-orderby-syntax.md) används för att filtrera och fielded sökningar kan också användas för aspekter, sortering och välja fält i en sökbegäran. För komplexa typer tillämpas reglerna som styr vilka underordnade fält kan markeras som kan sorteras eller fasettbara. Mer information om dessa regler finns i den [Create Index-API-referens](https://docs.microsoft.com/rest/api/searchservice/create-index#request).

### <a name="faceting-sub-fields"></a>Fasettering underordnade fält

Alla underordnade fält kan markeras som fasettbar om det inte är av typen `Edm.GeographyPoint` eller `Collection(Edm.GeographyPoint)`.

Antalet dokument som returneras i resultatet aspekten beräknas för det överordnade dokumentet (ett hotell) inte underordnade dokumenten i en komplex samling (rum). Anta exempelvis att ett hotell har 20 rum av typen ”suite”. Beroende av den här aspekten parametern `facet=Rooms/Type`, antalet aspekten blir en för hotell, inte 20 för lokalerna.

### <a name="sorting-complex-fields"></a>Sortera komplexa fält

Sorteringsåtgärder gäller dokument (hotell) och inte underordnade dokument (rum). När du har en komplex typ-samling, till exempel rum, är det viktigt att tänka på att du inte kan sortera rum alls. I själva verket kan du sortera efter en samling.

Sorteringsåtgärder fungerar när fält har ett enda värde per dokument, om fältet är en enkel fältreferens eller ett underordnade fält i en komplex typ. Till exempel `Address/City` får vara sorterbar eftersom det finns bara en adress per hotell, så `$orderby=Address/City` sorteras hotels efter ort.

### <a name="filtering-on-complex-fields"></a>Filtrering på komplexa fält

Du kan referera till underordnade fält i ett komplext fält i ett filteruttryck. Använd samma [OData sökvägssyntaxen](query-odata-filter-orderby-syntax.md) som används för aspekter, sortering och välja fält. Exempelvis returnerar följande filter alla hotell i Kanada:

    $filter=Address/Country eq 'Canada'

Om du vill filtrera på en komplex samling fält, kan du använda en **lambda-uttrycket** med den [ `any` och `all` operatörer](search-query-odata-collection-operators.md). I så fall den **intervallet variabeln** av lambda-uttrycket är ett objekt med underordnade fält. Du kan referera till de underordnade fält med OData-syntax för standard-sökväg. Exempelvis returnerar följande filter alla hotell med minst en deluxe plats och alla icke-hälsan rum:

    $filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)

Som med översta enkel fält, enkel underordnade fält av komplexa fält kan bara ingå i filter om de har den **filtrerbara** attributet inställt på `true` i indexdefinitionen. Mer information finns i den [Create Index-API-referens](https://docs.microsoft.com/rest/api/searchservice/create-index#request).

## <a name="next-steps"></a>Nästa steg

Prova den [Hotels datauppsättning](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md) i den **dataimport** guiden. Du måste Cosmos DB-anslutningsinformationen som angavs i viktigt att komma åt data.

Med den informationen till hands är första steget i guiden att skapa en ny Azure Cosmos DB-datakälla. Ytterligare ser på i guiden när du kommer till indexsidan target du ett index med komplexa typer. Skapa och läsa in det här indexet och sedan köra frågor för att förstå den nya strukturen.

> [!div class="nextstepaction"]
> [Snabbstart: hanteringsportalens guide för import, indexering och frågor](search-get-started-portal.md)