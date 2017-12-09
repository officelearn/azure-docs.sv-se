---
title: "Sökning med Azure-plats-baserade tjänster | Microsoft Docs"
description: "Sök i närheten intressant med hjälp av Azure baserad platstjänster"
services: location-based-services
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 11/28/2017
ms.topic: tutorial
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 15afdead60d4c1ee3c7e3c079d43e0651b262ec8
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="search-nearby-point-of-interest-using-azure-location-based-services"></a>Sök i närheten intressant med hjälp av Azure baserad platstjänster

Den här kursen visar hur du skapar ett konto med Azure baserad platstjänster och sedan använda den tillhandahållna API: er för att söka efter en punkt av intresse. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa ett konto med Azure baserad platstjänster
> * Hämta nyckel för prenumerationen för ditt konto
> * Skapa ny webbsida med kartan kontroll-API
> * Använda Search-tjänsten för att hitta Närliggande intressant

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

# <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal
Logga in på [Azure-portalen](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-location-based-services"></a>Skapa ett konto med Azure baserad platstjänster

Följ dessa steg om du vill skapa ett nytt konto för plats-baserade tjänster.

1. I det övre vänstra hörnet av den [Azure-portalen](https://portal.azure.com), klickar du på **skapar du en resurs**.
2. I den *söka Marketplace* skriver **plats-baserade tjänster**.
3. Från den *resultat*, klicka på den **baserat platstjänster (förhandsgranskning)**. Klicka på **skapa** som visas nedan kartan. 
4. På den **skapa plats baserat Services-konto** anger du följande värden:
    - Den *namn* för ditt nya konto. 
    - Den *prenumeration* som du vill använda för det här kontot.
    - Den *resursgruppen* namn för det här kontot. Du kan välja att *Skapa nytt* eller *använda befintliga* resursgruppen.
    - Välj den *resursgruppsplats*.
    - Läs den *Förhandsgranskningsvillkoren* och markera kryssrutan för att acceptera villkoren. 
    - Klicka slutligen på den **skapa** knappen.
   
    ![Skapa kontot för plats-baserade tjänster i portalen](./media/tutorial-search-location/create-lbs-account.png)


<a id="getkey"></a>

## <a name="get-the-subscription-key-for-your-account"></a>Hämta nyckel för prenumerationen för ditt konto

När ditt konto för plats-baserade tjänster har skapats, Följ stegen för att länka till kartan sökningen API: er:

1. Öppna din plats-baserade tjänster-konto i portalen.
2. Navigera till ditt konto **inställningar**, och välj sedan **nycklar**.
3. Kopiera den **primärnyckel** till Urklipp. Spara filen lokalt för att använda i steg om du fortsätter. 

    ![Hämta primärnyckel i portalen](./media/tutorial-search-location/lbs-get-key.png)


<a id="createmap"></a>

## <a name="create-new-web-page-using-azure-map-control-api"></a>Skapa ny webbsida med Azure kartan kontroll-API
Azure kartan kontroll-API är ett bekvämt klientbiblioteket som hjälper dig att enkelt integrera Azure baserad platstjänster i ditt webbprogram. Den döljer komplexiteten i de minimala REST-anrop och ökar produktiviteten med styleable och anpassningsbara komponenter. Följande steg visar hur du skapar en statisk HTML-sida inbäddade med plats baserat tjänsterna kartan kontroll-API. 

1. Skapa en ny fil på den lokala datorn och ge den namnet **MapSearch.html**. 
2. Lägg till följande HTML-komponenter i filen:

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Search</title>

        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>

        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #map {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    <body>
        <div id="map"></div>
        <script>
        // Embed Map Control JavaScript code here
        </script>

    </body>

    </html>
    ``` 
    Observera att HTML-huvudet innehåller resursfiler CSS- och JavaScript hos Azure Kartkontrollen-biblioteket. Observera den *skriptet* segment som lagts till i den *brödtext* av HTML-fil. Det här segmentet innehåller inline JavaScript-kod för att komma åt Azure plats baserat tjänstens API: er.
 
3.  Lägg till följande JavaScript-kod till den *skriptet* block med HTML-fil. Ersätt platshållaren *< INS >* med ditt konto baserat platstjänster primärnyckel. 

    ```HTML/JavaScript
            // Instantiate map to the div with id "map"
            var subscriptionKey = "<insert-key>";
            var map = new atlas.Map("map", {
                "subscription-key": subscriptionKey
            });

    ```
    Det här segmentet initierar kartan kontroll-API för din prenumeration nyckel. **Atlas** är det namnområde som innehåller Azure kartan kontroll-API och relaterade visuella komponenter. **Atlas. Kartan** ger kontrollen för en visual och interaktiva webb-karta. Du kan se hur kartans ser ut som genom att öppna HTML-sidan i webbläsaren. 

4. Lägg till följande JavaScript-kod till den *skriptet* block, att lägga till ett lager av Sök PIN-koder i Kartkontrollen:

    ```HTML/JavaScript
            // Initialize the pin layer for search results to the map
            var searchLayerName = "search-results";
            map.addPins([], {
                name: searchLayerName,
                cluster: false,
                icon: "pin-round-darkblue"
            });
    ```

5. Spara filen på din dator. 


<a id="usesearch"></a>

## <a name="use-search-service-to-find-nearby-point-of-interest"></a>Använda Search-tjänsten för att hitta Närliggande intressant

Det här avsnittet visar hur du använder Azure plats baserat tjänsterna Söktjänsts-API för att hitta en plats av intresse på kartan. Det är en RESTful-API för utvecklare att söka efter adresser, punkter av intresse och andra geografisk information. Söktjänsten tilldelar en latitud och longitud information till en angiven adress. 

1. Öppna den **MapSearch.html** filen skapas i föregående avsnitt och Lägg till följande JavaScript-kod till den *skriptet* block att illustrera Search-tjänsten. 
    ```HTML/JavaScript
            // Perform a request to the search service and create a pin on the map for each result
            var xhttp = new XMLHttpRequest();
            xhttp.onreadystatechange = function () {
                var searchPins = [];

                if (this.readyState === 4 && this.status === 200) {
                    var response = JSON.parse(this.responseText);

                    var poiResults = response.results.filter((result) => { return result.type === "POI" }) || [];

                    searchPins = poiResults.map((poiResult) => {
                        var poiPosition = [poiResult.position.lon, poiResult.position.lat];
                        return new atlas.data.Feature(new atlas.data.Point(poiPosition), {
                            name: poiResult.poi.name,
                            address: poiResult.address.freeformAddress,
                            position: poiResult.position.lat + ", " + poiResult.position.lon
                        });
                    });

                    map.addPins(searchPins, {
                        name: searchLayerName
                    });

                    var lons = searchPins.map((pin) => { return pin.geometry.coordinates[0] });
                    var lats = searchPins.map((pin) => { return pin.geometry.coordinates[1] });

                    var swLon = Math.min.apply(null, lons);
                    var swLat = Math.min.apply(null, lats);
                    var neLon = Math.max.apply(null, lons);
                    var neLat = Math.max.apply(null, lats);

                    map.setCameraBounds({
                        bounds: [swLon, swLat, neLon, neLat],
                        padding: 50
                    });
                }
            };
    ```
    Det här kodstycket skapar en [XMLHttpRequest](https://xhr.spec.whatwg.org/), och lägger till en händelsehanterare för att analysera inkommande svaret. För ett lyckat svar samlar det in adresser, namn, latitud och logitude information för varje plats returneras i den `searchPins` variabeln. Slutligen lägger den till den här samlingen av plats pekar på `map` kontrollen är PIN-koder. 

2. Lägg till följande kod i den *skriptet* block, skicka XMLHttpRequest till Azure plats baserat tjänsterna Search-tjänsten:

    ```HTML/JavaScript
            var url = "https://atlas.microsoft.com/search/fuzzy/json?";
            url += "&api-version=1.0";
            url += "&query=gasoline%20station";
            url += "&subscription-key=" + subscriptionKey;
            url += "&lat=47.6292";
            url += "&lon=-122.2337";
            url += "&radius=100000"

            xhttp.open("GET", url, true);
            xhttp.send();
    ``` 
    Den här fragment använder enkel sökning API för Search-tjänsten som kallas den **Fuzzy Sök**. Den hanterar mest fuzzy av indata som hanterar valfri kombination av adress eller *POI* token. Den söker efter i närheten **bensin station**, för den angivna adressen i latitud och longitud och inom den angivna radien. Ditt konto prenumeration nyckel som anges tidigare i exempel-filen används för att göra anrop till den plats baserat tjänster. Returnerar resultatet som latitud/longitud par för att hitta platser. Du kan se Sök PIN-koder genom att öppna HTML-sidan i webbläsaren. 

3. Lägg till följande rader till den *skriptet* blockera, skapa popup-fönster för punkter av intresse som returneras av söktjänsten:

    ```HTML/JavaScript
            // Add a popup to the map which will display some basic information about a search result on hover over a pin
            var popup = new atlas.Popup();
            map.addEventListener("mouseover", searchLayerName, (e) => {
                var popupContentElement = document.createElement("div");
                popupContentElement.style.padding = "5px";

                var popupNameElement = document.createElement("div");
                popupNameElement.innerText = e.features[0].properties.name;
                popupContentElement.appendChild(popupNameElement);

                var popupAddressElement = document.createElement("div");
                popupAddressElement.innerText = e.features[0].properties.address;
                popupContentElement.appendChild(popupAddressElement);

                var popupPositionElement = document.createElement("div");
                popupPositionElement.innerText = e.features[0].properties.position;
                popupContentElement.appendChild(popupPositionElement);

                popup.setPopupOptions({
                    position: e.features[0].geometry.coordinates,
                    content: popupContentElement
                });

                popup.open(map);
            });
    ```
    API: et **atlas. Popup-fönster** ger information fönstret förankrade till önskad placering på kartan. Det här kodstycket anger innehåll och position för popup-meny som lägger till en händelselyssnaren till den `map` kontroll, väntar på att den _musen_ att rulla över popup-fönstret. 

4. Spara filen och öppna den **MapSearch.html** filen i en webbläsare som du väljer och se resultatet. Nu visas information popup-fönster i kartan i webbläsaren när du hovrar över någon av de Sök PIN-koder visas liknar följande. 

    ![Azure Kartkontrollen och söktjänsten](./media/tutorial-search-location/lbs-map-search.png)


## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett konto med Azure baserad platstjänster
> * Hämta nyckel för prenumerationen för ditt konto
> * Skapa ny webbsida med kartan kontroll-API
> * Använda Search-tjänsten för att hitta Närliggande intressant

Gå vidare till självstudiekursen [väg till en plats med hjälp av Azure baserad platstjänster intressanta](./tutorial-route-location.md) att lära dig hur du använder Azure baserad platstjänsterna för att vidarebefordra till intressant. 
