---
title: Hitta en adress med hjälp av Azure Maps Search-tjänsten | Microsoft Docs
description: Lär dig hur du söker efter en adress med hjälp av Azure Maps Search-tjänsten
author: walsehgal
ms.author: v-musehg
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 56194bcfb9531def87a9918ad442a2927413c964
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432964"
---
# <a name="find-an-address-using-the-azure-maps-search-service"></a>Hitta en adress med hjälp av Azure Maps Search-tjänsten

Sök tjänsten Maps är en uppsättning RESTful-API: er som är utformade för utvecklare för att söka efter adresser, platser, punkter av intresse, företags listor och annan geografisk information. Tjänsten tilldelar en latitud/longitud till en viss adress, ett kors gatan, en geografisk funktion eller en orienterings punkt (POI). Värdena för latitud och longitud som returneras av sökningen kan användas som parametrar i andra Maps-tjänster som flödes-och trafikflöde.

I den här artikeln får du lära dig hur du:

* Sök efter en adress med hjälp av [API för fuzzy-sökning](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* Sök efter en adress tillsammans med egenskaper och koordinater
* Gör en [Omvänd adresss ökning](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) för att söka efter en gatuadress
* Sök efter en kors gata med [Sök adress omvänt kors gatan API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)

## <a name="prerequisites"></a>Krav

Om du vill göra anrop till Maps-tjänstens API: er behöver du ett Maps-konto och nyckel. Följ instruktionerna i [skapa ett konto](quick-demo-map-app.md#create-an-account-with-azure-maps) om du vill skapa en Azure Maps konto prenumeration och följ stegen i [Hämta primär nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account) för att hämta den primära nyckeln för ditt konto. Mer information om autentisering i Azure Maps finns i [hantera autentisering i Azure Maps](./how-to-manage-authentication.md).

I den här artikeln används [Postman-appen](https://www.getpostman.com/apps) för att bygga rest-anrop. Du kan använda valfri API utvecklings miljö som du föredrar.

## <a name="using-fuzzy-search"></a>Använda FUZZY-sökning

Standard-API: et för Sök tjänsten är en [suddig sökning](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) och är användbart när du inte vet vad dina användarindata är för en Sök fråga. API: n kombinerar POI-sökning och-kodning i en kanonisk ' enkel rads sökning '. API: t kan till exempel hantera indata för valfri kombination av adress eller POI-token. Det kan också vägas med en kontext position (lat./lon. par), helt begränsad av en koordinat och radie, eller körs oftare utan någon fäst punkt för geo-förskjutning.

De flesta Sök frågor som standard `maxFuzzyLevel=1` för att få prestanda och minska ovanliga resultat. Standardvärdet kan åsidosättas efter behov per begäran genom att skicka in Frågeparametern `maxFuzzyLevel=2` eller `3`.

### <a name="search-for-an-address-using-fuzzy-search"></a>Sök efter en adress med hjälp av en Fuzzy-sökning

1. Öppna Postman-appen, klicka på ny | Skapa nytt och välj **Hämta begäran**. Ange ett namn på den **oskarpa sökningen**, Välj en samling eller mapp att spara den i och klicka på **Spara**.

2. På fliken Builder väljer du metoden **Hämta** http och anger fråge-URL: en för API-slutpunkten.

    ![Fuzzy-sökning](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Parameter | Föreslaget värde |
    |---------------|------------------------------------------------|
    | HTTP-metod | HÄMTA |
    | URL för begäran | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | Autentisering | Ingen autentisering |

    **JSON** -ATTRIBUTET i URL-sökvägen bestämmer svars formatet. Du använder JSON i den här artikeln för enkel användning och läsbarhet. Du hittar tillgängliga svars format i den **Hämta sökningens fuzzy** -definition av [Maps funktions-API-referens](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

3. Klicka på **params**och ange följande nyckel/värde-par som ska användas som fråga-eller Sök vägs parametrar i fråge-URL:

    ![Fuzzy-sökning](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Nyckel | Värde |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | prenumerations nyckel | \<din Azure Maps nyckel\> |
    | DocumentDB | pizza |

4. Klicka på **Skicka** och granska svars texten.

    Den tvetydiga frågesträngen "pizza" returnerade 10 [punkter av intresse resultat](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) (POI) med kategorier som omfattas av "pizza" och "restaurang". Varje resultat returnerar en gatuadress, latitud-/longitud värden, visar port och start punkter för platsen.
  
    Resultaten varierar för den här frågan, inte knutna till någon särskild referens plats. Du kan använda parametern **countrySet** för att ange bara de länder/regioner som programmet behöver täckning för, eftersom standard beteendet är att söka hela världen, vilket kan returnera onödiga resultat.

5. Lägg till följande nyckel/värde-par i avsnittet **params** och klicka på **Skicka**:

    | Nyckel | Värde |
    |------------------|-------------------------|
    | countrySet | USA |
  
    Resultaten begränsas nu av lands koden och frågan returnerar pizza restauranger i USA.
  
    Om du vill ange resultat för en plats kan du fråga en orienterings punkt och använda värdena för latitud och longitud i ditt anrop till fuzzy Search-tjänsten. I detta fall använde du Sök tjänsten för att returnera platsen för bladet Seattle, Seattle och använde lat. / lon. värden för att orientera sökningen.
  
6. Ange följande nyckel/värde-par i params och klicka på **Skicka**:

    ![Fuzzy-sökning](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | Nyckel | Värde |
    |-----|------------|
    | koder | 47,620525 |
    | lon | – 122,349274 |

## <a name="search-for-address-properties-and-coordinates"></a>Sök efter adress egenskaper och koordinater

Du kan skicka en fullständig eller delvis gatuadress till Sök adress-API: et och få ett svar som innehåller detaljerade adress egenskaper som kommun eller underindelning, samt positions värden i latitud och longitud.

1. I Postman klickar du på **ny begäran** | **Hämta begäran** och ge den namnet **adress Sök**.
2. På fliken Builder väljer du metoden **Hämta** http, anger fråge-URL: en för API-slutpunkten och väljer ett Authorization-protokoll, om det finns några.

    ![Adresss ökning](./media/how-to-search-for-address/address_search_url.png)
  
    | Parameter | Föreslaget värde |
    |---------------|------------------------------------------------|
    | HTTP-metod | HÄMTA |
    | URL för begäran | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) |
    | Autentisering | Ingen autentisering |

3. Klicka på **params**och ange följande nyckel/värde-par som ska användas som fråga-eller Sök vägs parametrar i fråge-URL:
  
    ![Adresss ökning](./media/how-to-search-for-address/address_search_params.png)
  
    | Nyckel | Värde |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | prenumerations nyckel | \<din Azure Maps nyckel\> |
    | DocumentDB | 400 Broad St, Seattle, WA 98109 |
  
4. Klicka på **Skicka** och granska svars texten.
  
    I det här fallet angav du en fullständig adress fråga och får ett enda resultat i svars texten.
  
5. I params redigerar du frågesträngen till följande värde:
    ```plaintext
        400 Broad, Seattle
    ```

6. Lägg till följande nyckel/värde-par i avsnittet **params** och klicka på **Skicka**:

    | Nyckel | Värde |
    |-----|------------|
    | typeahead | sant |

    Flaggan **typeahead** visar API för adresss ökning för att behandla frågan som en del indata och returnera en matris med förutsägande värden.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Sök efter en gatuadress med omvänd adresss ökning

1. I Postman klickar du på **ny begäran** | **Hämta begäran** och ge den namnet **Omvänd adress Sök**.

2. På fliken Builder väljer du metoden **Hämta** http och anger fråge-URL: en för API-slutpunkten.
  
    ![URL för omvänd adresss ökning](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parameter | Föreslaget värde |
    |---------------|------------------------------------------------|
    | HTTP-metod | HÄMTA |
    | URL för begäran | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | Autentisering | Ingen autentisering |
  
3. Klicka på **params**och ange följande nyckel/värde-par som ska användas som fråga-eller Sök vägs parametrar i fråge-URL:
  
    ![Sök parametrar för omvänd adress](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | Nyckel | Värde |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | prenumerations nyckel | \<din Azure Maps nyckel\> |
    | DocumentDB | 47.591180,-122,332700 |
  
4. Klicka på **Skicka** och granska svars texten.

    Svaret innehåller viktig adress information om Safeco-fältet.
  
5. Lägg till följande nyckel/värde-par i avsnittet **params** och klicka på **Skicka**:

    | Nyckel | Värde |
    |-----|------------|
    | nummer | sant |

    Om [värdet](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) för Frågeparametern skickas med begäran kan svaret omfatta sidan av gatan (vänster/höger) och även en förskjutnings position för det talet.
  
6. Lägg till följande nyckel/värde-par i avsnittet **params** och klicka på **Skicka**:

    | Nyckel | Värde |
    |-----|------------|
    | returnSpeedLimit | sant |
  
    När [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) -Frågeparametern har angetts returneras svars tiden för den anslagna hastighets gränsen.

7. Lägg till följande nyckel/värde-par i avsnittet **params** och klicka på **Skicka**:

    | Nyckel | Värde |
    |-----|------------|
    | returnRoadUse | sant |

    När Frågeparametern [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) anges, returnerar svaret vägens användnings mat ris för omvända omvändare på gatu nivå.

8. Lägg till följande nyckel/värde-par i avsnittet **params** och klicka på **Skicka**:

    | Nyckel | Värde |
    |-----|------------|
    | roadUse | sant |

    Du kan begränsa den omvända koden för den omvända koden till en speciell typ av väg användning med hjälp av Frågeparametern [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) .
  
## <a name="search-for-the-cross-street-using-reverse-address-cross-street-search"></a>Sök efter kors gatan med omvänd adress över gata-sökning

1. I Postman, klickar du på **ny begäran** | **Get-begäran** och ger den **Omvänd adress mellan gata Sök**.

2. På fliken Builder väljer du metoden **Hämta** http och anger fråge-URL: en för API-slutpunkten.
  
    ![Omvänd adress, korsad gata-sökning](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parameter | Föreslaget värde |
    |---------------|------------------------------------------------|
    | HTTP-metod | HÄMTA |
    | URL för begäran | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | Autentisering | Ingen autentisering |
  
3. Klicka på **params**och ange följande nyckel/värde-par som ska användas som fråga-eller Sök vägs parametrar i fråge-URL:
  
    | Nyckel | Värde |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | prenumerations nyckel | \<din Azure Maps nyckel\> |
    | DocumentDB | 47.591180,-122,332700 |
  
4. Klicka på **Skicka** och granska svars texten.

## <a name="next-steps"></a>Nästa steg

- Utforska [Azure Maps Search Service API-](https://docs.microsoft.com/rest/api/maps/search) dokumentationen.
