---
title: Sök kategorier för API:et för sökning i Bing lokalt företag
titleSuffix: Azure Cognitive Services
description: Använd den här artikeln om du vill lära dig hur du anger sökkategorier för slutpunkten för Bing Local Business-sök-API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 56b94d66eb0929d2fd0ca74a1a631d229330adfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "69906399"
---
# <a name="search-categories-for-the-bing-local-business-search-api"></a>Sök kategorier för API:et för sökning i Bing lokalt företag

Med API:et för sökning i Bing kan du söka efter lokala företag i en mängd olika kategorier, med prioritet för resultat som stänger en användares plats. Du kan inkludera dessa sökningar `localCircularView` i `localMapView` sökningar tillsammans med [och parametrar](specify-geographic-search.md).


## <a name="toplevel-categories"></a>Toppklasskategorier 

Följande typer definierar huvudkategorier för sökning.  Mer än en kategori kan anges med hjälp av en kommaavgränsad lista som tilldelats parametern. `localCategories`  
- EatDrink (andra) 
- SeGör 
- Butik 
- HotellOchMotels 
- BankerOchCreditUnions 
- Parkering 
- Sjukhus 

## <a name="sub-categories"></a>Underkategorier
Underkategorier skickas på samma `localCategories`sätt som . Underkategorier är mer specifika kategorier. De är underordnade i den meningen att om du anger en kategori C och en av dess underkategorier S i samma kommaavgränsade lista, får du samma resultat som om du angav C ensam.

### <a name="eat-drink"></a>Ät dryck 
|  |  |  |  |
| - | - | - | - |
| BryggerierOchBrewPubs | CocktailLounges ( | AfrikanskaRestauranger |
| AmerikanskaRestauranger | Bagels | GrillRestauranger |
| Tavernor | SportBars | Barer |
| BarsGrillsOchPubs | BufféRestauranger| BelgiskaRestauranger | 
| BrittiskaRestauranger | CafeRestauranger | KaribienRestauranger |
| KinesiskaRestauranger | KaffeOchTea | Delikatesser | 
| DeliveryService | Diners | DiscountStores | 
| Munkar | Fastfood | FranskaRestauranger | 
| FrozenYogurt (svenska) | TyskaRestauranger | Stormarknader | 
| GrekiskaRestauranger | Livsmedelsaffär | HawaiianRestauranger | 
| UngerskaRestauranger | IceCreamAndFrozenDesserts | IndiskaRestauranger | 
| ItalienskaRestauranger | JapanskaRestauranger | Juice | 
| KoreanskaRestauranger | LiquorStores (SpritButiker) | MexikanskaRestauranger |
| MiddleEasternRestaurants | Pizza | PolskaRestauranger | 
| PortugisiskaRestauranger | Pretzels | Restauranger | 
| RyskaOchUkrainianRestaurants | Smörgåsar | SeafoodRestauranger | 
| SpanskaRestauranger | SteakHouseRestauranger | SushiRestauranger | 
| Takeaway | ThaiRestauranger | TurkiskaRestauranger | 
| Vegetariska OchVeganRestauranger | VietnamesiskaRestauranger|  |
 
### <a name="see-do"></a>Se Gör 
|  |  |  |
| -- | -- | -- |
| Nöjesparker | Attraktioner | Karnevaler |
| Kasinon | LandmärkenOchhistoriskaplatser | MiniatyrGolfKursar |
| Biotheaters | Museer | Parker |
| SightseeingTours | TuristInformation | Djurparker |
 
### <a name="shop"></a>Butik 
|  |  |  |
| -- | -- | -- |
| AntiqueStores (AntiqueStores) | Bokhandlar | CDAndRecordStores |
| BarnsClothingStores | CigarAndTobaccoButiker | ComicBookStores (På andra hand) |
| AvdelningButiker | DiscountStores | LoppmarknaderOchBazaars |
| MöblerButiker | HemImprovementButiker | SmyckenOchWatchesStores |
| KöksutrustningButiker | LiquorStores (SpritButiker) | GalleriorOchShoppingCenters |
| MensClothingStores | MusicStores (MusikButiker) | OutletStores (Utloppshandel) |
| PetShops (på nytt) | PetSupplyStores | SkolaOchOfficeSupplyStores |
| ShoeStores (Skoaffärer) | SportingGoodsButiker | ToyAndGameStores |
| VitaminAndSupplementButiker | WomensClothingStores |  |


## <a name="examples-of-local-categories-search"></a>Exempel på sökning i lokala kategorier

Följande exempel FÅ resultat `localCategories` enligt parametern:

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=HotelsAndMotels`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=EatDrink`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Shop`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Hospitals`

Följande fråga begränsar antalet "sjukhusresultat" till de tre första som returneras från API:et för lokal företagssökning på Bing:

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localCategories=Hospitals&count=3&offset=0`

Följande exempel JSON svar omfattar tre sjukhus i större Seattle-området:

```json
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
```

## <a name="next-steps"></a>Nästa steg
- [Geografiska sökgränser](specify-geographic-search.md)
- [Fråga och svar](local-search-query-response.md)
- [Snabbstart i C #](quickstarts/local-quickstart.md)
