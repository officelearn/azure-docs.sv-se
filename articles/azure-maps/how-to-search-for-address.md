---
title: Sök efter en plats med Hjälp av Azure Maps Search-tjänster | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du söker efter en plats med hjälp av Söktjänsten i Microsoft Azure Maps för geokodning och omvänd geokodning.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cf0e5267885df1ace51271c53bb2d68ee5002f00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335433"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>Sök efter en plats med hjälp av Azure Maps Search-tjänster

Azure Maps [Search Service](https://docs.microsoft.com/rest/api/maps/search) är en uppsättning RESTful API:er som utformats för att hjälpa utvecklare att söka efter adresser, platser, företagsuppgifter efter namn eller kategori och annan geografisk information. Förutom att stödja traditionell geokodning kan tjänster också vända geokodadresser och korsgator baserat på breddgrader och longituder. Latitude- och longitudvärden som returneras av sökningen kan användas som parametrar i andra Azure Maps-tjänster som [Route-](https://docs.microsoft.com/rest/api/maps/route) och [Weather-tjänster.](https://docs.microsoft.com/rest/api/maps/weather)

I den här artikeln får du lära dig hur du:

* Begär latitud- och longitudkoordinater för en adress (geografisk kodadressplats) med hjälp av [API för sökadress]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)
* Sök efter en adress eller intressepunkt (POI) med hjälp av [Fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* Sök efter en adress tillsammans med egenskaper och koordinater
* Gör en [omvänd adresssökning](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) för att översätta koordinatplats till gatuadress
* Sök efter en tvärgata med [sökadress Reverse Cross Street API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)

## <a name="prerequisites"></a>Krav

För att slutföra stegen i den här artikeln måste du först skapa ett Azure Maps-konto och få dig kartor konto prenumerationsnyckel. Följ instruktionerna i [Skapa ett konto](quick-demo-map-app.md#create-an-account-with-azure-maps) för att skapa en Azure Maps-kontoprenumeration och följ stegen för att få [primärnyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account) för att få den primära nyckeln för ditt konto. Mer information om autentisering i Azure Maps finns [i hantera autentisering i Azure Maps](./how-to-manage-authentication.md).

I den här artikeln används [Postman-appen](https://www.getpostman.com/apps) för att skapa REST-anrop. Du kan använda valfri API-utvecklingsmiljö som du föredrar.

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>Begär latitud och longitud för en adress (geokodning)

I det här exemplet använder vi Azure Maps [Get Search Address API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) för att konvertera gatuadress till latitud- och longitudkoordinater. Du kan skicka en fullständig eller partiell gatuadress till API:et och få ett svar som innehåller detaljerade adressegenskaper som gata, postnummer och land/region, samt positionsvärden i latitud och longitud.

Om du har en uppsättning adresser till geokod kan du använda [Batch-API för postsökadress](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatch) för att skicka en grupp frågor i ett enda API-anrop.

1. I Postman klickar du på **Ny begäran** | **hämta begäran** och namnger den **Adresssökning**.

2. På fliken Builder väljer du metoden **GET** HTTP, anger url:en för begäran för API-slutpunkten och väljer ett auktoriseringsprotokoll, om något.

![Sök adress](./media/how-to-search-for-address/address_search_url.png)

| Parameter | Föreslaget värde |
|---------------|------------------------------------------------| 
| HTTP-metod | HÄMTA |
| URL för begäran | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) | 
| Auktorisering | Ingen Auth |

3. Klicka på **Params**och ange följande nyckel-/värdepar som ska användas som fråge- eller sökvägsparametrar i begäran OM URL: 

![Sök adress](./media/how-to-search-for-address/address_search_params.png) 

| Nyckel | Värde | 
|------------------|-------------------------| 
| api-version | 1.0 | 
| prenumerationsnyckel | \<din Azure Maps-nyckel\> | 
| DocumentDB | 400 Breda St, Seattle, WA 98109 | 

4. Klicka på **Skicka** och granska svarstexten. 

I det här fallet har du angett en fullständig adressfråga och få ett enda resultat i svarstexten. 

5. I Params redigerar du frågesträngen till följande värde: 

    ```plaintext 
        400 Broad, Seattle 
    ``` 

6. Lägg till följande nyckel / värde par i **Params** avsnittet och klicka på **Skicka:** 

| Nyckel | Värde | 
|-----|------------| 
| typeahead (typeahead) | true | 

**Typeahead-flaggan** talar om för adresssök-API:et att behandla frågan som en partiell indata och returnera en matris med förutsägande värden.

## <a name="using-fuzzy-search-api"></a>Använda Api för fuzzy-sökning

Azure Maps[ Fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) rekommenderas att använda när du inte vet vad dina användarindata är för en sökfråga. API:et kombinerar poi-sökning (Point of Interest) och geokodning till en kanonisk "enkelradssökning". API:et kan till exempel hantera indata för valfri adress eller POI-tokenkombination. Det kan också viktas med en kontextuell position (lat./lon. par), helt begränsad av en koordinat och radie, eller utförs mer allmänt utan geo partisk ankarpunkt.

De flesta sökfrågor `maxFuzzyLevel=1` som standard får prestanda och minskar ovanliga resultat. Den här standardinställningen kan åsidosättas efter behov `maxFuzzyLevel=2` `3`per begäran genom att skicka in frågeparametern eller .

### <a name="search-for-an-address-using-fuzzy-search"></a>Sök efter en adress med Hjälp av Fuzzy Search

1. Öppna postman-appen, klicka på Ny | Skapa nytt och välj **GET-begäran**. Ange ett begärandenamn för **Fuzzy-sökning,** välj en samling eller mapp som du vill spara den i och klicka på **Spara**.

2. På fliken Builder väljer du METODEN **GET** HTTP och anger url:en för begäran för din API-slutpunkt.

    ![Fuzzy Sök](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Parameter | Föreslaget värde |
    |---------------|------------------------------------------------|
    | HTTP-metod | HÄMTA |
    | URL för begäran | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | Auktorisering | Ingen Auth |

    **Json-attributet** i URL-sökvägen bestämmer svarsformatet. Denna artikel använder json för användarvänlighet och läsbarhet. Du hittar tillgängliga svarsformat i **definitionen Get Search Fuzzy** av [referensen Maps Functional API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

3. Klicka på **Params**och ange följande nyckel-/värdepar som ska användas som fråge- eller sökvägsparametrar i begäran OM URL:

    ![Fuzzy Sök](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Nyckel | Värde |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | prenumerationsnyckel | \<din Azure Maps-nyckel\> |
    | DocumentDB | Pizza |

4. Klicka på **Skicka** och granska svarstexten.

    Den tvetydiga frågesträngen för "pizza" returnerade 10 [punkt av intresse resultat](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) (POI) i både "pizza" och "restaurang" kategorier. Varje resultat returnerar en gatuadress, latitud och longitudvärden, vyport och startpunkter för platsen.
  
    Resultaten är varierande för den här frågan, inte knutna till någon särskild referensplats. Du kan använda parametern **countrySet** för att bara ange de länder/regioner som ditt program behöver täckning för. Standardbeteendet är att söka i hela världen, vilket kan ge onödiga resultat.

5. Lägg till följande nyckel / värde par i **Params** avsnittet och klicka på **Skicka:**

    | Nyckel | Värde |
    |------------------|-------------------------|
    | landSet | USA |
  
    Resultaten avgränsas nu av landskoden och frågan returnerar pizzarestauranger i USA.
  
    Om du vill ge resultat för en plats kan du fråga efter en intressant plats och använda de returnerade latitud- och longitudvärdena i samtalet till tjänsten Fuzzy Search. I det här fallet använde du söktjänsten för att returnera platsen för Seattle Space Needle och använde lat. / lon. värden för att orientera sökningen.
  
6. I Params anger du följande nyckel- och värdepar och klickar på **Skicka:**

    ![Fuzzy Sök](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | Nyckel | Värde |
    |-----|------------|
    | Lat | 47.620525 |
    | Lon | -122.349274 |


## <a name="search-for-a-street-address-using-reverse-address-search"></a>Sök efter en gatuadress med omvänd adresssökning

Azure Maps [Get Search Address Reverse API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) hjälper till att översätta en koordinat (exempel: 37.786505, -122.3862) till en mänsklig begriplig gatuadress. Oftast behövs detta för att spåra program där du får en GPS-matning från enheten eller tillgången och vill veta vilken adress där koordinaten finns.
Om du har en uppsättning koordinatplatser för att återföra geokod kan du använda [Batch-API](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatch) för omvänd batch efter sökadress för att skicka en grupp med frågor i ett enda API-anrop.


1. I Postman klickar du på **Ny begäran** | **HÄMTA begäran** och namnger den Omvänd **adresssökning**.

2. På fliken Builder väljer du METODEN **GET** HTTP och anger url:en för begäran för din API-slutpunkt.
  
    ![Url för omvänd adresssökning](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parameter | Föreslaget värde |
    |---------------|------------------------------------------------|
    | HTTP-metod | HÄMTA |
    | URL för begäran | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | Auktorisering | Ingen Auth |
  
3. Klicka på **Params**och ange följande nyckel-/värdepar som ska användas som fråge- eller sökvägsparametrar i begäran OM URL:
  
    ![Sök parametrar för omvänd adress](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | Nyckel | Värde |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | prenumerationsnyckel | \<din Azure Maps-nyckel\> |
    | DocumentDB | 47.591180,-122.332700 |
  
4. Klicka på **Skicka** och granska svarstexten.

    Svaret innehåller viktig adressinformation om Safeco Field.
  
5. Lägg till följande nyckel / värde par i **Params** avsnittet och klicka på **Skicka:**

    | Nyckel | Värde |
    |-----|------------|
    | nummer | true |

    Om [parametern talfråga](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) skickas med begäran kan svaret innehålla sidan av gatan (vänster eller höger) och även en förskjutningsposition för det numret.
  
6. Lägg till följande nyckel / värde par i **Params** avsnittet och klicka på **Skicka:**

    | Nyckel | Värde |
    |-----|------------|
    | returneraRSpeedLimit | true |
  
    När parametern [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) ställs in returnerar svaret den bokförda hastighetsgränsen.

7. Lägg till följande nyckel / värde par i **Params** avsnittet och klicka på **Skicka:**

    | Nyckel | Värde |
    |-----|------------|
    | returnRoadUse | true |

    När frågeparametern [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) har angetts returnerar svaret väganvändningsmatrisen för omvända geokoder på gatunivå.

8. Lägg till följande nyckel / värde par i **Params** avsnittet och klicka på **Skicka:**

    | Nyckel | Värde |
    |-----|------------|
    | vägAnvänd | true |

    Du kan begränsa den omvända geokodfrågan till en viss typ av väg med hjälp av frågeparametern [roadUse.](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
  
## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>Sök efter cross street med omvänd adress Cross Street Search

1. I Postman klickar du på **Ny begäran** | **HÄMTA begäran** och namnger den Omvänd adress Cross Street **Search**.

2. På fliken Builder väljer du METODEN **GET** HTTP och anger url:en för begäran för din API-slutpunkt.
  
    ![Omvänd adress Cross Street Search](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parameter | Föreslaget värde |
    |---------------|------------------------------------------------|
    | HTTP-metod | HÄMTA |
    | URL för begäran | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | Auktorisering | Ingen Auth |
  
3. Klicka på **Params**och ange följande nyckel-/värdepar som ska användas som fråge- eller sökvägsparametrar i begäran OM URL:
  
    | Nyckel | Värde |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | prenumerationsnyckel | \<din Azure Maps-nyckel\> |
    | DocumentDB | 47.591180,-122.332700 |
  
4. Klicka på **Skicka** och granska svarstexten.

## <a name="next-steps"></a>Nästa steg

- Utforska [AZURE Maps Search Service REST API-dokumentationen](https://docs.microsoft.com/rest/api/maps/search).
- Lär dig mer om [metodtips för Azure Maps Search Service](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search) och hur du optimerar dina frågor.
