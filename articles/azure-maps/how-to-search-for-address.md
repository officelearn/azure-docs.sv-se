---
title: Söka efter en adress med hjälp av tjänsten Azure Maps Search | Microsoft Docs
description: Lär dig hur du söker efter en adress med hjälp av Azure Maps Search-tjänst
author: dsk-2015
ms.author: dkshir
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: fe3bb3a778a42696cd15f9e4265448479bf043a1
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2018
ms.locfileid: "42059203"
---
# <a name="how-to-find-an-address-using-the-azure-maps-search-service"></a>Så här hittar du en adress med hjälp av Azure Maps search-tjänst

Söktjänsten Maps är en uppsättning API: er för utvecklare att söka efter adresser, platser, Orienteringspunkter, företag och annan geografisk information. Tjänsten tilldelar latitud/longitud till en specifik adress, mellan gata, geografiska funktion eller orienteringspunkt (POI). Latitud och longitud värden som returneras av sökningen kan användas som parametrar i andra Maps-tjänster som väg och trafikflöde.

## <a name="prerequisites"></a>Förutsättningar

För att göra något anrop till API: er för Maps-tjänsten, behöver du en Maps-konto och nyckel. Information om att skapa ett konto och hämta en nyckel finns i [så här hanterar du ditt Azure Maps-konto och dina nycklar](how-to-manage-account-keys.md).

Den här artikeln används den [Postman-appen](https://www.getpostman.com/apps) att skapa REST-anrop. Du kan använda alla API-utvecklingsmiljö du föredrar. 


## <a name="using-fuzzy-search"></a>Använda fuzzy-sökning

Standard-API: et för search-tjänsten är [fuzzy-sökning](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy), som hanterar indata för valfri kombination av adress eller POI-token. Den här sökningen API är den kanoniska ”rad Sök” och är användbart när du inte vet vilken din användarindata som en sökfråga. Fuzzy-sökning-API är en kombination av POI sökning och geokodning. API: et kan också vägas med sammanhangsberoende position (lat./celligt. Koppla ihop), fullständigt begränsas av en koordinaten och radius, eller den kan köras mer allmänt utan någon geo börvärdessignalerna ankarpunkten.

De flesta sökfrågor som standard ”maxFuzzyLevel = 1' om du vill få prestanda och minska ovanliga resultat. Det här standardvärdet kan åsidosättas vid behov per begäran genom att skicka in Frågeparametern ”maxFuzzyLevel = 2' eller '3'.

### <a name="search-for-an-address-using-fuzzy-search"></a>Sök efter en adress med hjälp av Fuzzy-sökning

1. Öppna Postman-appen, klicka på New | Skapa ny och välj **GET-begäran**. Ange ett namn för **Fuzzy-sökning**, Välj en samling eller en mapp att spara den till och klicka på **spara**.

2. På fliken Builder väljer den **hämta** HTTP-metoden och ange fråge-URL för API-slutpunkten.

    ![Fuzzy-sökning ](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Parameter | Föreslaget värde |
    |---------------|------------------------------------------------|
    | HTTP-metod | HÄMTA |
    | Fråge-URL | https://atlas.microsoft.com/search/fuzzy/json? |
    | Auktorisering | Ingen autentisering |

    Den **json** attribut i en URL-sökväg avgör svarsformat. Du använder json i den här artikeln för enkel användning och läsbarhet. Du kan hitta tillgängliga svar-format i den **hämta Fuzzy Search** definitionen av [Maps funktions-API-referens] (https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

3. Klicka på **Params**, och ange följande nyckel / värde-par som används som frågan eller sökväg parametrar i fråge-URL:

    ![Fuzzy-sökning ](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Nyckel | Värde |
    |------------------|-------------------------|
    | API-versionen | 1.0 |
    | prenumerationsnyckel | \<Azure Maps-nyckel\> |
    | DocumentDB | pizza |

4. Klicka på **skicka** och granska svarstexten. 

    Tvetydig frågesträngen för ”platt” returneras 10 punkter av intresse (POI) resultat med kategorier med ”platt” och ”restaurang”. Varje resultatet returnerar en gatuadress latitud / longitudvärden, visa port och startpunkter för platsen.
    
    Den här frågan är inte kopplat till vilken plats som helst särskilt varieras resultaten. Du kan använda den **countrySet** parametern för att ange endast de länder som programmet behöver täckning som standard är att söka i hela världen, potentiellt returnera onödiga resultat.

5. Lägg till följande nyckel / värde-par för den **Params** och klicka **skicka**:

    | Nyckel | Värde |
    |------------------|-------------------------|
    | countrySet | USA |
    
    Resultatet är nu bundna av landskoden och frågan returnerar pizza restauranger i USA.
    
    Om du vill visa resultat som är inriktade på en viss plats, kan du fråga efter en orienteringspunkt och använda returnerade latitud och longitudvärden i anrop till tjänsten Fuzzy-sökning. I detta fall använder du används Search-tjänsten för att returnera platsen för Seattle utrymme nålen och används lat. / celligt. värden att orientera sökningen.
    
4. I parametrar, anger du följande nyckel / värde-par och klicka på **skicka**:

    ![Fuzzy-sökning ](./media/how-to-search-for-address/fuzzy_search_latlon.png)
    
    | Nyckel | Värde |
    |-----|------------|
    | lat | 47.62039 |
    | celligt | -122.34928 |

## <a name="search-for-address-properties-and-coordinates"></a>Sök efter egenskaper och koordinater 

Du kan skicka en fullständig eller partiell gatuadress till search-adress API och ta emot ett svar som innehåller detaljerade egenskaper, till exempel kommuner eller delfältet samt namngivna värden i latitud och longitud.

1. I Postman, klickar du på **ny begäran** | **GET-begäran** och ge den namnet **sökningen efter adresser**.
2. På fliken Builder väljer den **hämta** HTTP-metoden, ange begärans-URL för API-slutpunkten och välj eventuellt auktoriseringsprotokoll, om sådana.

    ![Sökningen efter adresser ](./media/how-to-search-for-address/address_search_url.png)
    
    | Parameter | Föreslaget värde |
    |---------------|------------------------------------------------|
    | HTTP-metod | HÄMTA |
    | Fråge-URL | https://atlas.microsoft.com/search/address/json? |
    | Auktorisering | Ingen autentisering |

2. Klicka på **Params**, och ange följande nyckel / värde-par som används som frågan eller sökväg parametrar i fråge-URL:
    
    ![Sökningen efter adresser ](./media/how-to-search-for-address/address_search_params.png)
    
    | Nyckel | Värde |
    |------------------|-------------------------|
    | API-versionen | 1.0 |
    | prenumerationsnyckel | \<Azure Maps-nyckel\> |
    | DocumentDB | 400 breda St, Seattle, WA 98109 |
    
3. Klicka på **skicka** och granska svarstexten. 
    
    I detta fall använder du angett en fullständig adress-fråga och ta emot ett enskilt resultat i svarstexten. 
    
4. Redigera parametrar, frågesträng till följande värde:
    ```
        400 Broad, Seattle
    ```

5. Lägg till följande nyckel / värde-par för den **Params** och klicka **skicka**:

    | Nyckel | Värde |
    |-----|------------|
    | typeahead | true |

    Den **typeahead** flaggan säger Sök-API-adress för att behandla frågan som en partiell indata och returnerar en matris med förutsägande värden.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Sök efter en gatuadress använda omvänd sökning av adress
1. I Postman, klickar du på **ny begäran** | **GET-begäran** och ge den namnet **omvänd sökningen efter adresser**.

2. På fliken Builder väljer den **hämta** HTTP-metoden och ange fråge-URL för API-slutpunkten.
    
    ![Omvänd sökning Webbadress ](./media/how-to-search-for-address/reverse_address_search_url.png)
    
    | Parameter | Föreslaget värde |
    |---------------|------------------------------------------------|
    | HTTP-metod | HÄMTA |
    | Fråge-URL | https://atlas.microsoft.com/search/address/reverse/json? |
    | Auktorisering | Ingen autentisering |
    
2. Klicka på **Params**, och ange följande nyckel / värde-par som används som frågan eller sökväg parametrar i fråge-URL:
    
    ![Omvänd adress sökparametrar ](./media/how-to-search-for-address/reverse_address_search_params.png)
    
    | Nyckel | Värde |
    |------------------|-------------------------|
    | API-versionen | 1.0 |
    | prenumerationsnyckel | \<Azure Maps-nyckel\> |
    | DocumentDB | 47.59093,-122.33263 |
    
3. Klicka på **skicka** och granska svarstexten. 
    
    Svaret innehåller posten POI för Safeco Field med en poi kategori av ”arenan”. 
    
4. Lägg till följande nyckel / värde-par för den **Params** och klicka **skicka**:

    | Nyckel | Värde |
    |-----|------------|
    | nummer | true |

    Om den [nummer](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) Frågeparametern skickas med begäran, svaret kan innehålla sida för gata (vänster/höger) och en förskjutning position för det numret.
    
5. Lägg till följande nyckel / värde-par för den **Params** och klicka **skicka**:

    | Nyckel | Värde |
    |-----|------------|
    | returnSpeedLimit | true |
    
    När den [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) Frågeparametern anges svaret som returneras av bokförda hastighet gränsen.

6. Lägg till följande nyckel / värde-par för den **Params** och klicka **skicka**:

    | Nyckel | Värde |
    |-----|------------|
    | returnRoadUse | true |

    När den [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) Frågeparametern är inställt, svaret returnerar väg Använd matris för reversegeocodes på nivån för gata.

7. Lägg till följande nyckel / värde-par för den **Params** och klicka **skicka**:

    | Nyckel | Värde |
    |-----|------------|
    | roadUse | true |

    Du kan begränsa omvänd geocode frågan till en viss typ av väg användning med den [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) frågeparameter.
    
## <a name="search-for-the-cross-street-using-reverse-address-cross-street-search"></a>Sök efter mellan gata med hjälp av omvänd adress mellan gatuadress Search

1. I Postman, klickar du på **ny begäran** | **GET-begäran** och ge den namnet **omvänd adress mellan gata Search**.

2. På fliken Builder väljer den **hämta** HTTP-metoden och ange fråge-URL för API-slutpunkten.
    
    ![Omvänd adress mellan gata sökning ](./media/how-to-search-for-address/reverse_address_search_url.png)
    
    | Parameter | Föreslaget värde |
    |---------------|------------------------------------------------|
    | HTTP-metod | HÄMTA |
    | Fråge-URL | https://atlas.microsoft.com/search/address/reverse/crossstreet/json? |
    | Auktorisering | Ingen autentisering |
    
3. Klicka på **Params**, och ange följande nyckel / värde-par som används som frågan eller sökväg parametrar i fråge-URL:
    
    | Nyckel | Värde |
    |------------------|-------------------------|
    | API-versionen | 1.0 |
    | prenumerationsnyckel | \<Azure Maps-nyckel\> |
    | DocumentDB | 47.59093,-122.33263 |
    
4. Klicka på **skicka** och granska svarstexten. 

## <a name="next-steps"></a>Nästa steg
- Utforska den [söktjänst i Azure Maps](https://docs.microsoft.com/rest/api/maps/search) API-dokumentation 
