---
title: Sök efter kategorier för den lokala företag i Bing | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Använd den här artikeln för att lära dig hur du anger search kategorier för den lokala företag i Bing search API-slutpunkten.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-local-business
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: aec45e94e2cbec9093cdb72c896dabc633f118b2
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2018
ms.locfileid: "50748722"
---
# <a name="search-categories-for-the-bing-local-business-search-api"></a>Sök i kategorier för den lokala företag i Bing

Lokala företag sökning i Bing kan du söka efter lokala affärsentiteter i olika kategorier, där prioritet ges att Stäng resultat användarens plats. Du kan inkludera dessa sökningar i sökningar tillsammans med den `localCircularView` och `localMapView` [parametrar](specify-geographic-search.md).


## <a name="toplevel-categories"></a>TopLevel kategorier 

Följande typer definiera huvudkategorierna av search.  Mer än en kategori kan anges med hjälp av en kommaavgränsad lista som tilldelats den `localCategories` parametern.  
- EatDrink 
- SeeDo 
- Butik 
- HotelsAndMotels 
- BanksAndCreditUnions 
- Parkera 
- Sjukhus 

## <a name="sub-categories"></a>Underkategorier
Underkategorier skickas på samma sätt som `localCategories`. Underkategorier finns mer specifika kategorier. De är underordnade i den mening att om du anger en kategori C och en av dess underkategorier S i samma kommaavgränsad lista du får samma resultat som om du har angett C enbart.

### <a name="eat-drink"></a>Äta dryck 
|  |  |  |  |
| - | - | - | - |
| BreweriesAndBrewPubs | CocktailLounges | AfricanRestaurants |
| AmericanRestaurants | Bagels | BarbecueRestaurants |
| Taverns | SportsBars | Staplar |
| BarsGrillsAndPubs | BuffetRestaurants| BelgianRestaurants | 
| BritishRestaurants | CafeRestaurants | CaribbeanRestaurants |
| ChineseRestaurants | CoffeeAndTea | Delicatessens | 
| DeliveryService | Diners | DiscountStores | 
| Donuts | FastFood | FrenchRestaurants | 
| FrozenYogurt | GermanRestaurants | Stormarknader | 
| GreekRestaurants | Grocers | HawaiianRestaurants | 
| HungarianRestaurants | IceCreamAndFrozenDesserts | IndianRestaurants | 
| ItalianRestaurants | JapaneseRestaurants | Bärsaft | 
| KoreanRestaurants | LiquorStores | MexicanRestaurants |
| MiddleEasternRestaurants | pizza | PolishRestaurants | 
| PortugueseRestaurants | Salta pinnar | Restauranger | 
| RussianAndUkrainianRestaurants | Smörgåsar | SeafoodRestaurants | 
| SpanishRestaurants | SteakHouseRestaurants | SushiRestaurants | 
| Sak | ThaiRestaurants | TurkishRestaurants | 
| VegetarianAndVeganRestaurants | VietnameseRestaurants|  |
 
### <a name="see-do"></a>Se gör 
|  |  |  |
| -- | -- | -- |
| AmusementParks | Attraktioner | Carnivals |
| Kasinon | LandmarksAndHistoricalSites | MiniatureGolfCourses |
| MovieTheaters | Museer | Parker |
| SightseeingTours | TouristInformation | Djurparker |
 
### <a name="shop"></a>Butik 
|  |  |  |
| -- | -- | -- |
| AntiqueStores | Bokhandeln | CDAndRecordStores |
| ChildrensClothingStores | CigarAndTobaccoShops | ComicBookStores |
| DepartmentStores | DiscountStores | FleaMarketsAndBazaars |
| FurnitureStores | HomeImprovementStores | JewelryAndWatchesStores |
| KitchenwareStores | LiquorStores | MallsAndShoppingCenters |
| MensClothingStores | MusicStores | OutletStores |
| PetShops | PetSupplyStores | SchoolAndOfficeSupplyStores |
| ShoeStores | SportingGoodsStores | ToyAndGameStores |
| VitaminAndSupplementStores | WomensClothingStores |  |


## <a name="examples-of-local-categories-search"></a>Exempel på lokal kategorier sökning

I följande exempel resulterar GET enligt den `localCategories` parameter:

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=HotelsAndMotels`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=EatDrink`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Shop`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Hospitals`

Följande fråga begränsar antalet 'sjukhuset' resultaten till första tre som returnerades från den lokala företag i Bing:

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localCategories=Hospitals&count=3&offset=0`

I följande exempel JSON-svaret innehåller tre sjukhus i större Seattle-området:

````json
BingAPIs-TraceId: 68AFB51807C6485CAB8AAF20E232EFFF
BingAPIs-SessionId: F89E7B8539B34BF58AAF811485E83B20
X-MSEdge-ClientID: 1C44E64DBFAA6BCA1270EADDBE7D6A22
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 68AFB51807C6485CAB8AAF20E232EFFF Ref B: CO1EDGE0108 Ref C: 2018-10-22T18:52:28Z

{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": ""
   },
   "places": {
      "readLink": "https:\/\/www.bingapis.com\/api\/v7\/places\/search?q=",
      "totalEstimatedMatches": 0,
      "value": [
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.0",
            "name": "Overlake Hospital Medical Center",
            "url": "http:\/\/www.overlakehospital.org\/",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6204986572266,
               "longitude": -122.185829162598
            },
            "routablePoint": {
               "latitude": 47.6204986572266,
               "longitude": -122.185668945312
            },
            "address": {
               "streetAddress": "1035 116th Ave NE",
               "addressLocality": "Bellevue",
               "addressRegion": "WA",
               "postalCode": "98004",
               "addressCountry": "US",
               "neighborhood": "",
               "text": "1035 116th Ave NE, Bellevue, WA, 98004"
            },
            "telephone": "(425) 688-5000"
         },
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.1",
            "name": "Virginia Mason University Village Medical Center",
            "url": "https:\/\/virginiamason.org\/Seattle",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6095390319824,
               "longitude": -122.327941894531
            },
            "routablePoint": {
               "latitude": 47.6093978881836,
               "longitude": -122.328277587891
            },
            "address": {
               "streetAddress": "1100 9th Ave",
               "addressLocality": "Seattle",
               "addressRegion": "WA",
               "postalCode": "98101",
               "addressCountry": "US",
               "neighborhood": "University District",
               "text": "1100 9th Ave, Seattle, WA, 98101"
            },
            "telephone": "(206) 223-6600"
         },
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.2",
            "name": "Seattle Children’s Hospital",
            "url": "http:\/\/www.seattlechildrens.org\/",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6625061035156,
               "longitude": -122.283760070801
            },
            "routablePoint": {
               "latitude": 47.6631507873535,
               "longitude": -122.284614562988
            },
            "address": {
               "streetAddress": "4800 Sand Point Way NE",
               "addressLocality": "Seattle",
               "addressRegion": "WA",
               "postalCode": "98105",
               "addressCountry": "US",
               "neighborhood": "Laurelhurst",
               "text": "4800 Sand Point Way NE, Seattle, WA, 98105"
            },
            "telephone": "(206) 987-2000"
         }
      ],
      "searchAction": {

      }
   }
}
````

## <a name="next-steps"></a>Nästa steg
- [Geografisk search gränser](specify-geographic-search.md)
- [Frågor och svar](local-search-query-response.md)
- [Snabbstart iC#](quickstarts/local-quickstart.md)