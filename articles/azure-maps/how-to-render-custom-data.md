---
title: Hur du kan visa anpassade data på raster karta i Azure Maps | Microsoft Docs
description: Återge anpassade data på raster karta i Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: ddc051eb5f9638f7afec34db41c0e9d6e6d9d57d
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56890070"
---
# <a name="render-custom-data-on-raster-map"></a>Rendera anpassade data på raster map

Den här artikeln visar hur du använder [statisk avbildning service](https://docs.microsoft.com/rest/api/maps/render/getmapimage) med bild sammansättning funktioner för att tillåta överlägg ovanpå en raster karta. Bild sammansättning innefattar möjligheten att hämta en panel för raster med ytterligare data som anpassade kartnålar, etiketter och geometri överlägg. För att rendera anpassade kartnålar, etiketter och geometri ska använder vi postman-programmet. Öppna Postman-appen, klicka på New | Skapa ny, väljer en samling eller en mapp att spara den till och klicka på Spara.

Vi kommer att använda Azure Maps [Data Service API: er](https://docs.microsoft.com/rest/api/maps/data) kan lagra och återge överlägg. 


## <a name="prerequisites"></a>Förutsättningar

### <a name="create-an-azure-maps-account"></a>Skapa ett Azure Maps-konto 

Om du vill följa stegen i den här guiden, måste du först se [hantera konton och nycklar](how-to-manage-account-keys.md) du skapar och hanterar prenumerationen konto med S1 prisnivå.

## <a name="render-pushpins-with-labels-and-custom-image"></a>Rendera kartnålar med etiketter och anpassad avbildning

> [!Note]
> Det här exemplet kräver ett Azure Maps-konto med prisnivån S0 eller S1. 

Azure Maps-konto S0 SKU: N stöder bara en enda instans av den `pins` parametern så att användarna kan göra upp till 5 kartnålar som anges i url-begäran med anpassad avbildning.

För att rendera push PIN-koder med etiketter och anpassad avbildning för att följa stegen nedan:

1. Öppna Postman-appen, klicka på New | Create new (Ny | Skapa ny), och välj Request (Begäran). Ange ett namn för rendering kartnålar, Välj en samling eller en mapp att spara den till och klicka på Spara.
    
    ![Ladda upp geofences med hjälp av Postman](./media/tutorial-geofence/postman-new.png)

2. Välj Hämta HTTP-metod på fliken builder och ange följande URL för att göra en GET-begäran.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttp%3A%2F%2Fazuremapscodesamples.azurewebsites.net%2FCommon%2Fimages%2Fpushpins%2Fylw-pushpin.png
    ```
    Följande är svar-avbildning som du får.

    ![Rendera anpassade kartnålar med etiketter](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Hämta data från Azure Maps-datalagring

> [!Note]
> Det här exemplet kräver Azure Maps-konto med prisnivån S1.

Platsinformation sökväg och PIN-koder kan också köpas genom [Data överför API: et](https://docs.microsoft.com/rest/api/maps/mapdata/upload). Följ stegen nedan för att överföra data för sökvägen och PIN-koder.

1. I Postman-appen öppnar du en ny flik i samma samling som du skapade ovan. Välj efter HTTP-metod på fliken builder och ange följande URL för att göra en POST-begäran:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. Klicka på parametrar och ange följande nyckel/värde-par som ska användas för POST-begäran-URL. Ersätt värdet för prenumerationsnyckel med din Azure Maps-prenumerationsnyckel.
    
    ![Key-Value-parametrar för Postman](./media/how-to-render-custom-data/postman-key-vals.png)

3. Klicka på **Body** (Brödtext), välj raw-indataformatet och välj JSON som indataformat i den nedrullningsbara listan. Ange följande JSON som data som ska laddas upp:
    
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

4. Klicka på Send (Skicka) och granska svarsrubriken. Platsrubriken innehåller URI för åtkomst eller nedladdning av data för framtida användning. Den innehåller även ett unikt `udId` för uppladdade data.   

  ```HTTP
  https://atlas.microsoft.com/mapData/{udId}/status?api-version=1.0&subscription-key={Subscription-key}
  ```



5. Använd värdet för `udid` togs emot från API för ladda upp att återge funktioner på kartan. Du gör detta genom att öppna en ny flik i samma samling som du skapade ovan. Välj Hämta HTTP-metod på fliken builder och ange följande URL för att göra en GET-begäran:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

6. Svaret avbildningen bör se ut så här:

    ![Rendera överförda data](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-polygon-with-color-and-opacity"></a>Rendera polygon med färg och opacitet

> [!Note]
> Det här exemplet kräver Azure Maps-konto med prisnivån S1.

Du kan ändra utseendet på en polygon med hjälp av style modifierare med den [sökvägsparameter](https://docs.microsoft.com/rest-staging/api/maps/render/getmapimage).

1. I Postman-appen öppnar du en ny flik i samma samling som du skapade ovan. Välj Hämta HTTP-metod på fliken builder och ange följande URL för att göra en GET-begäran för att återge en polygon med färg och opacitet:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription--key}
    ```

Svaret avbildningen bör se ut så här:

![Rendera täckande polygon](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-polygon-with-circle-and-push-pins-with-custom-labels"></a>Rendera polygon med cirkeln och push PIN-koder med egna etiketter

> [!Note]
> Det här exemplet kräver Azure Maps-konto med prisnivån S1.

Du kan göra kartnålar och deras etiketter större eller mindre genom att använda ”sc” skala style modifieraren. Detta är ett värde som är större än noll. Värdet 1 är standard skalan. Värden som är större än 1 gör att PIN-koder större och värden som är mindre än 1 gör dem mindre. Mer information om style modifierare visas [statisk avbildning service sökvägsparametrar](https://docs.microsoft.com/rest/api/maps/render/getmapimage).

Följ stegen nedan för att återge en polygon med cirkeln och push PIN-koder med egna etiketter:

1. I Postman-appen öppnar du en ny flik i samma samling som du skapade ovan. Välj Hämta HTTP-metod på fliken builder och ange följande URL för att göra en GET-begäran:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

Svaret avbildningen bör se ut så här:

![Rendera cirkel med anpassade PIN-koder](./media/how-to-render-custom-data/circle-custom-pins.png)

## <a name="next-steps"></a>Nästa steg

* Utforska den [Azure Maps hämta kartan Image API](https://docs.microsoft.com/rest/api/maps/search) dokumentation.
* Läs mer om Azure Maps Data Service-funktioner i [dokumentation för service](https://docs.microsoft.com/rest/api/maps/data).
