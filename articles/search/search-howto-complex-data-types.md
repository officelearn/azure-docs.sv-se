---
title: Så här modellerar du komplexa datatyper
titleSuffix: Azure Cognitive Search
description: Kapslade eller hierarkiska datastrukturer kan modelleras i ett Azure Cognitive Search-index med hjälp av Datatyper för ComplexType och Samlingar.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2edd62825de08becf22f2f953a63a7f89f55e0a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283061"
---
# <a name="how-to-model-complex-data-types-in-azure-cognitive-search"></a>Så här modellerar du komplexa datatyper i Azure Cognitive Search

Externa datauppsättningar som används för att fylla i ett Azure Cognitive Search-index kan finnas i många former. Ibland innehåller de hierarkiska eller kapslade understrukturer. Exempel kan vara flera adresser för en enskild kund, flera färger och storlekar för en enda SKU, flera författare till en enda bok och så vidare. I modelleringstermer kan du se dessa strukturer som kallas *komplexa,* *sammansatta,* *sammansatta*eller *aggregerade* datatyper. Termen Azure Cognitive Search använder för det här konceptet är **komplex typ**. I Azure Cognitive Search modelleras komplexa typer med komplexa **fält**. Ett komplext fält är ett fält som innehåller underordnade (underfält) som kan vara av vilken datatyp som helst, inklusive andra komplexa typer. Detta fungerar på ett liknande sätt som strukturerade datatyper i ett programmeringsspråk.

Komplexa fält representerar antingen ett enskilt objekt i dokumentet eller en matris med objekt, beroende på datatypen. Fält av `Edm.ComplexType` typen representerar enstaka `Collection(Edm.ComplexType)` objekt, medan fält av typen representerar matriser med objekt.

Azure Cognitive Search stöder inbyggt komplexa typer och samlingar. Med dessa typer kan du modellera nästan alla JSON-strukturer i ett Azure Cognitive Search-index. I tidigare versioner av Azure Cognitive Search API:er kan endast förenklade raduppsättningar importeras. I den senaste versionen kan ditt index nu närmare motsvara källdata. Med andra ord, om källdata har komplexa typer kan ditt index också ha komplexa typer.

För att komma igång rekommenderar vi [hotelldatauppsättningen](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md), som du kan läsa in i guiden **Importera data** i Azure-portalen. Guiden identifierar komplexa typer i källan och föreslår ett indexschema baserat på de identifierade strukturerna.

> [!Note]
> Stöd för komplexa typer `api-version=2019-05-06`är allmänt tillgängligt i . 
>
> Om söklösningen bygger på tidigare lösningar med tillplattade datauppsättningar i en samling bör du ändra indexet så att det innehåller komplexa typer som stöds i den senaste API-versionen. Mer information om hur du uppgraderar API-versioner finns i [Uppgradera till den senaste REST API-versionen](search-api-migration.md) eller [Uppgradera till den senaste .NET SDK-versionen](search-dotnet-sdk-migration-version-9.md).

## <a name="example-of-a-complex-structure"></a>Exempel på en komplex struktur

Följande JSON-dokument består av enkla fält och komplexa fält. Komplexa fält, `Address` till `Rooms`exempel och , har underfält. `Address`har en enda uppsättning värden för dessa underfält, eftersom det är ett enda objekt i dokumentet. Däremot `Rooms` har flera uppsättningar värden för sina underfält, en för varje objekt i samlingen.

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

Precis som med alla indexdefinitioner kan du använda portalen, [REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)eller [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) för att skapa ett schema som innehåller komplexa typer. 

I följande exempel visas ett JSON-indexschema med enkla fält, samlingar och komplexa typer. Observera att i en komplex typ har varje underfält en typ och kan ha attribut, precis som fält på den översta nivån gör. Schemat motsvarar exempeldata ovan. `Address`är ett komplext fält som inte är en samling (ett hotell har en adress). `Rooms`är ett komplext samlingsfält (ett hotell har många rum).

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

## <a name="updating-complex-fields"></a>Uppdatera komplexa fält

Alla [omindexeringsregler](search-howto-reindex.md) som gäller för fält i allmänhet gäller fortfarande för komplexa fält. Om du upprepar några av huvudreglerna här, kräver det inte en indexreprisning om du lägger till ett fält, men de flesta ändringar gör det.

### <a name="structural-updates-to-the-definition"></a>Strukturella uppdateringar av definitionen

Du kan lägga till nya underfält i ett komplext fält när som helst utan att behöva återskapa index. Till exempel, lägga till `Address` "Postnummer" till `Rooms` eller "Bekvämligheter" till är tillåtet, precis som att lägga till en toppnivå fält till ett index. Befintliga dokument har ett null-värde för nya fält tills du uttryckligen fyller i dessa fält genom att uppdatera dina data.

Observera att i en komplex typ har varje delfält en typ och kan ha attribut, precis som fält på den översta nivån

### <a name="data-updates"></a>Datauppdateringar

Att uppdatera befintliga dokument `upload` i ett index med åtgärden fungerar på samma sätt för komplexa och enkla fält – alla fält ersätts. `merge` (eller `mergeOrUpload` när det tillämpas på ett befintligt dokument) fungerar dock inte på samma sätt i alla fält. `merge` Specifikt stöder inte sammanslagning av element i en samling. Den här begränsningen finns för samlingar av primitiva typer och komplexa samlingar. Om du vill uppdatera en samling måste du hämta hela insamlingsvärdet, göra ändringar och sedan inkludera den nya samlingen i index-API-begäran.

## <a name="searching-complex-fields"></a>Söka i komplexa fält

Fritt formulär sökuttryck fungerar som förväntat med komplexa typer. Om ett sökbart fält eller underfält någonstans i ett dokument matchar är själva dokumentet en matchning.

Frågor blir mer nyanserade när du har flera termer och operatorer, och vissa termer har angivna fältnamn, vilket är möjligt med [Lucene-syntaxen](query-lucene-syntax.md). Den här frågan försöker till exempel matcha två termer, "Portland" och "ELLER", mot två underfält i fältet Adress:

    search=Address/City:Portland AND Address/State:OR

Frågor som denna är *okorrelerade* för fulltextsökning, till skillnad från filter. I filter kororderas frågor över underfält i en komplex [ `any` samling `all` ](search-query-odata-collection-operators.md)med hjälp av intervallvariabler i eller . Lucene-frågan ovan returnerar dokument som innehåller både "Portland, Maine" och "Portland, Oregon", tillsammans med andra städer i Oregon. Detta beror på att varje sats gäller för alla värden i fältet i hela dokumentet, så det finns inget begrepp om ett "aktuellt underdokument". Mer information om detta finns [i Förstå OData-samlingsfilter i Azure Cognitive Search](search-query-understand-collection-filters.md).

## <a name="selecting-complex-fields"></a>Markera komplexa fält

Parametern `$select` används för att välja vilka fält som ska returneras i sökresultaten. Om du vill använda den här parametern för att välja specifika underfält i`/`ett komplext fält inkluderar du det överordnade fältet och underfältet avgränsade med ett snedstreck ( ).

    $select=HotelName, Address/City, Rooms/BaseRate

Fält måste markeras som Hämtningsbara i indexet om du vill ha dem i sökresultaten. Endast fält som är markerade som Hämtningsbara kan användas i en `$select` sats.

## <a name="filter-facet-and-sort-complex-fields"></a>Filtrera, aspekt och sortera komplexa fält

Samma [OData-sökvägssyntax](query-odata-filter-orderby-syntax.md) som används för filtrering och fältvisita sökningar kan också användas för att faceting, sortera och välja fält i en sökbegäran. För komplexa typer gäller regler som styr vilka underfält som kan markeras som sorterbara eller facetable. Mer information om dessa regler finns i [referensen Skapa index-API](/rest/api/searchservice/create-index).

### <a name="faceting-sub-fields"></a>Fasning av underfält

Alla delfält kan markeras som ansiktstabell om `Edm.GeographyPoint` `Collection(Edm.GeographyPoint)`det inte är av typ eller .

De dokumentantal som returneras i aspektresultaten beräknas för det överordnade dokumentet (ett hotell), inte underdokumenten i en komplex samling (rum). Anta till exempel att ett hotell har 20 rum av typen "svit". Med tanke på `facet=Rooms/Type`denna aspekt parameter, aspekten räkna kommer att vara en för hotellet, inte 20 för rummen.

### <a name="sorting-complex-fields"></a>Sortera komplexa fält

Sorteringsåtgärder gäller dokument (Hotell) och inte underdokument (Rum). När du har en komplex typsamling, till exempel Rum, är det viktigt att inse att du inte kan sortera på rum alls. Faktum är att du inte kan sortera på någon samling.

Sorteringsoperationer fungerar när fält har ett enda värde per dokument, om fältet är ett enkelt fält eller ett underfält i en komplex typ. Till exempel `Address/City` är tillåtet att vara sorterbar eftersom det bara `$orderby=Address/City` finns en adress per hotell, så kommer att sortera hotell efter stad.

### <a name="filtering-on-complex-fields"></a>Filtrering på komplexa fält

Du kan referera till underfält i ett komplext fält i ett filteruttryck. Använd bara samma [OData-sökvägssyntax](query-odata-filter-orderby-syntax.md) som används för att visa, sortera och markera fält. Följande filter returnerar till exempel alla hotell i Kanada:

    $filter=Address/Country eq 'Canada'

Om du vill filtrera på ett komplext [ `any` samlingsfält `all` ](search-query-odata-collection-operators.md)kan du använda ett **lambda-uttryck** med operatorerna och . I så fall är **intervallvariabeln** för lambda-uttrycket ett objekt med underfält. Du kan referera till dessa underfält med standardsyntaxen för OData-sökväg. Följande filter returnerar till exempel alla hotell med minst ett deluxerum och alla rökfria rum:

    $filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)

Precis som med enkla fält på den översta nivån kan enkla underfält i komplexa fält `true` endast inkluderas i filter om de har det **filterbara attributet** inställt på i indexdefinitionen. Mer information finns i [referensen Skapa index-API](/rest/api/searchservice/create-index).

## <a name="next-steps"></a>Nästa steg

Prova [hotels-datauppsättningen](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md) i guiden **Importera data.** Du behöver Cosmos DB-anslutningsinformationen som finns i readme för att komma åt data.

Med den informationen i handen är ditt första steg i guiden att skapa en ny Azure Cosmos DB-datakälla. Längre fram i guiden visas ett index med komplexa typer när du kommer till målindexsidan. Skapa och ladda det här indexet och kör sedan frågor för att förstå den nya strukturen.

> [!div class="nextstepaction"]
> [Snabbstart: portalguide för import, indexering och frågor](search-get-started-portal.md)
