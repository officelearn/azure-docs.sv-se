---
title: Återge anpassade data på en rasterkarta | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du renderar anpassade data på en rasterkarta med hjälp av Microsoft Azure Maps statiska avbildningstjänst.
author: philmea
ms.author: philmea
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b8d47b69b4aba14c86fb09176b662aee7d5482d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335521"
---
# <a name="render-custom-data-on-a-raster-map"></a>Återge anpassade data på en rasterkarta

I den här artikeln beskrivs hur du använder den [statiska bildtjänsten](https://docs.microsoft.com/rest/api/maps/render/getmapimage), med bildkompositionsfunktioner, för att tillåta överlagringar ovanpå en rasterkarta. Bildkompositionen innehåller möjligheten att få tillbaka en rasterpanel, med ytterligare data som anpassade kartnålar, etiketter och geometriöverlägg.

Om du vill rendera anpassade kartnålar, etiketter och geometriöverlägg kan du använda Postman-programmet. Du kan använda Azure Maps [Data Service API:er](https://docs.microsoft.com/rest/api/maps/data) för att lagra och återge överlägg.

> [!Tip]
> Det är ofta mycket mer kostnadseffektivt att använda Azure Maps Web SDK för att visa en enkel karta på en webbsida än att använda den statiska bildtjänsten. Webben SDK använder kartpaneler och om inte användaren panorerar och zoomar kartan genererar de ofta bara en bråkdel av en transaktion per kartbelastning. Observera att Azure Maps web SDK har alternativ för att inaktivera panorering och zoomning. Dessutom ger Azure Maps web SDK en rikare uppsättning datavisualiseringsalternativ än vad en statisk kartwebbtjänst gör.  

## <a name="prerequisites"></a>Krav

### <a name="create-an-azure-maps-account"></a>Skapa ett Azure Maps-konto

För att slutföra procedurerna i den här artikeln måste du först skapa ett Azure Maps-konto och hämta nyckeln till ditt maps-konto. Följ instruktionerna i [Skapa ett konto](quick-demo-map-app.md#create-an-account-with-azure-maps) för att skapa en Azure Maps-kontoprenumeration och följ stegen för att få [primärnyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account) för att få den primära nyckeln för ditt konto. Mer information om autentisering i Azure Maps finns [i hantera autentisering i Azure Maps](./how-to-manage-authentication.md).


## <a name="render-pushpins-with-labels-and-a-custom-image"></a>Rendera kartnålar med etiketter och en anpassad bild

> [!Note]
> Proceduren i det här avsnittet kräver ett Azure Maps-konto på prisnivå S0 eller S1.

Azure Maps-kontot S0 stöder endast en `pins` enda instans av parametern. Det gör att du kan återge upp till fem kartnålar, som anges i URL-begäran, med en anpassad bild.

Så här gör du kartnålar med etiketter och en anpassad bild:

1. Skapa en samling där begäranden ska lagras. Välj **Ny**i Postman-appen . Välj **Samling**i fönstret **Skapa nytt** . Namnge samlingen och välj knappen **Skapa.** 

2. Om du vill skapa begäran väljer du **Ny** igen. Välj **Begär**i fönstret **Skapa nytt** . Ange ett **begärandenamn** för kartnålarna. Välj den samling som du skapade i föregående steg, som plats för att spara begäran. Välj sedan **Spara**.
    
    ![Skapa en begäran i Postman](./media/how-to-render-custom-data/postman-new.png)

3. Välj metoden GET HTTP på fliken Builder och ange följande URL för att skapa en GET-begäran.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttps%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FAzureMapsCodeSamples%2Fmaster%2FAzureMapsCodeSamples%2FCommon%2Fimages%2Ficons%2Fylw-pushpin.png
    ```
    Här är den resulterande bilden:

    ![En anpassad kartnål med en etikett](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Hämta data från Azure Maps datalagring

> [!Note]
> Proceduren i det här avsnittet kräver ett Azure Maps-konto på prisnivå S1.

Du kan också hämta sökvägs- och fästplatsinformationen med hjälp av [API:et för dataöverföring](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). Följ stegen nedan för att ladda upp sökvägen och stiftdata.

1. Öppna en ny flik i samlingen som du skapade i föregående avsnitt i Postman-appen. Välj metoden POST HTTP på fliken Builder och ange följande URL för att göra en POST-begäran:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. På fliken **Params** anger du följande nyckel-/värdepar som används för URL:en postbegäran. Ersätt `subscription-key` värdet med din Azure Maps-prenumerationsnyckel.
    
    ![Nyckel-/värdeparams i Brevbäraren](./media/how-to-render-custom-data/postman-key-vals.png)

3. På fliken **Brödtext** väljer du råinmatningsformatet och väljer JSON som indataformat i listrutan. Ange detta JSON som data som ska laddas upp:
    
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

4. Välj **Skicka** och granska svarshuvudet. När en lyckad begäran har slutförts innehåller platshuvudet status-URI för att kontrollera aktuell status för överföringsbegäran. Status-URI:n skulle vara av följande format.  

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

5. Kopiera din status-URI och lägg till parametern för prenumerationsnyckel till den med värdet för prenumerationsnyckeln för Ditt Azure Maps-konto. Använd samma kontoprenumerationsnyckel som du använde för att överföra data. Status-URI-formatet ska se ut som det nedan:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

6. Om du vill hämta udId öppnar du en ny flik i Postman-appen. Välj GET HTTP-metod på fliken Builder. Gör en GET-begäran på status-URI. Om dataöverföringen lyckades får du en udId i svarstexten. Kopiera udId.

   ```JSON
   {
      "udid" : "{udId}"
   }
   ```

7. Använd `udId` värdet som tas emot från API:et för dataöverföring för att återge funktioner på kartan. Det gör du genom att öppna en ny flik i samlingen som du skapade i föregående avsnitt. Välj METODEN GET HTTP på fliken Builder, ersätt {subscription-key} och {udId} med dina värden och ange den här URL:en för att göra en GET-begäran:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

    Här är svarsbilden:

    ![Hämta data från Azure Maps datalagring](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>Återge en polygon med färg och opacitet

> [!Note]
> Proceduren i det här avsnittet kräver ett Azure Maps-konto på prisnivå S1.


Du kan ändra utseendet på en polygon genom att använda formatmodifierare med [banparametern](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).

1. Öppna en ny flik i samlingen som du skapade tidigare i Postman-appen. Välj METODEN GET HTTP på fliken Builder och ange följande URL för att konfigurera en GET-begäran om att återge en polygon med färg och opacitet:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription-key}
    ```

    Här är svarsbilden:

    ![Återge en ogenomskinlig polygon](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>Återge en cirkel och kartnålar med anpassade etiketter

> [!Note]
> Proceduren i det här avsnittet kräver ett Azure Maps-konto på prisnivå S1.


Du kan ändra utseendet på stiften genom att lägga till formatmodifierare. Om du till exempel vill göra kartnålar och `sc` deras etiketter större eller mindre använder du modifieraren "skalformat". Den här modifieraren tar ett värde som är större än noll. Värdet 1 är standardskalan. Värden som är större än 1 gör stiften större, och värden som är mindre än 1 gör dem mindre. Mer information om formatmodifierare finns i [statiska parametrar för bildtjänstsökväg](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).


Följ dessa steg för att återge en cirkel och kartnålar med anpassade etiketter:

1. Öppna en ny flik i samlingen som du skapade tidigare i Postman-appen. Välj metoden GET HTTP på fliken Builder och ange den här URL:en för att göra en GET-begäran:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Här är svarsbilden:

    ![Återge en cirkel med anpassade kartnålar](./media/how-to-render-custom-data/circle-custom-pins.png)

2. Om du vill ändra färgen på kartnålarna från det sista steget ändrar du modifieraren i "co"-stil. Titta `pins=default|la15+50|al0.66|lc003C62|co002D62|`på, skulle den aktuella färgen anges som #002D62 i CSS. Anta att du vill ändra den till #41d42a. Skriv det nya färgvärdet efter "co"-specificeraren, så här: `pins=default|la15+50|al0.66|lc003C62|co41D42A|`. Gör en ny GET-begäran:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co41D42A||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Här är svarsbilden efter att ha ändrat färgerna på stiften:

    ![Återge en cirkel med uppdaterade kartnålar](./media/how-to-render-custom-data/circle-updated-pins.png)

På samma sätt kan du ändra, lägga till och ta bort andra formatmodifierare.

## <a name="next-steps"></a>Nästa steg


* Utforska [Azure Maps Hämta API-dokumentation för kartavbildning.](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* Mer information om Azure Maps Data Service finns i [tjänstdokumentationen](https://docs.microsoft.com/rest/api/maps/data).

