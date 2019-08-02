---
title: Så här återger du anpassade data på en raster karta i Azure Maps | Microsoft Docs
description: Återge anpassade data på en raster karta i Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b6343931287ed59363db2715641ca63a814a9c32
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638808"
---
# <a name="render-custom-data-on-a-raster-map"></a>Återge anpassade data på en raster karta

Den här artikeln förklarar hur du använder [tjänsten för statisk avbildning](https://docs.microsoft.com/rest/api/maps/render/getmapimage) med bild sammansättnings funktionen för att tillåta överlägg ovanpå en raster karta. Med bild komposition kan du få en raster panel tillbaka, med ytterligare data som anpassade kartnålar, etiketter och geometri överlägg.

Om du vill återge anpassade kartnålar, etiketter och geometri överlägg kan du använda Postman-programmet. Du kan använda [API: er för Azure Maps data tjänst](https://docs.microsoft.com/rest/api/maps/data) för att lagra och återge överlägg.


## <a name="prerequisites"></a>Förutsättningar

### <a name="create-an-azure-maps-account"></a>Skapa ett Azure Maps-konto

För att slutföra procedurerna i den här artikeln måste du först [skapa ett Azure Maps-konto](how-to-manage-account-keys.md) på pris nivån S1.

## <a name="render-pushpins-with-labels-and-a-custom-image"></a>Återge kartnålar med etiketter och en anpassad bild

> [!Note]
> Proceduren i det här avsnittet kräver ett Azure Maps konto på pris nivån S0 eller S1.

Azure Maps-kontots S0-nivå stöder endast en instans av `pins` parametern. Du kan rendera upp till fem kartnålar som anges i URL-begäran med en anpassad avbildning.

Slutför följande steg för att återge kartnålar med etiketter och en anpassad bild:

1. Skapa en samling där förfrågningarna ska lagras. I Postman-appen väljer du **ny**. I fönstret **Skapa nytt** väljer du **samling**. Namnge samlingen och välj knappen **skapa** . 

2. Välj **nytt** om du vill skapa en begäran. I fönstret **Skapa nytt** väljer du **begäran**. Ange ett **namn** på en begäran för kartnålarna, Välj den samling som du skapade i föregående steg som den plats där du vill spara begäran och välj sedan **Spara**.
    
    ![Skapa en begäran i Postman](./media/how-to-render-custom-data/postman-new.png)

3. Välj metoden Hämta HTTP på fliken Builder och ange följande URL för att skapa en GET-begäran.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttp%3A%2F%2Fazuremapscodesamples.azurewebsites.net%2FCommon%2Fimages%2Fpushpins%2Fylw-pushpin.png
    ```
    Här är den resulterande bilden:

    ![En anpassad kartnål med en etikett](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Hämta data från Azure Maps data lagring

> [!Note]
> Proceduren i det här avsnittet kräver ett Azure Maps konto i pris nivån S1.

Du kan också hämta plats informationen för sökvägen och PIN-koden genom att använda [API: et för data överföring](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). Följ stegen nedan för att ladda upp sökvägen och PIN-data.

1. Öppna en ny flik i den samling som du skapade i föregående avsnitt i Postman-appen. Välj metoden POST HTTP på fliken Builder och ange följande URL för att göra en POST-begäran:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. På fliken **parametrar** anger du följande nyckel/värde-par som används för URL: en för post-begäran. `subscription-key` Ersätt värdet med din Azure Maps prenumerations nyckel.
    
    ![Nyckel/värde-parametrar i Postman](./media/how-to-render-custom-data/postman-key-vals.png)

3. På fliken **brödtext** väljer du formatet RAW-indata och väljer JSON som indataformat i list rutan. Ange denna JSON som data som ska överföras:
    
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

4. Välj **Skicka** och granska svars huvudet. Vid en lyckad begäran kommer plats huvudet innehålla status-URI för att kontrol lera den aktuella statusen för uppladdnings förfrågan. Status-URI: n har följande format.  

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

5. Kopiera din status-URI och Lägg till prenumerations nyckel parametern till den med dess värde som din Azure Maps konto prenumerations nyckel som du använde för att överföra data. Status-URI-formatet bör se ut så här:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

6. För att hämta udId öppnar du en ny flik i Postman-appen och väljer Hämta HTTP-metod på fliken Builder och gör en GET-begäran i status-URI: n. Om din data uppladdning lyckades får du en udId i svars texten. Kopiera udId.

   ```JSON
   {
      "udid" : "{udId}"
   }
   ```

7. Använd det `udId` värde som togs emot från data överförings-API: n för att återge funktioner på kartan. Det gör du genom att öppna en ny flik i den samling som du skapade i föregående avsnitt. Välj metoden Hämta HTTP på fliken Builder och ange denna URL för att få en GET-begäran:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

    Här är svars bilden:

    ![Hämta data från Azure Maps data lagring](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>Återge en polygon med färg och opacitet

> [!Note]
> Proceduren i det här avsnittet kräver ett Azure Maps konto i pris nivån S1.


Du kan ändra utseendet på en polygon genom att använda format modifierare med [parametern Path](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).

1. Öppna en ny flik i den samling som du skapade tidigare i Postman-appen. Välj metoden Hämta HTTP på fliken Builder och ange följande URL för att konfigurera en GET-begäran för att återge en polygon med färg och opacitet:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription-key}
    ```

    Här är svars bilden:

    ![Återge en täckande polygon](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>Återge en cirkel och kartnålar med anpassade etiketter

> [!Note]
> Proceduren i det här avsnittet kräver ett Azure Maps konto i pris nivån S1.


Du kan göra kartnålar och deras etiketter större eller mindre med hjälp `sc` av modifieraren skalnings format. Den här modifieraren tar ett värde som är större än noll. Värdet 1 är standard skalan. Värden som är större än 1 gör PIN-koderna större och värden som är mindre än 1 blir mindre. Mer information om format modifierare finns i [Parametrar för statisk avbildnings tjänst Sök väg](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).


Följ dessa steg om du vill återge en cirkel och kartnålar med anpassade etiketter:

1. Öppna en ny flik i den samling som du skapade tidigare i Postman-appen. Välj metoden Hämta HTTP på fliken Builder och ange denna URL för att få en GET-begäran:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Här är svars bilden:

    ![Återge en cirkel med anpassade kartnålar](./media/how-to-render-custom-data/circle-custom-pins.png)

## <a name="next-steps"></a>Nästa steg


* Utforska [Azure Maps Hämta avbildnings-API](https://docs.microsoft.com/rest/api/maps/render/getmapimage) -dokumentation.
* Mer information om Azure Maps data service finns i [tjänst dokumentationen](https://docs.microsoft.com/rest/api/maps/data).

