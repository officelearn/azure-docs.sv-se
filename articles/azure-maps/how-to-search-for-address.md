---
title: Sök efter en plats med Azure Maps Sök tjänster | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du söker efter en plats med hjälp av Microsoft Azure Maps-Search Service för kodning och omvänt geografi.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cf0e5267885df1ace51271c53bb2d68ee5002f00
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80335433"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>Sök efter en plats med Azure Maps Sök tjänster

Azure Maps [search service](https://docs.microsoft.com/rest/api/maps/search) är en uppsättning RESTful-API: er som är utformade för att hjälpa utvecklare att söka efter adresser, platser, företags listor efter namn eller kategori och annan geografisk information. Utöver stöd för traditionell landskod kan tjänster också omvända koder för hår koder och korsade gator utifrån latitud och longitud. Värdena för latitud och longitud som returneras av sökningen kan användas som parametrar i andra Azure Maps tjänster som [väg](https://docs.microsoft.com/rest/api/maps/route) -och [väder](https://docs.microsoft.com/rest/api/maps/weather) tjänster.

I den här artikeln får du lära dig hur du:

* Begär latitud-och longitud-koordinater för en adress (kod för den här adressen) genom att använda [Sök adress-API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)
* Sök efter en adress eller ett intresse punkt (POI) med hjälp av [API för fuzzy search](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* Sök efter en adress tillsammans med egenskaper och koordinater
* Göra en [Omvänd adresss ökning](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) för att översätta koordinatens placering till gatuadress
* Sök efter en kors gata med [Sök adress omvänt kors gatan API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här artikeln måste du först skapa ett Azure Maps-konto och hämta konto prenumerations nyckeln. Följ instruktionerna i [skapa ett konto](quick-demo-map-app.md#create-an-account-with-azure-maps) om du vill skapa en Azure Maps konto prenumeration och följ stegen i [Hämta primär nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account) för att hämta den primära nyckeln för ditt konto. Mer information om autentisering i Azure Maps finns i [hantera autentisering i Azure Maps](./how-to-manage-authentication.md).

I den här artikeln används [Postman-appen](https://www.getpostman.com/apps) för att bygga rest-anrop. Du kan använda valfri API utvecklings miljö som du föredrar.

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>Begär latitud och longitud för en adress (kod)

I det här exemplet använder vi Azure Maps [Hämta Sök adress-API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) för att konvertera gatuadressen till latitud-och longitud-koordinater. Du kan skicka en fullständig eller delvis gatuadress till API: et och få ett svar som innehåller detaljerade adress egenskaper som gata, post nummer och land/region samt positions värden i latitud och longitud.

Om du har en uppsättning adresser till landskod, kan du använda [batch-API för posts ökning](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatch) för att skicka en batch med frågor i ett enda API-anrop.

1. I Postman klickar du på **ny begäran**  |  **Hämta begäran** och namnge den **adressen Sök**.

2. På fliken Builder väljer du metoden **Hämta** http, anger fråge-URL: en för API-slutpunkten och väljer ett Authorization-protokoll, om det finns några.

![Adresss ökning](./media/how-to-search-for-address/address_search_url.png)

| Parameter | Föreslaget värde |
|---------------|------------------------------------------------| 
| HTTP-metod | HÄMTA |
| Begärans-URL | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) | 
| Auktorisering | Ingen autentisering |

3. Klicka på **params**och ange följande nyckel/värde-par som ska användas som fråga-eller Sök vägs parametrar i fråge-URL: 

![Adresss ökning](./media/how-to-search-for-address/address_search_params.png) 

| Tangent | Värde | 
|------------------|-------------------------| 
| api-version | 1.0 | 
| prenumerations nyckel | \<your Azure Maps key\> | 
| DocumentDB | 400 bred St, Seattle, WA 98109 | 

4. Klicka på **Skicka** och granska svars texten. 

I det här fallet angav du en fullständig adress fråga och får ett enda resultat i svars texten. 

5. I params redigerar du frågesträngen till följande värde: 

    ```plaintext 
        400 Broad, Seattle 
    ``` 

6. Lägg till följande nyckel/värde-par i avsnittet **params** och klicka på **Skicka**: 

| Tangent | Värde | 
|-----|------------| 
| typeahead | true | 

Flaggan **typeahead** visar API för adresss ökning för att behandla frågan som en del indata och returnera en matris med förutsägande värden.

## <a name="using-fuzzy-search-api"></a>Använda API för fuzzy-sökning

Azure Maps[ API för oskarp sökning](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) är en rekommenderad tjänst som du kan använda när du inte vet vad dina användarindata är för en Sök fråga. API: et kombinerar en POI-sökning (Point of Interest) och en kanonisk "enkel rads sökning". API: t kan till exempel hantera indata för valfri kombination av adress eller POI-token. Det kan också vägas med en kontext position (lat./lon. par), helt begränsad av en koordinat och radie, eller körs oftare utan någon fäst punkt för geo-förskjutning.

De flesta Sök frågor är standard för `maxFuzzyLevel=1` att få prestanda och minska ovanliga resultat. Standardvärdet kan åsidosättas efter behov per begäran genom att skicka i Frågeparametern `maxFuzzyLevel=2` eller `3` .

### <a name="search-for-an-address-using-fuzzy-search"></a>Sök efter en adress med hjälp av en Fuzzy-sökning

1. Öppna Postman-appen, klicka på ny | Skapa nytt och välj **Hämta begäran**. Ange ett namn på den **oskarpa sökningen**, Välj en samling eller mapp att spara den i och klicka på **Spara**.

2. På fliken Builder väljer du metoden **Hämta** http och anger fråge-URL: en för API-slutpunkten.

    ![Fuzzy-sökning](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Parameter | Föreslaget värde |
    |---------------|------------------------------------------------|
    | HTTP-metod | HÄMTA |
    | Begärans-URL | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | Auktorisering | Ingen autentisering |

    **JSON** -ATTRIBUTET i URL-sökvägen bestämmer svars formatet. I den här artikeln används JSON för enkel användning och läsbarhet. Du hittar tillgängliga svars format i den **Hämta sökningens fuzzy** -definition av [Maps funktions-API-referens](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

3. Klicka på **params**och ange följande nyckel/värde-par som ska användas som fråga-eller Sök vägs parametrar i fråge-URL:

    ![Fuzzy-sökning](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Tangent | Värde |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | prenumerations nyckel | \<your Azure Maps key\> |
    | DocumentDB | pizza |

4. Klicka på **Skicka** och granska svars texten.

    Den tvetydiga frågesträngen för "pizza" returnerade 10 [punkter av intresse resultat](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) (POI) i båda kategorierna "pizza" och "restaurang". Varje resultat returnerar en gatuadress, latitud-och longitud-värden, visar port och start punkter för platsen.
  
    Resultaten varierar för den här frågan, inte knutna till någon särskild referens plats. Du kan använda parametern **countrySet** om du bara vill ange de länder/regioner som programmet behöver täckning för. Standard beteendet är att söka hela världen och eventuellt returnera onödiga resultat.

5. Lägg till följande nyckel/värde-par i avsnittet **params** och klicka på **Skicka**:

    | Tangent | Värde |
    |------------------|-------------------------|
    | countrySet | USA |
  
    Resultaten begränsas nu av lands koden och frågan returnerar pizza restauranger i USA.
  
    Om du vill ange resultat för en plats kan du fråga en orienterings punkt och använda värdena för latitud och longitud i ditt anrop till fuzzy Search-tjänsten. I detta fall använde du Sök tjänsten för att returnera platsen för bladet Seattle, Seattle och använde lat. / lon. värden för att orientera sökningen.
  
6. Ange följande nyckel/värde-par i params och klicka på **Skicka**:

    ![Fuzzy-sökning](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | Tangent | Värde |
    |-----|------------|
    | koder | 47,620525 |
    | lon | – 122,349274 |


## <a name="search-for-a-street-address-using-reverse-address-search"></a>Sök efter en gatuadress med omvänd adresss ökning

Azure Maps [Get search Address reversed API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) hjälper till att översätta en koordinat (till exempel: 37,786505,-122,3862) till en human gatuadress. Oftast behövs detta för att spåra program där du får ett GPS-flöde från enheten eller till gången och vill veta vilken adress som koordinaten finns i.
Om du har en uppsättning koordinerande platser för omvänd landskod, kan du använda [posts öknings adress omvänt batch-API](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatch) för att skicka en batch med frågor i ett enda API-anrop.


1. I Postman klickar du på **ny begäran**  |  **Hämta begäran** och ge den namnet **Omvänd adress Sök**.

2. På fliken Builder väljer du metoden **Hämta** http och anger fråge-URL: en för API-slutpunkten.
  
    ![URL för omvänd adresss ökning](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parameter | Föreslaget värde |
    |---------------|------------------------------------------------|
    | HTTP-metod | HÄMTA |
    | Begärans-URL | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | Auktorisering | Ingen autentisering |
  
3. Klicka på **params**och ange följande nyckel/värde-par som ska användas som fråga-eller Sök vägs parametrar i fråge-URL:
  
    ![Sök parametrar för omvänd adress](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | Tangent | Värde |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | prenumerations nyckel | \<your Azure Maps key\> |
    | DocumentDB | 47.591180,-122,332700 |
  
4. Klicka på **Skicka** och granska svars texten.

    Svaret innehåller viktig adress information om Safeco-fältet.
  
5. Lägg till följande nyckel/värde-par i avsnittet **params** och klicka på **Skicka**:

    | Tangent | Värde |
    |-----|------------|
    | nummer | true |

    Om [värdet](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) för Frågeparametern skickas med begäran kan svaret omfatta sidan av gatan (vänster eller höger) och även en förskjutnings position för det talet.
  
6. Lägg till följande nyckel/värde-par i avsnittet **params** och klicka på **Skicka**:

    | Tangent | Värde |
    |-----|------------|
    | returnSpeedLimit | true |
  
    När [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) -Frågeparametern har angetts returnerar svaret den anslagna hastighets gränsen.

7. Lägg till följande nyckel/värde-par i avsnittet **params** och klicka på **Skicka**:

    | Tangent | Värde |
    |-----|------------|
    | returnRoadUse | true |

    När Frågeparametern [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) anges, returnerar svaret vägens användnings mat ris för omvända omvändare på gatu nivå.

8. Lägg till följande nyckel/värde-par i avsnittet **params** och klicka på **Skicka**:

    | Tangent | Värde |
    |-----|------------|
    | roadUse | true |

    Du kan begränsa den omvända koden till en speciell typ av väg med hjälp av Frågeparametern [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) .
  
## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>Sök efter kors gatan med omvänd adress över gata-sökning

1. Klicka på **ny begäran**  |  **Get-begäran** i Postman och ge den namnet **Omvänd adress mellan gata**.

2. På fliken Builder väljer du metoden **Hämta** http och anger fråge-URL: en för API-slutpunkten.
  
    ![Omvänd adress, korsad gata-sökning](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parameter | Föreslaget värde |
    |---------------|------------------------------------------------|
    | HTTP-metod | HÄMTA |
    | Begärans-URL | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | Auktorisering | Ingen autentisering |
  
3. Klicka på **params**och ange följande nyckel/värde-par som ska användas som fråga-eller Sök vägs parametrar i fråge-URL:
  
    | Tangent | Värde |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | prenumerations nyckel | \<your Azure Maps key\> |
    | DocumentDB | 47.591180,-122,332700 |
  
4. Klicka på **Skicka** och granska svars texten.

## <a name="next-steps"></a>Nästa steg

- Utforska [Azure Maps Search Service REST API-dokumentationen](https://docs.microsoft.com/rest/api/maps/search).
- Lär dig mer om [Azure Maps search service bästa praxis](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search) och hur du optimerar dina frågor.
