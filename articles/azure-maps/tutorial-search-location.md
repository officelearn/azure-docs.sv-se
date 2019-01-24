---
title: Söka med Azure Maps | Microsoft Docs
description: Söka efter orienteringspunkter i närheten med hjälp av Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 01/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 04ab94d8b0b8f012707bb88a52c44b91063cbe39
ms.sourcegitcommit: c31a2dd686ea1b0824e7e695157adbc219d9074f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2019
ms.locfileid: "54402191"
---
# <a name="search-nearby-points-of-interest-using-azure-maps"></a>Söka efter orienteringspunkter i närheten med hjälp av Azure Maps

Den här självstudiekursen visar hur du skapar ett konto med Azure Maps och sedan använder API:er för Maps för att söka efter en orienteringspunkt. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett Azure Maps-konto
> * Hämta primärnyckeln för ditt Maps-konto
> * Skapa en ny webbsida med API:n för kartkontroll
> * Använda Maps-söktjänsten för att hitta orienteringspunkter i närheten

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>Skapa ett konto med Azure Maps

Skapa ett nytt Maps-konto med följande steg:

1. Klicka på **Skapa en resurs** längst upp till vänster i [Azure Portal](https://portal.azure.com).
2. Skriv **Maps** i rutan *Sök på Marketplace*.
3. Bland *resultaten* väljer du **Maps**. Klicka på knappen **Skapa** som visas nedanför kartan.
4. Ange följande värden på sidan **Skapa Maps-konto**:
    * *Namn* för ditt nya konto.
    * Den *Prenumeration* som ska användas för det här kontot.
    * Namnet på *Resursgrupp* för kontot. Du kan välja att *skapa ny* eller *använda befintlig* resursgrupp.
    * Välj en *Resursgruppsplats*.
    * Läs *licensen* och *sekretesspolicyn* och markera kryssrutan för att godkänna villkoren.
    * Klicka på knappen **Skapa**.

    ![Skapa Maps-konto i portalen](./media/tutorial-search-location/create-account.png)

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Hitta primärnyckeln för ditt konto

När ditt Maps-konto har skapats hämtar du nyckeln som gör att du kan fråga API:et Kartkontroll.

1. Öppna ditt Maps-konto i portalen.
2. I fönstret Inställningar, väljer du **Nycklar**.
3. Kopiera **Primärnyckel** till Urklipp. Spara den lokalt för senare användning i den här självstudien.

    ![Hämta primärnyckel i portalen](./media/tutorial-search-location/get-key.png)

<a id="createmap"></a>

## <a name="create-a-new-map"></a>Skapa en ny karta

API:et Kartkontroll är ett praktiskt klientbiblioteket som hjälper dig att enkelt integrera Maps i ditt webbprogram. Det döljer komplexiteten i de rena REST-tjänstanropen och ökar produktiviteten med formateringsbara och anpassningsbara komponenter. Följande steg visar hur du skapar en statisk HTML-sida inbäddad med API:et Kartkontroll.

1. Skapa en ny fil på den lokala datorn och ge den namnet **MapSearch.html**.
2. Lägg till följande HTML-komponenter i filen:

   ```HTML
   <!DOCTYPE html>
   <html>
   <head>
      <title>Map Search</title>
      <meta charset="utf-8" />
      <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

      <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
      <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>

      <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
      <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1"></script>

      <script>      
         var map, datasource, client, popup;

         function GetMap(){
            //Add Map Control JavaScript code here.
         }
      </script>
      <style>
      html,
      body {
         width: 100%;
         height: 100%;
         padding: 0;
         margin: 0;
      }

      #myMap {
         width: 100%;
         height: 100%;
      }
      </style>
   </head>
   <body onload="GetMap()">
      <div id="myMap"></div>
   </body>
   </html>
   ```

   Observera att HTML-huvudet innehåller CSS- och JavaScriptresursfiler som med Azure Kartkontroll-biblioteket som värd. Observera `onload`-händelsen i innehållet på sidan, som anropar funktionen `GetMap` när sidans innehåll har lästs in. Den här funktionen innehåller infogad JavaScript-kod för att komma åt API:et i Azure Maps.

3. Lägg till följande JavaScript-kod i HTML-filens `GetMap`-funktion. Ersätt strängen **\<Din Azure Maps-nyckel\>** med den primärnyckel som du kopierade från Maps-kontot.

   ```JavaScript
   //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
   atlas.setSubscriptionKey('<Your Azure Maps Key>');

   //Initialize a map instance.
   map = new atlas.Map('myMap');
   ```

   Det här segmentet initierar API:et Kartkontroll för din Azure Maps-kontonyckel. **Atlas** är det namnområde som innehåller API:et och relaterade visuella komponenter. **atlas.Map** ger kontroll över en visuell och interaktiv webbkarta. 

4. Spara dina ändringar i filen och öppna HTML-sidan i en webbläsare. Det här är den mest grundläggande mappningen du kan göra genom att anropa **atlas.map** med hjälp av din kontonyckel.

   ![Visa kartan](./media/tutorial-search-location/basic-map.png)

5. I funktionen `GetMap`, när du har initierat kartan, lägger du till följande JavaScript-kod. 

   ```JavaScript
   //Wait until the map resources have fully loaded.
   map.events.add('load', function () {

      //Create a data source and add it to the map.
      datasource = new atlas.source.DataSource();
      map.sources.add(datasource);

      //Add a layer for rendering point data.
      var resultLayer = new atlas.layer.SymbolLayer(datasource, null, {
         iconOptions: {
            iconImage: 'pin-round-darkblue',
            anchor: 'center',
            allowOverlap: true
         }
      });
      map.layers.add(resultLayer);

   });
   ```

   En inläsningshändelse har lagts till på kartan, som utlöses när kartresurserna har lästs in helt. I händelsehanteraren för kartinläsning skapas en datakälla för att lagra resultatdata. Ett symbollager skapas och ansluts till datakällan. Det här lagret anger hur resultatdata i datakällan ska renderas, i det här fallet ned en mörkblå rund nålikon som är centrerad över resultatkoordinaten och som tillåter andra ikoner att överlappa. 

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>Lägga till sökfunktioner

Det här avsnittet visar hur du API:et Maps Search för att hitta en orienteringspunkt på kartan. Det är ett RESTful-API för utvecklare för att söka efter adresser, orienteringspunkter och annan geografisk information. Search Service tilldelar en angiven adress latitud- och longitudinformation. **Tjänstemodulen** som beskrivs nedan kan användas till att söka efter en plats med hjälp av API:et för kartsökning.

### <a name="service-module"></a>Tjänstmodul

1. I händelsehanteraren för kartinläsnings skapar du en instans av klienttjänsten genom att lägga till följande Javascript-kod.

    ```JavaScript
    //Create an instance of the services client.
     client = new atlas.service.Client(atlas.getSubscriptionKey());
    ```

2. Lägg därefter till följande skriptblock för att skapa sökfrågan. Den använder det enkla söknings-API:et i Search Service, som kallas Fuzzy Search. Fuzzy Search-tjänsten hanterar de flesta fuzzy-indata som adresser, platser och platser av intresse (POI). Den här koden söker efter närliggande bensinstationer inom angiven radie. Svaret parsas sedan i GeoJSON-format och läggs till i datakällan, som automatiskt resulterar i att data renderas på kartan via symbollagret. Den sista delen av skriptet ställer in kameravyn med hjälp av avgränsningsrektangeln för resultat med kartans [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)-egenskap. En utfyllnad läggs till för att kompensera för pixeldimensionerna för symbolikonerna när avgränsningsrektangeln beräknas baserat på koordinaterna. 
 
   ```JavaScript
   //Execute a POI search query then add the results to the map.
    client.search.getSearchPOI('gasoline station', {
        lat: 47.6292,
        lon: -122.2337,
        radius: 100000
    }).then(response => {
        //Parse the response into GeoJSON so that the map can understand.
        var geojsonResponse = new atlas.service.geojson.GeoJsonSearchResponse(response);
        var results = geojsonResponse.getGeoJsonResults();

        //Add the results to the data source so they can be rendered. 
        datasource.add(results);

        // Set the camera bounds
        map.setCamera({
            bounds: results.bbox,
            padding: 50
        });
    });
   ```
 
3. Spara filen **MapSearch.html** och uppdatera webbläsaren. Du bör nu se att kartan är centrerad i Seattle med blå kartnålar som markerar platserna för områdets bensinstationer.

   ![Visa kartan med sökresultat](./media/tutorial-search-location/pins-map.png)

4. Du kan visa rådata som kartan återger genom att ange följande HTTP-förfrågan i webbläsaren. Ersätt \<din Azure Maps-nyckel\> med din primärnyckel.

   ```http
   https://atlas.microsoft.com/search/fuzzy/json?api-version=1.0&query=gasoline%20station&subscription-key=<Your Azure Maps Key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

I det här läget kan MapSearch-sidan visa orienteringspunkterna som returneras från en fuzzy-sökfråga. Lägg till vissa interaktiva funktioner och mer information om platser.

## <a name="add-interactive-data"></a>Lägga till interaktiva data

Karta som har vi gjort tittar hittills bara på longitud-/latituddata för sökresultaten. Om du tittar på den oformaterade JSON som Maps-söktjänsten returnerar ser du att den innehåller ytterligare information om varje bensinstation, inklusive namn och adress. Du kan införliva dessa data i kartan med interaktiva popup-rutor.

1. Lägg till följande kodrader i händelsehanteraren för kartinläsning efter koden för att fråga Fuzzy Search-tjänsten. Det skapar en instans av en popup-fönster och lägger till en muspekarhändelse i symbollagret.

    ```JavaScript
   //Create a popup but leave it closed so we can update it and display it later.
    popup = new atlas.Popup();

    //Add a mouse over event to the result layer and display a popup when this event fires.
    map.events.add('mouseover', resultLayer, showPopup);
    ```
    
    API:et **atlas. Popup** ger ett informationsfönster som är fäst vid motsvarande position på kartan. 
      
2. I *script*-taggen, efter `GetMap`-funktionen lägger du till följande kod för att visa hovringsresultatinformationen i popup-fönstret. 

   ```JavaScript
   function showPopup(e) {
        //Get the properties and coordinates of the first shape that the event occurred on.
        var p = e.shapes[0].getProperties();
        var position = e.shapes[0].getCoordinates();

        //Create HTML from properties of the selected result.
        var html = ['<div style="padding:5px"><div><b>', p.poi.name,
            '</b></div><div>', p.address.freeformAddress,
            '</div><div>', position[1], ', ', position[0], '</div></div>'];

        //Update the content and position of the popup.
        popup.setPopupOptions({
            content: html.join(''),
            position: position
        });

        //Open the popup.
        popup.open(map);
   }
   ```

2. Spara filen och uppdatera webbläsaren. Nu visar kartan i webbläsaren när du hovrar över någon av sökningskartnålarna.

    ![Azure Kartkontroll och Search Service](./media/tutorial-search-location/popup-map.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett konto med Azure Maps
> * Hitta primärnyckeln för ditt konto
> * Skapa ny webbsida med API:et Kartkontroll
> * Använda Search Service för att hitta orienteringspunkter i närheten

Du kan komma åt kodexemplet för den här självstudien här:

> [Sök efter plats med Azure Maps](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/search.html)

[Se exemplet live här](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20for%20points%20of%20interest)

Nästa självstudie demonstrerar hur du visar en väg mellan två platser.

> [!div class="nextstepaction"]
> [Dirigera mot ett mål](./tutorial-route-location.md)
