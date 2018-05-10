---
title: Hur du söker efter en adress med hjälp av Azure Maps söktjänsten | Microsoft Docs
description: Lär dig att söka efter en adress med hjälp av tjänsten Azure Maps Search
services: azure-maps
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
ms.openlocfilehash: 4a87c0d0a487706209acc1c6a4ded92e6aab8f9e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-find-an-address-using-the-azure-maps-search-service"></a>Så här hittar du en adress som använder tjänsten Azure Maps search

Tjänsten Maps search är en RESTful uppsättning API: er som utformats för utvecklare att söka efter adresser, platser, punkter av intresse, företag och andra geografisk information. Tjänsten tilldelar latitud/longitud till en specifik adress mellan gata, geografiska funktion eller intressant (POI). Latitud och longitud värden som returneras av sökningen kan användas som parametrar i andra Maps-tjänster som vägen och trafikflöde.

## <a name="prerequisites"></a>Förutsättningar

Om du vill att alla anrop till tjänsten Maps API: er, behöver du en Maps-konto och nyckel. Mer information om hur du skapar ett konto och hämta en nyckel finns [hur du hanterar din Azure Maps-konto och nycklar](how-to-manage-account-keys.md).

Den här artikeln används den [Postman app](https://www.getpostman.com/apps) att skapa REST-anrop. Du kan använda alla API-utvecklingsmiljö som du föredrar. 


## <a name="using-fuzzy-search"></a>Fuzzy sökning

Standard-API: t för söktjänsten [fuzzy Sök](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy), som hanterar indata av en kombination av adress eller POI-token. Den här sökningen API är kanoniska 'enradig sökningen' och är användbart när du inte vet vad din användarindata som en sökfråga. Fuzzy Sök API är en kombination av POI-sökning och geokodning. API: N kan också vägas med kontextuella position (lat./celligt. Koppla), fullständigt begränsas av en koordinaten och RADIUS-, eller det kan köras mer generellt utan någon geo börvärdessignalerna ankarpunkten.

De flesta sökningar som standard ' maxFuzzyLevel = 1' för att få prestanda och minska ovanliga resultat. Det här standardvärdet kan åsidosättas vid behov per begäran genom att passera i Frågeparametern ' maxFuzzyLevel = 2' eller '3'.

### <a name="search-for-an-address-using-fuzzy-search"></a>Söka efter en adress Fuzzy sökning

1. Öppna appen Postman, klicka på ny | Skapa ny och välj **GET-begäran**. Ange ett namn för begäran med **Fuzzy Sök**, markera en samling eller en mapp att spara ändringarna och klicka på **spara**.

2. På fliken Builder väljer du den **hämta** HTTP-metoden och ange URL: en begäran för API-slutpunkt.

    ![Fuzzy sökning ](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Parameter | Föreslaget värde |
    |---------------|------------------------------------------------|
    | HTTP-metod | HÄMTA |
    | Fråge-URL | https://atlas.microsoft.com/search/fuzzy/json? |
    | Auktorisering | Ingen autentisering |

    Den **json** attribut i en URL-sökväg anger svarsformatet. Du använder json i den här artikeln för att underlätta användning och läsbarhet. Du kan hitta tillgängliga svar-format i den **hämta Sök Fuzzy** definition av [Maps funktionella API-referens] (https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

3. Klicka på **Params**, och ange följande nyckel / värde-par som används som frågan eller sökväg parametrar i URL: en för begäran:

    ![Fuzzy sökning ](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Nyckel | Värde |
    |------------------|-------------------------|
    | API-version | 1.0 |
    | prenumerationen-nyckel | \<din Azure Maps-nyckel\> |
    | DocumentDB | pizza |

4. Klicka på **skicka** och granska brödtext för svar. 

    Tvetydig frågesträngen för ”pizza” returneras 10 punkter av intresse (POI) resultat med kategorier som omfattas av i ”pizza” och ”restaurang”. Varje resultat returnerar en gatuadress, latitud / longitudvärden, visa port och startpunkter för platsen.
    
    Resultaten varieras för den här frågan inte kopplad till en viss referensplats. Du kan använda den **countrySet** med parametern endast land som ditt program måste täckning, som standard att söka i hela världen potentiellt returnering onödiga resultat.

5. Lägg till följande nyckel / värde-par till den **Params** avsnittet och klicka på **skicka**:

    | Nyckel | Värde |
    |------------------|-------------------------|
    | countrySet | USA |
    
    Resultatet är nu begränsas av landskoden och frågan returnerar pizza hotell i USA.
    
    För att ge resultat som är inriktade på en viss plats, kan du fråga en punkt intressanta och använda returnerade latitud och longitudvärden i anrop till Fuzzy söktjänsten. I så fall måste du används Search-tjänsten för att returnera platsen för Seattle utrymme nålen och används lat. / celligt. värden om du vill att sökningen.
    
4. I parametrar, anger du följande nyckel / värde-par och klicka på **skicka**:

    ![Fuzzy sökning ](./media/how-to-search-for-address/fuzzy_search_latlon.png)
    
    | Nyckel | Värde |
    |-----|------------|
    | lat | 47.62039 |
    | celligt | -122.34928 |

## <a name="search-for-address-properties-and-coordinates"></a>Sök efter egenskaper och koordinater 

Du kan skicka en fullständig eller partiell gatuadress till Sök adresser API och ett svar som innehåller detaljerade egenskaper, till exempel namnet eller delfältet samt namngivna värden i latitud och longitud.

1. I Postman, klickar du på **ny begäran** | **GET-begäran** och ger den namnet **adress Sök**.
2. På fliken Builder väljer du den **hämta** HTTP-metod ange URL: en begäran för API-slutpunkt och välj ett authorization protocol eventuella.

    ![Sökningen efter adresser ](./media/how-to-search-for-address/address_search_url.png)
    
    | Parameter | Föreslaget värde |
    |---------------|------------------------------------------------|
    | HTTP-metod | HÄMTA |
    | Fråge-URL | https://atlas.microsoft.com/search/address/json? |
    | Auktorisering | Ingen autentisering |

2. Klicka på **Params**, och ange följande nyckel / värde-par som används som frågan eller sökväg parametrar i URL: en för begäran:
    
    ![Sökningen efter adresser ](./media/how-to-search-for-address/address_search_params.png)
    
    | Nyckel | Värde |
    |------------------|-------------------------|
    | API-version | 1.0 |
    | prenumerationen-nyckel | \<din Azure Maps-nyckel\> |
    | DocumentDB | 400 breda St, Seattle, WA 98109 |
    
3. Klicka på **skicka** och granska brödtext för svar. 
    
    I så fall måste du angett en fullständig adress-fråga och få ett enskilt resultat i brödtext för svar. 
    
4. Parametrar, redigera frågesträngen följande värde:
    ```
        400 Broad, Seattle
    ```

5. Lägg till följande nyckel / värde-par till den **Params** avsnittet och klicka på **skicka**:

    | Nyckel | Värde |
    |-----|------------|
    | typeahead | true |

    Den **typeahead** flaggan talar om Sök-API-adress för att behandla frågan som en partiell indata och returnerar en matris med förutsägande värden.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Sök efter en gatuadress omvänd sökning adress
1. I Postman, klickar du på **ny begäran** | **GET-begäran** och ger den namnet **omvänd sökning adress**.

2. På fliken Builder väljer du den **hämta** HTTP-metoden och ange URL: en begäran för API-slutpunkt.
    
    ![Adress för omvänd sökning URL ](./media/how-to-search-for-address/reverse_address_search_url.png)
    
    | Parameter | Föreslaget värde |
    |---------------|------------------------------------------------|
    | HTTP-metod | HÄMTA |
    | Fråge-URL | https://atlas.microsoft.com/search/address/reverse/json? |
    | Auktorisering | Ingen autentisering |
    
2. Klicka på **Params**, och ange följande nyckel / värde-par som används som frågan eller sökväg parametrar i URL: en för begäran:
    
    ![Omvänd adress sökparametrarna ](./media/how-to-search-for-address/reverse_address_search_params.png)
    
    | Nyckel | Värde |
    |------------------|-------------------------|
    | API-version | 1.0 |
    | prenumerationen-nyckel | \<din Azure Maps-nyckel\> |
    | DocumentDB | 47.59093,-122.33263 |
    
3. Klicka på **skicka** och granska brödtext för svar. 
    
    Svaret innehåller POI-posten för Safeco Field med en poi-kategori av ”stadium”. 
    
4. Lägg till följande nyckel / värde-par till den **Params** avsnittet och klicka på **skicka**:

    | Nyckel | Värde |
    |-----|------------|
    | nummer | true |

    Om den [nummer](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) frågeparameter skickas med begäran, svaret kan omfatta sida av gata (åt vänster och höger) och en offset position för talet.
    
5. Lägg till följande nyckel / värde-par till den **Params** avsnittet och klicka på **skicka**:

    | Nyckel | Värde |
    |-----|------------|
    | spatialKeys | true |

    När den [spatialKeys](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) frågeparameter har angetts, svaret innehåller egna geo-spatiala viktig information för en angiven plats.

6. Lägg till följande nyckel / värde-par till den **Params** avsnittet och klicka på **skicka**:

    | Nyckel | Värde |
    |-----|------------|
    | returnSpeedLimit | true |
    
    När den [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) frågeparameter har angetts returnerar svaret för anslagna hastighetsgränsen.

7. Lägg till följande nyckel / värde-par till den **Params** avsnittet och klicka på **skicka**:

    | Nyckel | Värde |
    |-----|------------|
    | returnRoadUse | true |

    När den [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) frågeparameter har angetts, svaret returnerar väg Använd matris för reversegeocodes på Gata nivå.

8. Lägg till följande nyckel / värde-par till den **Params** avsnittet och klicka på **skicka**:

    | Nyckel | Värde |
    |-----|------------|
    | roadUse | true |

    Du kan begränsa omvänd geocode frågan till en viss typ av väg användning med den [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) Frågeparametern.
    
## <a name="search-for-the-cross-street-using-reverse-address-cross-street-search"></a>Sök efter mellan gata omvänd adress mellan gata sökning

1. I Postman, klickar du på **ny begäran** | **GET-begäran** och ger den namnet **omvänd adress mellan gata Sök**.

2. På fliken Builder väljer du den **hämta** HTTP-metoden och ange URL: en begäran för API-slutpunkt.
    
    ![Omvänd adress mellan gata sökning ](./media/how-to-search-for-address/reverse_address_search_url.png)
    
    | Parameter | Föreslaget värde |
    |---------------|------------------------------------------------|
    | HTTP-metod | HÄMTA |
    | Fråge-URL | https://atlas.microsoft.com/search/address/reverse/crossstreet/json? |
    | Auktorisering | Ingen autentisering |
    
3. Klicka på **Params**, och ange följande nyckel / värde-par som används som frågan eller sökväg parametrar i URL: en för begäran:
    
    | Nyckel | Värde |
    |------------------|-------------------------|
    | API-version | 1.0 |
    | prenumerationen-nyckel | \<din Azure Maps-nyckel\> |
    | DocumentDB | 47.59093,-122.33263 |
    
4. Klicka på **skicka** och granska brödtext för svar. 

## <a name="next-steps"></a>Nästa steg
- Utforska den [Azure Maps söktjänsten](https://docs.microsoft.com/rest/api/maps/search) API-dokumentationen 
