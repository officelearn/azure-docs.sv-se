<properties
    pageTitle="Dataöverföring i Azure Search med hjälp av REST-API:et | Microsoft Azure | Värdbaserad söktjänst i molnet"
    description="Lär dig hur du laddar upp data till ett index i Azure Search med hjälp av REST-API:et."
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager=""
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="05/31/2016"
    ms.author="ashmaka"/>

# Ladda upp data till Azure Search med hjälp av REST-API:et
> [AZURE.SELECTOR]
- [Översikt](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)

Den här artikeln beskriver hur du använder [REST-API:et för Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx) för att importera data till ett Azure Search-index.

Innan du påbörjar den här genomgången bör du redan ha [skapat ett Azure Search-index](search-what-is-an-index.md).

För att skicka dokument till ditt index med hjälp av REST-API:et skickar du en HTTP POST-begäran till URL-slutpunkten för indexet. HTTP-begärandetexten är ett JSON-objekt som innehåller de dokument som ska läggas till, ändras eller tas bort.

## I. Identifiera Azure Search-tjänstens API-administratörsnyckel
När HTTP-begäranden skickas mot din tjänst med hjälp av REST-API:et måste *varje* API-begäran innehålla API-nyckeln som genererades för Search-tjänsten som du etablerade. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

1. För att hitta din tjänsts API-nycklar måste du logga in på [Azure Portal](https://portal.azure.com/).
2. Gå till Azure Search-tjänstens blad
3. Klicka på nyckelikonen

Tjänsten har *administratörsnycklar* och *frågenycklar*.

  - Dina primära och sekundära *administratörsnycklar* ger fullständig behörighet för alla åtgärder, inklusive möjligheten att hantera tjänsten, skapa och ta bort index, indexerare och datakällor. Det finns två nycklar så att du kan fortsätta att använda den sekundära nyckeln om du bestämmer dig för att återskapa den primära nyckeln och tvärtom.
  - Dina *frågenycklar* beviljar läsbehörighet till index och dokument och distribueras vanligen till klientprogram som skickar sökförfrågningar.

För att importera data till ett index kan du antingen använda din primära eller sekundära administratörsnyckel.

## II. Bestäm vilken indexeringsåtgärd som du vill använda
När du använder REST-API:et skickar du HTTP POST-begäranden med JSON-begärandetext till URL:en för Azure Search-indexets slutpunkt. JSON-objektet i HTTP-begärandetexten innehåller en enstaka JSON-matris med namnet ”value” som innehåller JSON-objekt som representerar dokument som du vill lägga till i ditt index, uppdatera eller ta bort.

Varje JSON-objekt i ”value”-matrisen representerar ett dokument som ska indexeras. Vart och ett av dessa objekt innehåller dokumentets nyckel och anger önskad indexeringsåtgärd (ladda upp, sammanfoga, ta bort osv.). Beroende på vilken av åtgärderna nedan som du väljer måste endast vissa fält tas med för varje dokument:

@search.action | Beskrivning | Nödvändiga fält för varje dokument | Anteckningar
--- | --- | --- | ---
`upload` | En `upload`-åtgärd liknar en ”upsert” där dokumentet infogas om det är nytt och uppdateras/ersätts om det finns. | nyckel plus eventuella andra fält som du vill definiera | När du uppdaterar och ersätter ett befintligt dokument tilldelas alla fält som inte angetts i begäran `null`. Detta sker även om fältet tidigare hade ett värde som inte var null.
`merge` | Uppdaterar ett befintligt dokument med de angivna fälten. Sammanfogningen misslyckas om dokumentet inte finns i indexet. | nyckel plus eventuella andra fält som du vill definiera | Alla fält som du anger i en sammanfogning ersätter det befintliga fältet i dokumentet. Detta gäller även fält av typen `Collection(Edm.String)`. Om dokumentet till exempel innehåller ett `tags`-fält med värdet `["budget"]` och du utför en sammanfogning med värdet `["economy", "pool"]` för `tags` så blir det slutliga värdet för fältet `tags` `["economy", "pool"]`. Det blir inte `["budget", "economy", "pool"]`.
`mergeOrUpload` | Den här åtgärden fungerar som `merge` om ett dokument med den angivna nyckeln redan finns i indexet. Om dokumentet inte finns fungerar den som `upload` med ett nytt dokument. | nyckel plus eventuella andra fält som du vill definiera | -
`delete` | Tar bort det angivna dokumentet från indexet. | endast nyckel | Andra fält som du anger än nyckelfältet ignoreras. Om du vill ta bort ett enstaka fält från ett dokument använder du `merge` i stället och anger bara fältet till null.

## III. Skapa HTTP-begäran och begärandetexten
Nu när du har samlat in nödvändiga fältvärden för dina indexåtgärder är det dags att skapa själva HTTP-begärandena och JSON-begärandetexten för att importera dina data.

#### Begäran och begärandehuvuden
I URL:en måste du ange tjänstnamnet, indexnamnet (”hotels” i detta fall) samt API-versionen (den aktuella API-versionen är `2015-02-28` vid tidpunkten för publiceringen av det här dokumentet). Du måste definiera `Content-Type`- och `api-key`-begärandehuvuden. För det senare använder du någon av administratörsnycklarna för tjänsten.

    POST https://[search service].search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28
    Content-Type: application/json
    api-key: [admin key]

#### Begärandetext

```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "hotelId": "1",
            "baseRate": 199.0,
            "description": "Best hotel in town",
            "description_fr": "Meilleur hôtel en ville",
            "hotelName": "Fancy Stay",
            "category": "Luxury",
            "tags": ["pool", "view", "wifi", "concierge"],
            "parkingIncluded": false,
            "smokingAllowed": false,
            "lastRenovationDate": "2010-06-27T00:00:00Z",
            "rating": 5,
            "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
            "@search.action": "upload",
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags": ["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        },
        {
            "@search.action": "delete",
            "hotelId": "6"
        }
    ]
}
```

I detta fall använder vi `upload`, `mergeOrUpload` och `delete` som våra sökåtgärder.

Anta att exempelindexet ”hotels” redan fyllts med ett antal dokument. Observera att vi inte behövde ange alla tillgängliga dokumentfält när vi använde `mergeOrUpload` och att vi bara angav dokumentnyckeln (`hotelId`) när vi använde `delete`.

Observera också att du bara kan ta med upp till 1 000 dokument (eller 16 MB) i samma indexeringsbegäran.

## IV. Förstå HTTP-svarskoden
#### 200
När du har skickat en lyckad indexeringsbegäran får du ett HTTP-svar med statuskoden `200 OK`. JSON-texten i HTTP-svaret är:

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": true,
            "errorMessage": null
        },
        ...
    ]
}
```

#### 207
Statuskoden `207` returneras om minst ett objekt inte indexerades. JSON-texten i HTTP-svaret innehåller information om det eller de dokument som misslyckades.

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": false,
            "errorMessage": "The search service is too busy to process this document. Please try again later."
        },
        ...
    ]
}
```

> [AZURE.NOTE] Detta innebär ofta att belastningen på din söktjänst når en punkt där indexeringsbegäranden börjar returnera `503`-svar. I detta fall rekommenderar vi starkt att klientkoden stannar upp och väntar innan ett nytt försök görs. På så sätt får systemet tid på sig att återställas, vilket ökar sannolikheten för att framtida begäranden lyckas. Snabba återförsök kommer endast att förlänga situationen.

#### 429
Statuskoden `429` returneras om du överskrider kvoten för antal dokument per index.

#### 503
Statuskoden `503` returneras om inget av objekten i begäran indexerades. Detta fel innebär att systemet är hårt belastat och att det inte går att bearbeta din begäran just nu.

> [AZURE.NOTE] I detta fall rekommenderar vi starkt att klientkoden stannar upp och väntar innan ett nytt försök görs. På så sätt får systemet tid på sig att återställas, vilket ökar sannolikheten för att framtida begäranden lyckas. Snabba återförsök kommer endast att förlänga situationen.

Mer information om dokumentåtgärder och svar om lyckade/misslyckade åtgärder finns i [Lägga till, uppdatera eller ta bort dokument](https://msdn.microsoft.com/library/azure/dn798930.aspx). Mer information om andra HTTP-statuskoder som kan returneras om det uppstår fel finns i [HTTP-statuskoder (Azure Search)](https://msdn.microsoft.com/library/azure/dn798925.aspx).

## Nästa
När du har fyllt Azure Search-indexet kan du börja skicka frågor för att söka efter dokument. Mer information finns i [Fråga ditt Azure Search-index](search-query-overview.md).



<!--HONumber=jun16_HO2-->


