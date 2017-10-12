---
title: "Fråga ett index (REST-API – Azure Search)| Microsoft Docs"
description: "Skapa en sökfråga i Azure Search och använd sökparametrar för att filtrera och sortera sökresultat."
services: search
documentationcenter: 
manager: jhubbard
author: ashmaka
ms.assetid: 8b3ca890-2f5f-44b6-a140-6cb676fc2c9c
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 01/12/2017
ms.author: ashmaka
ms.openlocfilehash: 49062bec233ad35cd457f9665fa94c1855343582
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="query-your-azure-search-index-using-the-rest-api"></a>Skicka frågor mot ditt Azure Search-index med hjälp av REST-API:et
> [!div class="op_single_selector"]
>
> * [Översikt](search-query-overview.md)
> * [Portalen](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
>
>

Den här artikeln beskriver hur du kör frågor mot ett index med hjälp av [REST-API:et för Azure Search](https://docs.microsoft.com/rest/api/searchservice/).

Innan du påbörjar den här genomgången bör du redan ha [skapat ett Azure Search-index](search-what-is-an-index.md) och [fyllt det med data](search-what-is-data-import.md). Mer bakgrundsinformation finns i [Hur fullständig textsökning fungerar i Azure Search](search-lucene-query-architecture.md).

## <a name="identify-your-azure-search-services-query-api-key"></a>Identifiera API-frågenyckeln för Azure Search-tjänsten
En viktig del av varje sökåtgärd mot REST-API:et för Azure Search är *API-nyckeln* som genererades för tjänsten som du etablerat. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

1. Om du vill hitta din tjänsts API-nycklar kan du logga in på [Azure Portal](https://portal.azure.com/)
2. Gå till Azure Search-tjänstens blad
3. Klicka på ikonen ”Nycklar”

Tjänsten har *administratörsnycklar* och *frågenycklar*.

* Dina primära och sekundära *administratörsnycklar* ger fullständig behörighet för alla åtgärder, inklusive möjligheten att hantera tjänsten, skapa och ta bort index, indexerare och datakällor. Det finns två nycklar så att du kan fortsätta att använda den sekundära nyckeln om du bestämmer dig för att återskapa den primära nyckeln och tvärtom.
* Dina *frågenycklar* beviljar läsbehörighet till index och dokument och distribueras vanligen till klientprogram som skickar sökförfrågningar.

Du kan använda en av din frågenycklar för att skicka frågor till ett index. Administratörsnycklarna kan även användas för frågor, men du bör använda en frågenyckel i programkoden eftersom detta bättre överensstämmer med [principen om lägsta behörighet](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## <a name="formulate-your-query"></a>Formulera frågan
Du kan [söka i ditt index med hjälp av REST-API:et](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) på två sätt. Ett sätt är att skicka en HTTP POST-begäran där dina frågeparametrar definieras i ett JSON-objekt i begärandetexten. Det andra sättet är att skicka en HTTP GET-begäran där dina frågeparametrar definieras i URL:en för begäran. POST har mindre [restriktiva gränser](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) vad gäller frågeparametrarnas storlek än GET. Av den anledningen rekommenderar vi att du använder POST såvida det inte finns särskilda omständigheter som gör att GET är lämpligare.

För både POST och GET måste du ange *tjänstnamnet*, *indexnamnet* och *API-versionen* (den aktuella API-versionen är `2016-09-01` vid tidpunkten för publiceringen av det här dokumentet) i URL:en för begäran. För GET anger du frågeparametrarna i *frågesträngen* i slutet av URL:en. Se URL-formatet nedan:

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2016-09-01

Formatet för POST är samma, men med endast ”api-version” i frågesträngsparametrarna.

#### <a name="example-queries"></a>Exempelfrågor
Här är några exempelfrågor för ett index med namnet ”hotels”. Frågorna visas i både GET- och POST-format.

Sök igenom hela indexet efter termen ”budget” och returnera bara `hotelName`-fältet:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=budget&$select=hotelName&api-version=2016-09-01

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
{
    "search": "budget",
    "select": "hotelName"
}
```

Använd ett filter för indexet för att hitta hotell som är billigare än 150 USD per natt och returnera `hotelId` och `description`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2016-09-01

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

Sök igenom hela indexet, ordna efter ett visst fält (`lastRenovationDate`) i fallande ordning och visa endast `hotelName` och `lastRenovationDate`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate&api-version=2016-09-01

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
{
    "search": "*",
    "orderby": "lastRenovationDate desc",
    "select": "hotelName,lastRenovationDate",
    "top": 2
}
```

## <a name="submit-your-http-request"></a>Skicka HTTP-begäran
Nu när du har formulerat frågan som en del av HTTP-begärans URL (för GET) eller brödtext (för POST) kan du definiera begärandehuvudena och skicka din fråga.

#### <a name="request-and-request-headers"></a>Begäran och begärandehuvuden
Du måste definiera två begärandehuvuden för GET, eller tre för POST:

1. `api-key`-huvudet måste anges till frågenyckeln som du identifierade i steg I ovan. Du kan även använda en administratörsnyckel som `api-key`-huvud, men vi rekommenderar att du använder en frågenyckel eftersom den ger exklusiv läsbehörighet till index och dokument.
2. `Accept`-huvudet måste anges till `application/json`.
3. För POST måste `Content-Type`-huvudet också anges till `application/json`.

Nedan illustreras en HTTP GET-begäran för en sökning i ”hotels”-indexet med hjälp av REST-API:et för Azure Search, med en enkel fråga som söker efter termen ”motel”:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=motel&api-version=2016-09-01
Accept: application/json
api-key: [query key]
```

Här är samma exempelfråga men med HTTP POST:

```
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
Content-Type: application/json
Accept: application/json
api-key: [query key]

{
    "search": "motel"
}
```

Ett lyckat frågeresultat returnerar statuskoden `200 OK` och sökresultaten returneras som JSON i svarstexten. Så här ser resultatet ut för ovanstående fråga, förutsatt att indexet ”hotels” fyllts med exempeldata från [Dataimport i Azure Search med hjälp av REST-API:et](search-import-data-rest-api.md). (Observera att JSON har formaterats för tydlighetens skull.)

```JSON
{
    "value": [
        {
            "@search.score": 0.59600675,
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags":["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": {
                "type": "Point",
                "coordinates": [-122.131577, 49.678581],
                "crs": {
                    "type":"name",
                    "properties": {
                        "name": "EPSG:4326"
                    }
                }
            }
        }
    ]
}
```

Om du vill veta mer går du till avsnittet ”Svar” i [Söka efter dokument](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Mer information om andra HTTP-statuskoder som kan returneras om det uppstår fel finns i [HTTP-statuskoder (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).
