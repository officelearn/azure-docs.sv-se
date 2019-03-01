---
title: Hur du kan visa anpassade data på en karta för raster i Azure Maps | Microsoft Docs
description: Återge anpassade data på en karta för raster i Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 982a8b6ed67b955fcb68006072b67f7c59f29688
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010165"
---
# <a name="render-custom-data-on-a-raster-map"></a>Rendera anpassade data på en karta för raster

Den här artikeln förklarar hur du använder den [statisk avbildning service](https://docs.microsoft.com/rest/api/maps/render/getmapimage) med bild sammansättning funktioner för att tillåta överlägg ovanpå en raster karta. Bild sammansättning innefattar möjligheten att hämta en panel för raster tillbaka, plus ytterligare information som anpassade kartnålar, etiketter och geometri överlägg.

För att rendera anpassade kartnålar, etiketter och geometri överlägg ska använda du Postman-programmet. Du kan använda Azure Maps [Data Service API: er](https://docs.microsoft.com/rest/api/maps/data) kan lagra och återge överlägg.


## <a name="prerequisites"></a>Förutsättningar

### <a name="create-an-azure-maps-account"></a>Skapa ett Azure Maps-konto

För att slutföra procedurerna i den här artikeln, måste du först [skapa ett Azure Maps-konto](how-to-manage-account-keys.md) i du prisnivån S1.

## <a name="render-pushpins-with-labels-and-a-custom-image"></a>Rendera kartnålar med etiketter och en anpassad avbildning

> [!Note]
> Proceduren i det här avsnittet kräver ett Azure Maps-konto i prisnivå S0 eller S1.

Azure Maps-konto S0-nivån stöder endast en instans av den `pins` parametern. Det kan du återge upp till fem kartnålar som anges i URL-begäran med en anpassad avbildning.

För att rendera kartnålar med etiketter och en anpassad avbildning ska du utföra följande steg:

1. Skapa en samling som ska lagra begäranden. I Postman-appen, väljer **New**. I den **Skapa ny** väljer **samling**. Ge samlingen ett namn och välj den **skapa** knappen. 

2. För att skapa begäran, Välj **New** igen. I den **Skapa ny** väljer **begära**. Ange en **frågenamn** för kartnålar, väljer du den samling som du skapade i föregående steg som platsen där du vill spara begäran och välj sedan **spara**.
    
    ![Skapa en begäran i Postman](./media/tutorial-geofence/postman-new.png)

3. Välj Hämta HTTP-metod på fliken builder och ange följande URL för att skapa en GET-begäran.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttp%3A%2F%2Fazuremapscodesamples.azurewebsites.net%2FCommon%2Fimages%2Fpushpins%2Fylw-pushpin.png
    ```
    Här är den resulterande bilden:

    ![En anpassad kartnålen med en etikett](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Hämta data från Azure Maps-datalagring

> [!Note]
> Proceduren i det här avsnittet kräver ett Azure Maps-konto i prisnivån S1.

Du kan också hämta platsinformation sökväg och PIN-kod med hjälp av den [Data överför API: et](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). Följ stegen nedan för att överföra data för sökvägen och PIN-koder.

1. Öppna en ny flik i den samling som du skapade i föregående avsnitt i Postman-appen. Välj efter HTTP-metod på fliken builder och ange följande URL för att göra en POST-begäran:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. På den **Params** ange följande nyckel/värde-par, som används för POST-begäran-URL. Ersätt den `subscription-key` värdet med din prenumerationsnyckel för Azure Maps.
    
    ![Nyckel/värde-parametrar i Postman](./media/how-to-render-custom-data/postman-key-vals.png)

3. På den **brödtext** , Välj raw Indataformatet och välj JSON som Indataformatet från den nedrullningsbara listan. Ange JSON som data som ska laddas upp:
    
    ```JSON
    {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -73.98235,
                  40.76799
                ],
                [
                  -73.95785,
                  40.80044
                ],
                [
                  -73.94928,
                  40.7968
                ],
                [
                  -73.97317,
                  40.76437
                ],
                [
                  -73.98235,
                  40.76799
                ]
              ]
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "LineString",
            "coordinates": [
              [
                -73.97624731063843,
                40.76560773817073
              ],
              [
                -73.97914409637451,
                40.766826609362575
              ],
              [
                -73.98513078689575,
                40.7585866048861
              ]
            ]
          }
        }
      ]
    }
    ```

4. Välj **skicka** och granska svarshuvudet. Location-huvudet innehåller den URI som används för att komma åt eller hämta data för framtida användning. Den innehåller även ett unikt `udId` för uppladdade data.  

  ```HTTP
  https://atlas.microsoft.com/mapData/{udId}/status?api-version=1.0&subscription-key={Subscription-key}
  ```

5. Använd den `udId` värde togs emot från API för ladda upp att återge funktioner på kartan. Gör detta genom att öppna en ny flik i samlingen som du skapade i föregående avsnitt. Välj Hämta HTTP-metod på fliken builder och ange URL: en för att göra en GET-begäran:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

6. Här är den resulterande bilden:

    ![Hämta data från Azure Maps-datalagring](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>Rendera en polygon med färg och opacitet

> [!Note]
> Proceduren i det här avsnittet kräver ett Azure Maps-konto i prisnivån S1.


Du kan ändra utseendet på en polygon med hjälp av style modifierare med den [sökvägsparameter](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).

1. Öppna en ny flik i den samling som du skapade tidigare i Postman-appen. Välj Hämta HTTP-metod på fliken builder och ange följande URL för att konfigurera en GET-begäran för att återge en polygon med färg och opacitet:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription--key}
    ```

Här är den resulterande bilden:

![Rendera en täckande polygon](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>Rendera en cirkel och kartnålar med egna etiketter

> [!Note]
> Proceduren i det här avsnittet kräver ett Azure Maps-konto i prisnivån S1.


Du kan göra kartnålar och deras etiketter större eller mindre genom att använda den `sc` skala style modifieraren. Den här modifieraren tar ett värde som är större än noll. Värdet 1 är standard skalan. Värden som är större än 1 gör att PIN-koder större och värden som är mindre än 1 gör dem mindre. Läs mer om style modifierare [statisk avbildning service sökvägsparametrar](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).


Följ dessa steg för att rendera en cirkel och kartnålar med egna etiketter:

1. Öppna en ny flik i den samling som du skapade tidigare i Postman-appen. Välj Hämta HTTP-metod på fliken builder och ange URL: en för att göra en GET-begäran:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

Här är den resulterande bilden:

![Rendera en cirkel med anpassade kartnålar](./media/how-to-render-custom-data/circle-custom-pins.png)

## <a name="next-steps"></a>Nästa steg


* Utforska den [Azure Maps hämta kartan Image API](https://docs.microsoft.com/rest/api/maps/render/getmapimage) dokumentation.
* Läs mer om Azure Maps Data Service i den [dokumentation för service](https://docs.microsoft.com/rest/api/maps/data).

