---
title: 'Handledning: Migrera en webbapp från Google Maps | Microsoft Azure Maps'
description: Så här migrerar du en webbapp från Google Maps till Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: bdbf2a975cbdc3d06745b9375c1e6f8e751ddfd6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77914108"
---
# <a name="migrate-a-web-app-from-google-maps"></a>Migrera en webbapp från Google Maps

De flesta webbappar, som använder Google Maps, använder Google Maps V3 JavaScript SDK. Azure Maps Web SDK är lämplig Azure-baserad SDK att migrera till. Med Azure Maps Web SDK kan du anpassa interaktiva kartor med ditt eget innehåll och dina egna bilder. Du kan köra appen på både webb- eller mobilappar. Den här kontrollen använder WebGL, så att du kan rendera stora datauppsättningar med höga prestanda. Utveckla med denna SDK med JavaScript eller TypeScript.

Om du migrerar ett befintligt webbprogram kontrollerar du om det använder ett kartkontrollbibliotek med öppen källkod. Exempel på kartkontrollbibliotek med öppen källkod är Cesium, Leaflet och OpenLayers. Du kan fortfarande migrera ditt program, även om det använder ett kartkontrollbibliotek med öppen källkod och du inte vill använda Azure Maps Web SDK. I så fall ansluter du ditt program till Azure Maps-paneltjänsterna \| [(satellitpaneler](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)[för vägplattor).](https://docs.microsoft.com/rest/api/maps/render/getmaptile) Följande punkter beskrivs i detalj om hur du använder Azure Maps i några vanliga kartkontrollbibliotek med öppen källkod.

- Cesium - En 3D-kartkontroll för webben. Dokumentation [för kodexempel](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [Documentation](https://cesiumjs.org/)
- Broschyr – Lätt 2D-kartkontroll för webben. Dokumentation [för kodexempel](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [Documentation](https://leafletjs.com/)
- OpenLayers - En 2D-kartkontroll för webben som stöder projektioner. Dokumentation [för kodexempel](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [Documentation](https://openlayers.org/)

## <a name="key-features-support"></a>Stöd för viktiga funktioner

Tabellen visar viktiga API-funktioner i Google Maps V3 JavaScript SDK och api-funktionen som stöds i Azure Maps Web SDK.

| Google Maps-funktionen     | Support för Azure Maps Web SDK |
|-------------------------|:--------------------------:|
| Markörer                 | ✓                          |
| Markörkluster       | ✓                          |
| Polyliner & Polygoner    | ✓                          |
| Datalager             | ✓                          |
| Marköverlägg         | ✓                          |
| Värme kartor               | ✓                          |
| Lager med panel             | ✓                          |
| KML-skikt               | ✓                          |
| Ritningsverktyg           | ✓                          |
| Geokodrtjänst        | ✓                          |
| Vägbeskrivningstjänst      | ✓                          |
| Tjänst för avståndsmatris | ✓                          |
| Höjdtjänst       | Planerad                    |

## <a name="notable-differences-in-the-web-sdks"></a>Anmärkningsvärda skillnader i webben SDK

Följande är några viktiga skillnader mellan Google Maps och Azure Maps Web SDK: er, att vara medveten om:

- Förutom att tillhandahålla en värdslutspunkt för åtkomst till Azure Maps Web SDK är ett NPM-paket tillgängligt. Bädda in Web SDK-paketet i appar. Mer information finns i den här [dokumentationen](how-to-use-map-control.md). Det här paketet innehåller även TypeScript-definitioner.
- Du måste först skapa en instans av klassen Map i Azure Maps. Vänta tills `ready` `load` kartorna eller händelsen har avfyrats innan du interagerar programmässigt med kartan. Den här ordern säkerställer att alla kartresurser har lästs in och är redo att nås.
- Båda plattformarna använder ett liknande plattsättningssystem för baskartorna. Panelerna i Google Maps är 256 pixlar i dimension. Panelerna i Azure Maps är dock 512 pixlar i dimensionen. Om du vill få samma kartvy i Azure Maps som Google Maps subtraherar du Zoomnivån för Google Maps med nummer ett i Azure Maps.
- Koordinater i Google Maps kallas "latitud, longitud", medan Azure Maps använder "longitud,latitud". Azure Maps-formatet är anpassat `[x, y]`till standarden , som följs av de flesta GIS-plattformar.
- Former i Azure Maps Web SDK baseras på GeoJSON-schemat. Hjälpklasser exponeras via [ *namnområdet atlas.data* ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data?view=azure-iot-typescript-latest). Det finns också [*atlasen. Formklass.*](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape) Använd den här klassen för att radbrytas GeoJSON-objekt, för att göra det enkelt att uppdatera och underhålla det databindbara sättet.
- Koordinater i Azure Maps definieras som positionsobjekt. En koordinat anges som en `[longitude,latitude]`talmatris i formatet . Eller så anges det med hjälp av nya atlas.data.Position(longitud, latitud).
    > [!TIP]
    > Klassen Position har en statisk hjälpmetod för import av koordinater som är i formatet "latitud, longitud". [Metoden atlas.data.Position.fromLatLng](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest) kan ofta ersättas `new google.maps.LatLng` med metoden i Google Maps-koden.
- I stället för att ange formateringsinformation på varje form som läggs till på kartan separerar Azure Maps format från data. Data lagras i en datakälla och är ansluten till renderingslager. Azure Maps-kod använder datakällor för att återge data. Den här metoden ger bättre prestandafördelar. Dessutom stöder många lager datadriven styling där affärslogik kan läggas till i lagerformatalternativ. Det här stödet ändrar hur enskilda former återges i ett lager baserat på egenskaper som definierats i formen.

## <a name="web-sdk-side-by-side-examples"></a>Webb-SDK sida vid sida-exempel

Den här samlingen har kodexempel för varje plattform, och varje exempel täcker ett vanligt användningsfall. Den är avsedd att hjälpa dig att migrera ditt webbprogram från Google Maps V3 JavaScript SDK till Azure Maps Web SDK. Kodexempel relaterade till webbprogram finns i JavaScript. Azure Maps innehåller dock också TypeScript-definitioner som ett ytterligare alternativ via en [NPM-modul](how-to-use-map-control.md).

### <a name="load-a-map"></a>Ladda en karta

Båda SDK:erna har samma steg för att läsa in en karta:

- Lägg till en referens till Map SDK.
- Lägg `div` till en tagg i sidans brödtext, som fungerar som platshållare för kartan.
- Skapa en JavaScript-funktion som anropas när sidan har lästs in.
- Skapa en förekomst av respektive kartklass.

**Några viktiga skillnader**

- Google Maps kräver att en kontonyckel anges i skriptreferensen för API:et. Autentiseringsuppgifter för Azure Maps anges som alternativ för kartklassen. Den här autentiseringsen kan vara en prenumerationsnyckel eller Azure Active Directory-information.
- Google Maps accepterar en motringningsfunktion i skriptreferensen för API:et, som används för att anropa en initieringsfunktion för att läsa in kartan. Med Azure Maps ska onload-händelsen på sidan användas.
- När du refererar `div` till det element där kartan `Map` återges kräver klassen `id` i Azure Maps `HTMLElement` bara värdet medan Google Maps kräver ett objekt.
- Koordinater i Azure Maps definieras som positionsobjekt, som kan `[longitude, latitude]`anges som en enkel talmatris i formatet .
- Zoomnivån i Azure Maps är en nivå lägre än zoomnivån i Google Maps. Denna diskrepans beror på att skillnaden i storleken på plattsättningssystemet för de två plattformarna.
- Azure Maps lägger inte till några navigeringskontroller på kartarbetsytan. Som standard har en karta därför inte zoomknappar och kartstilsknappar. Men det finns kontrollalternativ för att lägga till en kartstilväljare, zoomknappar, kompass- eller rotationskontroll och en tonhöjdskontroll.
- En händelsehanterare läggs till i Azure `ready` Maps för att övervaka händelsen för kartinstansen. Den här händelsen startas när kartan har lästs in i WebGL-kontexten och alla nödvändiga resurser. Lägg till valfri kod som du vill köra när kartan har lästs in i den här händelsehanteraren.

De grundläggande exemplen nedan använder Google Maps för att läsa in en karta centrerad över New York vid koordinater. Longituden: -73.985, latitud: 40.747, och kartan är på zoomnivå av 12.

**Före: Google Maps**

Visa en Google Map centrerad och zoomad över en plats.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Om du kör den här koden i en webbläsare visas en karta som ser ut som följande bild:

<center>

![Enkla Google Maps](media/migrate-google-maps-web-app/simple-google-map.png)</center>

**Efter: Azure Maps**

Ladda en karta med samma vy i Azure Maps tillsammans med en kontroll och zoomknappar i kartstil.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-73.985, 40.747],  //Format coordinates as longitude, latitude.
                zoom: 11,   //Subtract the zoom level by one.

                //Specify authentication information when loading the map.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom controls to bottom right of map.
                map.controls.add(new atlas.control.ZoomControl(), {
                    position: 'bottom-right'
                });

                //Add map style control in top left corner of map.
                map.controls.add(new atlas.control.StyleControl(), {
                    position: 'top-left'
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Om du kör den här koden i en webbläsare visas en karta som ser ut som följande bild:

<center>

![Enkla Azure-kartor](media/migrate-google-maps-web-app/simple-azure-maps.png)</center>

Hitta detaljerad dokumentation om hur du konfigurerar och använder kartkontrollen För Azure Maps i en webbapp genom att klicka [här](how-to-use-map-control.md).

> [!NOTE]
> Till skillnad från Google Maps kräver Azure Maps inte ett första center och en zoomnivå för att läsa in kartan. Om den här informationen inte tillhandahålls när kartan läses in försöker Azure-mappningar avgöra användarens stad. Det kommer att centrera och zooma kartan där.

**Ytterligare resurser:**

- Azure Maps tillhandahåller också navigeringskontroller för att rotera och slå upp kartvyn, vilket dokumenteras [här](map-add-controls.md).

### <a name="localizing-the-map"></a>Lokalisera kartan

Om målgruppen är spridd över flera länder eller talar olika språk är lokalisering viktigt.

**Före: Google Maps**

Om du vill lokalisera Google Maps lägger du till språk- och regionparametrar.

```html
<script type="text/javascript" src=" https://maps.googleapis.com/maps/api/js?callback=initMap&key=[api_key]& language=[language_code]&region=[region_code]" async defer></script>
```

Här är ett exempel på Google Maps med språket inställt på "fr-FR".

<center>

![Anpassning av Google Maps](media/migrate-google-maps-web-app/google-maps-localization.png)</center>

**Efter: Azure Maps**

Azure Maps innehåller två olika sätt att ange språket och den regionala vyn på kartan. Det första alternativet är att lägga *atlas* till den här informationen i det globala atlasnamnområdet. Det resulterar i att alla kartkontrollinstanser i appen som standard är dessa inställningar. Följande ställer språket till franska ("fr-FR") och den regionala vyn till "auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

Det andra alternativet är att skicka den här informationen till kartalternativen när du läser in kartan. Gillar det här:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

> [!NOTE]
> Med Azure Maps är det möjligt att läsa in flera kartinstanser på samma sida med olika språk- och regioninställningar. Det är också möjligt att uppdatera dessa inställningar på kartan när den har lästs in. 

Hitta en detaljerad lista över [språk som stöds](supported-languages.md) i Azure Maps.

Här är ett exempel på Azure Maps med språket inställt på "fr" och användarregionen inställd på "fr-FR".

<center>

![Lokalisering av Azure Maps](media/migrate-google-maps-web-app/azure-maps-localization.png)</center>

### <a name="setting-the-map-view"></a>Ställa in kartvyn

Dynamiska kartor i både Azure och Google Maps kan flyttas programmässigt till nya geografiska platser. Om du vill göra det anropar du lämpliga funktioner i JavaScript. Exemplen visar hur du gör satellitens satellitbilder, centrerar kartan över en plats och ändrar zoomnivån till 15 i Google Maps. Följande platskoordinater används: longitud: -111.0225 och latitud: 35.0272.

> [!NOTE]
> Google Maps använder paneler som är 256 pixlar i dimensioner, medan Azure Maps använder en större 512-pixelpanel. Azure Maps kräver därför färre nätverksbegäranden för att läsa in samma kartområde som Google Maps. På grund av hur panelpyramider fungerar i kartkontroller måste du subtrahera zoomnivån som används i Google Maps med nummer ett när du använder Azure Maps. Den här aritmetiska åtgärden säkerställer att större paneler i Azure Maps återger samma kartområde som i Google Maps.

**Före: Google Maps**

Flytta kartkontrollen google maps `setOptions` med hjälp av metoden. Med den här metoden kan du ange mitten av kartan och en zoomnivå.

```javascript
map.setOptions({
    mapTypeId: google.maps.MapTypeId.SATELLITE,
    center: new google.maps.LatLng(35.0272, -111.0225),
    zoom: 15
});
```

<center>

![Ställ vy över Google Maps](media/migrate-google-maps-web-app/google-maps-set-view.png)</center>

**Efter: Azure Maps**

I Azure Maps ändrar du `setCamera` kartpositionen med metoden `setStyle` och ändrar kartformatet med hjälp av metoden. Koordinaterna i Azure Maps är i formatet "longitud, latitud" och zoomnivåvärdet subtraheras av en.

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite'
});
```

<center>

![Uppsättning vy för Azure Maps](media/migrate-google-maps-web-app/azure-maps-set-view.jpeg)</center>

**Ytterligare resurser:**

- [Välj ett kartformat](choose-map-style.md)
- [Kartformat som stöds](supported-map-styles.md)

### <a name="adding-a-marker"></a>Lägga till en markör

I Azure Maps finns det flera sätt på vilka punktdata kan återges på kartan:

- **HTML-markörer** – Återger punkter med traditionella DOM-element. HTML-markörer stöder dragen.
- **Symbollager** – Återger punkter med en ikon eller text i WebGL-kontexten.
- **Bubbellager** – Återger punkter som cirklar på kartan. Cirkelns radier kan skalas baserat på egenskaper i data.

Rendera symbollager och bubbellager i WebGL-kontexten. Båda lagren kan återge stora punkter på kartan. Dessa lager kräver att data lagras i en datakälla. Datakällor och renderingslager bör läggas `ready` till på kartan när händelsen har avlossats. HTML-markörer återges som DOM-element på sidan och de använder inte en datakälla. Ju fler DOM-element en sida har, desto långsammare blir sidan. Om du återger mer än några hundra punkter på en karta rekommenderas att du använder ett av renderingslagren i stället.

Låt oss lägga till en markör på kartan med siffran 10 överlagrad som en etikett. Använd longitud: -0,2 och latitud: 51,5.

**Före: Google Maps**

Med Google Maps lägger du till `google.maps.Marker` markörer på kartan med klassen och anger kartan som ett av alternativen.

```javascript
//Create a marker and add it to the map.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    label: '10',
    map: map
});
```

<center>

![Google Maps markör](media/migrate-google-maps-web-app/google-maps-marker.png)</center>

**Efter: Azure Maps med HTML-markörer**

I Azure Maps använder du HTML-markörer för att visa en punkt på kartan. HTML-markörer rekommenderas för appar som bara behöver visa ett litet antal punkter på kartan. Om du vill använda en HTML-markör skapar du en förekomst av `atlas.HtmlMarker` klassen. Ange text- och placeringsalternativ och lägg till `map.markers.add` markören på kartan med hjälp av metoden.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

<center>

![HTML-markör för Azure Maps](media/migrate-google-maps-web-app/azure-maps-html-marker.png)</center>

**Efter: Azure Maps med hjälp av ett symbollager**

För ett symbollager lägger du till data i en datakälla. Koppla datakällan till lagret. Dessutom bör datakällan och lagret läggas till `ready` på kartan när händelsen har avlossats. Om du vill återge ett unikt textvärde ovanför en symbol måste textinformationen lagras som en egenskap för datapunkten. Egenskapen måste refereras `textField` i alternativet för lagret. Den här metoden är lite mer arbete än att använda HTML-markörer, men det bättre prestanda.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Create a point feature, add a property to store a label for it, and add it to the data source.
                datasource.add(new atlas.data.Feature(new atlas.data.Point([-0.2, 51.5]), {
                    label: '10'
                }));

                //Add a layer for rendering point data as symbols.
                map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                    textOptions: {
                        //Use the label property to populate the text for the symbols.
                        textField: ['get', 'label'],
                        color: 'white',
                        offset: [0, -1]
                    }
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Symbollagret i Azure Maps](media/migrate-google-maps-web-app/azure-maps-symbol-layer.png)</center>

**Ytterligare resurser:**

- [Skapa en datakälla](create-data-source-web-sdk.md)
- [Lägga till ett symbollager](map-add-pin.md)
- [Lägga till ett bubbellager](map-add-bubble-layer.md)
- [Klusterpunktdata](clustering-point-data-web-sdk.md)
- [Lägga till HTML-markörer](map-add-custom-html.md)
- [Använda datadrivna formatuttryck](data-driven-style-expressions-web-sdk.md)
- [Alternativ för symbollager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Alternativet Text för symbollager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [Klassen HTML-markör](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [Alternativ för HTML-markör](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-custom-marker"></a>Lägga till en anpassad markör

Du kan använda anpassade bilder för att representera punkter på en karta. Kartan nedan använder en anpassad bild för att visa en punkt på kartan. Punkten visas vid latitud: 51,5 och longitud: -0,2. Ankaret förskjuter markörens position så att pushpinikonens punkt ligger i linje med rätt position på kartan.

<center>

![gul pushpin-bild](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_pushpin.png</center>

**Före: Google Maps**

Skapa en anpassad markör `Icon` genom att `url` ange ett objekt som innehåller bilden. Ange `anchor` en punkt för att justera punkten för kartnålsbilden med koordinaten på kartan. Ankarvärdet i Google Maps är relativt i bildens övre vänstra hörn.

```javascript
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    icon: {
        url: 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png',
        anchor: new google.maps.Point(5, 30)
    },
    map: map
});
```

<center>

![Anpassad markör för Google Maps](media/migrate-google-maps-web-app/google-maps-custom-marker.png)</center>

**Efter: Azure Maps med HTML-markörer**

Om du vill anpassa en `string` `HTMLElement` HTML-markör skickar du en `htmlContent` HTML-kod eller till alternativet för markören. Använd `anchor` alternativet för att ange markörens relativa position i förhållande till positionskoordinaten. Tilldela ett av nio definierade `anchor` referenspunkter till alternativet. De definierade punkterna är: "center", "top", "bottom", "left", "right", "top-left", "top-right", "bottom-left", "bottom-right". Innehållet är förankrat i html-innehållets nedre mittpunkt som standard. Om du vill göra det enklare att `anchor` migrera kod från Google Maps ställer `pixelOffset` du in "överst till vänster" och använder sedan alternativet med samma förskjutning som används i Google Maps. Förskjutningarna i Azure Maps flyttas i motsatt riktning mot förskjutningarna i Google Maps. Så multiplicera förskjutningar med minus en.

> [!TIP]
> Lägg `pointer-events:none` till som format på html-innehållet för att inaktivera standardrutförandet i Microsoft Edge, som visar en oönskad ikon.

```javascript
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

<center>

![Anpassad HTML-markör för Azure Maps](media/migrate-google-maps-web-app/azure-maps-custom-html-marker.png)</center>

**Efter: Azure Maps med hjälp av ett symbollager**

Symbollager i Azure Maps stöder även anpassade avbildningar. Läs först in bilden till kartresurserna och tilldela den med ett unikt ID. Referera till bilden i symbollagret. Använd `offset` alternativet för att justera bilden till rätt punkt på kartan. Använd `anchor` alternativet för att ange symbolens relativa position i förhållande till positionskoordinaterna. Använd en av de nio definierade referenspunkterna. Dessa punkter är: "center", "topp", "botten", "vänster", "höger", "övre vänstra", "övre högra", "nederkant-vänster", "nederkant-höger". Innehållet är förankrat i html-innehållets nedre mittpunkt som standard. Om du vill göra det enklare att `anchor` migrera kod från Google Maps ställer `offset` du in "överst till vänster" och använder sedan alternativet med samma förskjutning som används i Google Maps. Förskjutningarna i Azure Maps flyttas i motsatt riktning mot förskjutningarna i Google Maps. Så multiplicera förskjutningar med minus en.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Load the custom image icon into the map resources.
                map.imageSprite.add('my-yellow-pin', 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png').then(function () {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Create a point and add it to the data source.
                    datasource.add(new atlas.data.Point([-0.2, 51.5]));

                    //Add a layer for rendering point data as symbols.
                    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            //Set the image option to the id of the custom icon that was loaded into the map resources.
                            image: 'my-yellow-pin',
                            anchor: 'top-left',
                            offset: [-5, -30]
                        }
                    }));
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Symbollagret för anpassad ikon i Azure Maps](media/migrate-google-maps-web-app/azure-maps-custom-icon-symbol-layer.png)</center>

> [!TIP]
> Om du vill återge avancerade anpassade punkter använder du flera renderingslager tillsammans. Anta till exempel att du vill ha flera kartnålar som har samma ikon på olika färgade cirklar. I stället för att skapa en massa bilder för varje färgöverlägg lägger du till ett symbollager ovanpå ett bubbellager. Låt kartnålarna referera till samma datakälla. Detta tillvägagångssätt kommer att vara effektivare än att skapa och underhålla en massa olika bilder.

**Ytterligare resurser:**

- [Skapa en datakälla](create-data-source-web-sdk.md)
- [Lägga till ett symbollager](map-add-pin.md)
- [Lägga till HTML-markörer](map-add-custom-html.md)
- [Använda datadrivna formatuttryck](data-driven-style-expressions-web-sdk.md)
- [Alternativ för symbollager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Alternativet Text för symbollager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [Klassen HTML-markör](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [Alternativ för HTML-markör](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-polyline"></a>Lägga till en polylinje

Använd polylinjer för att representera en linje eller bana på kartan. Låt oss skapa en streckad polylinje på kartan.

**Före: Google Maps**

Klassen Polyline accepterar en uppsättning alternativ. Skicka en matris med `path` koordinater i alternativet för polylinjen.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Define a symbol using SVG path notation, with an opacity of 1.
var lineSymbol = {
    path: 'M 0,-1 0,1',
    strokeOpacity: 1,
    scale: 4
};

//Create the polyline.
var line = new google.maps.Polyline({
    path: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1)
    ],
    strokeColor: 'red',
    strokeOpacity: 0,
    strokeWeight: 4,
    icons: [{
        icon: lineSymbol,
        offset: '0',
        repeat: '20px'
    }]
});

//Add the polyline to the map.
line.setMap(map);
```

<center>

![Google Maps polyline](media/migrate-google-maps-web-app/google-maps-polyline.png)</center>

**Efter: Azure Maps**

Polyliner `LineString` anropas `MultiLineString` eller objekt. Dessa objekt kan läggas till i en datakälla och återges med hjälp av ett linjelager. Lägg `LineString` till i en datakälla och `LineLayer` lägg sedan till datakällan i en för att återge den.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a line and add it to the data source.
datasource.add(new atlas.data.LineString([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5]
]));

//Add a layer for rendering line data.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 4,
    strokeDashArray: [3, 3]
}));
```

<center>

![Polylinje för Azure Maps](media/migrate-google-maps-web-app/azure-maps-polyline.png)</center>

**Ytterligare resurser:**

- [Lägga till linjer på kartan](map-add-line-layer.md)
- [Alternativ för linjelager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Använda datadrivna formatuttryck](data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>Lägga till en polygon

Azure Maps och Google Maps ger liknande stöd för polygoner. Polygoner används för att representera ett område på kartan. Följande exempel visar hur du skapar en polygon som bildar en triangel baserat på kartans mittkoordinat.

**Före: Google Maps**

Klassen Polygon accepterar en uppsättning alternativ. Skicka en matris med `paths` koordinater till polygonens alternativ.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create a polygon.
var polygon = new google.maps.Polygon({
    paths: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1),
        center
    ],
    strokeColor: 'red',
    strokeWeight: 2,
    fillColor: 'rgba(0, 255, 0, 0.5)'
});

//Add the polygon to the map
polygon.setMap(map);
```

<center>

![Google Maps polygon](media/migrate-google-maps-web-app/google-maps-polygon.png)</center>

**Efter: Azure Maps**

Lägg `Polygon` till `MultiPolygon` ett eller ett objekt i en datakälla. Återge objektet på kartan med hjälp av lager. Återge området för en polygon med hjälp av ett polygonskikt. Och gör konturerna av en polygon med hjälp av ett linjelager.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a polygon and add it to the data source.
datasource.add(new atlas.data.Polygon([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5],
    center
]));

//Add a polygon layer for rendering the polygon area.
map.layers.add(new atlas.layer.PolygonLayer(datasource, null, {
    fillColor: 'rgba(0, 255, 0, 0.5)'
}));

//Add a line layer for rendering the polygon outline.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 2
}));
```

<center>

![Azure Maps polygon](media/migrate-google-maps-web-app/azure-maps-polygon.png)</center>

**Ytterligare resurser:**

- [Lägga till en polygon på kartan](map-add-shape.md)
- [Lägga till en cirkel på kartan](map-add-shape.md#add-a-circle-to-the-map)
- [Alternativ för polygonlager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)
- [Alternativ för linjelager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Använda datadrivna formatuttryck](data-driven-style-expressions-web-sdk.md)

### <a name="display-an-info-window"></a>Visa ett informationsfönster

Ytterligare information för en entitet kan `google.maps.InfoWindow` visas på kartan som en klass i Google Maps. I Azure Maps kan den här `atlas.Popup` funktionen uppnås med hjälp av klassen. I nästa exempel lägger du till en markör på kartan. När du klickar på markören visas ett informationsfönster eller en popup-markering.

**Före: Google Maps**

Instansiera ett informationsfönster med `google.maps.InfoWindow` konstruktorn.

```javascript
//Add a marker in which to display an infowindow for.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(47.6, -122.33),
    map: map
});

//Create an infowindow.
var infowindow = new google.maps.InfoWindow({
    content: '<div style="padding:5px"><b>Hello World!</b></div>'
});

//Add a click event to the marker to open the infowindow.
marker.addListener('click', function () {
    infowindow.open(map, marker);
});
```

<center>

![Google Maps popup](media/migrate-google-maps-web-app/google-maps-popup.png)</center>

**Efter: Azure Maps**

Låt oss använda popup för att visa ytterligare information om platsen. Skicka en `string` `HTMLElement` HTML eller `content` objekt till alternativet för popup. Om du vill kan popup-fönster visas oberoende av vilken form som helst. Popup-fönster kräver `position` därför att ett värde anges. Ange `position` värdet. Om du vill visa `open` en popup `map` anropar du metoden och skickar den i vilken popup-fönstret ska visas på.

```javascript
//Add a marker to the map in which to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:5px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```

<center>

![Popup-popup-popup-fönster i Azure Maps](media/migrate-google-maps-web-app/azure-maps-popup.png)</center>

> [!NOTE]
> Du kan göra samma sak med en symbol, bubbla, linje eller polygon lager genom att skicka det valda lagret till kartor händelsekod i stället för en markör.

**Ytterligare resurser:**

- [Lägg till ett popup-fönster](map-add-popup.md)
- [Popup med medieinnehåll](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
- [Popup-fönster på former](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
- [Återanvända popup med flera pins](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
- [Klassen Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
- [Popup-alternativ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

### <a name="import-a-geojson-file"></a>Importera en GeoJSON-fil

Google Maps stöder inläsning och dynamiskt `google.maps.Data` styling GeoJSON data via klassen. Funktionerna i den här klassen anpassar sig mycket mer till den datadrivna formateringen i Azure Maps. Men det är en viktig skillnad. Med Google Maps anger du en motringningsfunktion. Affärslogiken för att formatera varje funktion som bearbetas individuellt i gränssnittstråden. Men i Azure Maps har lager stöd för att ange datadrivna uttryck som stylingalternativ. Dessa uttryck bearbetas vid renderingstillfället på en separat tråd. Azure Maps-metoden förbättrar renderingsprestanda. Den här fördelen märks när större datauppsättningar måste återges snabbt.

Följande exempel laddar ett GeoJSON-flöde av alla jordbävningar under de senaste sju dagarna från USGS. Jordbävningar data återges som skalade cirklar på kartan. Färgen och skalan för varje cirkel baseras på omfattningen av `"mag"` varje jordbävning, som lagras i egenskapen för varje funktion i datauppsättningen. Om magnituden är större än eller lika med fem, kommer cirkeln att vara röd. Om det är större eller lika med tre, men mindre än fem, kommer cirkeln att vara orange. Om det är mindre än tre, kommer cirkeln att vara grön. Radien för varje cirkel kommer att vara exponentiell av magnitud multiplicerat med 0,1.

**Före: Google Maps**

Ange en enskild motringningsfunktion i `map.data.setStyle` metoden. I motringningsfunktionen använder du affärslogik på varje funktion. Ladda GeoJSON-matningen `map.data.loadGeoJson` med metoden.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Define a callback to style each feature.
            map.data.setStyle(function (feature) {

                //Extract the 'mag' property from the feature.
                var mag = parseFloat(feature.getProperty('mag'));

                //Set the color value to 'green' by default.
                var color = 'green';

                //If the mag value is greater than 5, set the color to 'red'.
                if (mag >= 5) {
                    color = 'red';
                }
                //If the mag value is greater than 3, set the color to 'orange'.
                else if (mag >= 3) {
                    color = 'orange';
                }

                return /** @type {google.maps.Data.StyleOptions} */({
                    icon: {
                        path: google.maps.SymbolPath.CIRCLE,

                        //Scale the radius based on an exponential of the 'mag' value.
                        scale: Math.exp(mag) * 0.1,
                        fillColor: color,
                        fillOpacity: 0.75,
                        strokeWeight: 2,
                        strokeColor: 'white'
                    }
                });
            });

            //Load the data feed.
            map.data.loadGeoJson(earthquakeFeed);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Google Maps GeoJSON](media/migrate-google-maps-web-app/google-maps-geojson.png)</center>

**Efter: Azure Maps**

GeoJSON är basdatatypen i Azure Maps. Importera den till en `datasource.importFromUrl` datakälla med hjälp av metoden. Använd ett bubbellager. Bubbellagret tillhandahåller funktioner för renderingskalade cirklar, baserat på egenskaperna för funktionerna i en datakälla. I stället för att ha en motringningsfunktion konverteras affärslogiken till ett uttryck och skickas till formatalternativen. Uttryck definierar hur affärslogiken fungerar. Uttryck kan skickas till en annan tråd och utvärderas mot funktionsdata. Flera datakällor och lager kan läggas till i Azure Maps, var och en med olika affärslogik. Med den här funktionen kan flera datauppsättningar återges på kartan på olika sätt.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as scaled circles.
                map.layers.add(new atlas.layer.BubbleLayer(datasource, null, {
                    //Make the circles semi-transparent.
                    opacity: 0.75,

                    color: [
                        'case',

                        //If the mag value is greater than 5, return 'red'.
                        ['>=', ['get', 'mag'], 5],
                        'red',

                        //If the mag value is greater than 3, return 'orange'.
                        ['>=', ['get', 'mag'], 3],
                        'orange',

                        //Return 'green' as a fallback.
                        'green'
                    ],

                    //Scale the radius based on an exponential of the 'mag' value.
                    radius: ['*', ['^', ['e'], ['get', 'mag']], 0.1]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps GeoJSON](media/migrate-google-maps-web-app/azure-maps-geojson.png)</center>

**Ytterligare resurser:**

- [Lägga till ett symbollager](map-add-pin.md)
- [Lägga till ett bubbellager](map-add-bubble-layer.md)
- [Klusterpunktdata](clustering-point-data-web-sdk.md)
- [Använda datadrivna formatuttryck](data-driven-style-expressions-web-sdk.md)

### <a name="marker-clustering"></a>Markörkluster

När du visualiserar många datapunkter på kartan kan punkter överlappa varandra. Överlappande gör att kartan ser rörig ut och kartan blir svår att läsa och använda. Klusterpunktsdata är processen att kombinera datapunkter som är nära varandra och representera dem på kartan som en enda klustrade datapunkt. När användaren zoomar in på kartan delas klustren isär i sina enskilda datapunkter. Klusterdata pekar för att förbättra användarupplevelsen och kartprestanda.

I följande exempel läser koden in en GeoJSON-feed av jordbävningsdata från den senaste veckan och lägger till den på kartan. Kluster återges som skalade och färgade cirklar. Cirklarnas skala och färg beror på hur många punkter de innehåller.

> [!NOTE]
> Google Maps och Azure Maps använder lite olika klusteralgoritmer. Som sådan varierar ibland punktfördelningen i klustren.

**Före: Google Maps**

Använd MarkerCluster-biblioteket för att klustermarkörer. Klusterikoner är begränsade till bilder, som har siffrorna en till fem som namn. De finns i samma katalog.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Download the GeoJSON data.
            fetch(earthquakeFeed)
                .then(function (response) {
                    return response.json();
                }).then(function (data) {
                    //Loop through the GeoJSON data and create a marker for each data point.
                    var markers = [];

                    for (var i = 0; i < data.features.length; i++) {

                        markers.push(new google.maps.Marker({
                            position: new google.maps.LatLng(data.features[i].geometry.coordinates[1], data.features[i].geometry.coordinates[0])
                        }));
                    }

                    //Create a marker clusterer instance and tell it where to find the cluster icons.
                    var markerCluster = new MarkerClusterer(map, markers,
                        { imagePath: 'https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/m' });
                });
        }
    </script>

    <!-- Load the marker cluster library. -->
    <script src="https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/markerclusterer.js"></script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Google Maps klustring](media/migrate-google-maps-web-app/google-maps-clustering.png)</center>

**Efter: Azure Maps**

Lägg till och hantera data i en datakälla. Anslut datakällor och datalager och återge sedan data. `DataSource` Klassen i Azure Maps innehåller flera klusteralternativ.

- `cluster`– Talar om för datakällan att klusterpunktsdata.
- `clusterRadius`- Radien i pixlar till kluster pekar tillsammans.
- `clusterMaxZoom`- Den maximala zoomnivån där klustring sker. Om du zoomar in mer än den här nivån återges alla punkter som symboler.
- `clusterProperties`- Definierar anpassade egenskaper som beräknas med hjälp av uttryck mot alla punkter i varje kluster och läggs till egenskaperna för varje klusterpunkt.

När klustring är aktiverat skickar datakällan klustrade och oknuddade datapunkter till lager för rendering. Datakällan kan samla hundratusentals datapunkter. En klustrad datapunkt har följande egenskaper:

| Egenskapsnamn             | Typ    | Beskrivning   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | Anger om funktionen representerar ett kluster. |
| `cluster_id`              | sträng  | Ett unikt ID för klustret som `getClusterExpansionZoom`kan `getClusterChildren`användas `getClusterLeaves` med DataSource , och metoder. |
| `point_count`             | nummer  | Antalet punkter som klustret innehåller.  |
| `point_count_abbreviated` | sträng  | En sträng som `point_count` förkortar värdet om det är långt. (till exempel blir 4 000 4K)  |

Klassen `DataSource` har följande hjälpfunktion för att komma åt ytterligare `cluster_id`information om ett kluster med hjälp av .

| Metod | Returtyp | Beskrivning |
|--------|-------------|-------------|
| `getClusterChildren(clusterId: number)` | Promise&lt;&lt;&lt;Array-funktionsgeometri, valfri&gt; \| form&gt;&gt; | Hämtar underordnade i det angivna klustret på nästa zoomnivå. Dessa barn kan vara en kombination av former och subclusters. Subclusters kommer att vara funktioner med egenskaper som matchar ClusteredProperties. |
| `getClusterExpansionZoom(clusterId: number)` | Löftesnummer&lt;&gt; | Beräknar en zoomnivå där klustret börjar expandera eller bryta isär. |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | Promise&lt;&lt;&lt;Array-funktionsgeometri, valfri&gt; \| form&gt;&gt; | Hämtar alla punkter i ett kluster. Ange `limit` att returnera en delmängd av punkterna `offset` och använd till-sidan genom punkterna. |

När du återger klustrade data på kartan är det ofta bäst att använda två eller flera lager. I följande exempel används tre lager. Ett bubbellager för ritning skalade färgade cirklar baserat på storleken på kluster. Ett symbollager för att återge klusterstorleken som text. Och den använder ett andra symbollager för att återge de oknämmade punkterna. Det finns många andra sätt att återge klustrade data. Mer information finns i dokumentationen [för klusterpunktsdata.](clustering-point-data-web-sdk.md)

Importera GeoJSON-data `importDataFromUrl` direkt med `DataSource` hjälp av funktionen på klassen, inuti Azure Maps-kartan.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource(null, {
                    //Tell the data source to cluster point data.
                    cluster: true
                });
                map.sources.add(datasource);

                //Create layers for rendering clusters, their counts and unclustered points and add the layers to the map.
                map.layers.add([
                    //Create a bubble layer for rendering clustered data points.
                    new atlas.layer.BubbleLayer(datasource, null, {
                        //Scale the size of the clustered bubble based on the number of points inthe cluster.
                        radius: [
                            'step',
                            ['get', 'point_count'],
                            20,         //Default of 20 pixel radius.
                            100, 30,    //If point_count >= 100, radius is 30 pixels.
                            750, 40     //If point_count >= 750, radius is 40 pixels.
                        ],

                        //Change the color of the cluster based on the value on the point_cluster property of the cluster.
                        color: [
                            'step',
                            ['get', 'point_count'],
                            'lime',            //Default to lime green. 
                            100, 'yellow',     //If the point_count >= 100, color is yellow.
                            750, 'red'         //If the point_count >= 750, color is red.
                        ],
                        strokeWidth: 0,
                        filter: ['has', 'point_count'] //Only rendered data points which have a point_count property, which clusters do.
                    }),

                    //Create a symbol layer to render the count of locations in a cluster.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            image: 'none' //Hide the icon image.
                        },
                        textOptions: {
                            textField: ['get', 'point_count_abbreviated'],
                            offset: [0, 0.4]
                        }
                    }),

                    //Create a layer to render the individual locations.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
                    })
                ]);

                //Retrieve a GeoJSON data set and add it to the data source. 
                datasource.importDataFromUrl(earthquakeFeed);
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps-kluster](media/migrate-google-maps-web-app/azure-maps-clustering.png)</center>

**Ytterligare resurser:**

- [Lägga till ett symbollager](map-add-pin.md)
- [Lägga till ett bubbellager](map-add-bubble-layer.md)
- [Klusterpunktdata](clustering-point-data-web-sdk.md)
- [Använda datadrivna formatuttryck](data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>Lägg till en värmekarta

Värmekartor, även kallade punktdensitetskartor, är en typ av datavisualisering. De används för att representera datatätheten med hjälp av en rad färger. Och de används ofta för att visa data "hotspots" på en karta. Värmekartor är ett bra sätt att återge stora punktdatauppsättningar.

Följande exempel laddar ett GeoJSON-flöde av alla jordbävningar under den senaste månaden, från USGS, och det gör dem som en viktad värmekarta. Egenskapen `"mag"` används som vikt.

**Före: Google Maps**

Om du vill skapa en värmekarta läser `&libraries=visualization` du in biblioteket "visualisering" genom att lägga till URL:en för API-skriptet. Värmekartlagret i Google Maps stöder inte GeoJSON-data direkt. Först ladda ner data och konvertera den till en rad viktade datapunkter:

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {

            var map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2,
                mapTypeId: 'satellite'
            });

            //Download the GeoJSON data.
            fetch(url).then(function (response) {
                return response.json();
            }).then(function (res) {
                var points = getDataPoints(res);

                var heatmap = new google.maps.visualization.HeatmapLayer({
                    data: points
                });
                heatmap.setMap(map);
            });
        }

        function getDataPoints(geojson, weightProp) {
            var points = [];

            for (var i = 0; i < geojson.features.length; i++) {
                var f = geojson.features[i];

                if (f.geometry.type === 'Point') {
                    points.push({
                        location: new google.maps.LatLng(f.geometry.coordinates[1], f.geometry.coordinates[0]),
                        weight: f.properties[weightProp]
                    });
                }
            }

            return points;
        } 
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]&libraries=visualization" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Google Maps värme karta](media/migrate-google-maps-web-app/google-maps-heatmap.png)</center>

**Efter: Azure Maps**

Ladda GeoJSON-data i en datakälla och anslut datakällan till ett värmekartlager. Egenskapen som ska användas för vikten kan `weight` skickas till alternativet med hjälp av ett uttryck. Importera GeoJSON-data direkt till `importDataFromUrl` Azure `DataSource` Maps med hjälp av funktionen på klassen.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,
                style: 'satellite',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as a heat map.
                map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
                    weight: ['get', 'mag'],
                    intensity: 0.005,
                    opacity: 0.65,
                    radius: 10
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps värmekarta](media/migrate-google-maps-web-app/azure-maps-heatmap.png)</center>

**Ytterligare resurser:**

- [Lägga till ett heatmapskikt](map-add-heat-map-layer.md)
- [Värmekarta lager klass](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.heatmaplayer?view=azure-iot-typescript-latest)
- [Alternativ för värmekartlager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)
- [Använda datadrivna formatuttryck](data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>Överlagra ett panellager

Panellager i Azure Maps kallas bildöverlägg i Google Maps. Med panellager kan du överlagra stora bilder som har delats upp i mindre kaklade bilder, som ligger i linje med kartkrätande systemet. Den här metoden är en vanlig används för att överlagra stora bilder eller stora datauppsättningar.

Följande exempel överlägg ett väderradarkakellager från Iowa Environmental Mesonet från Iowa State University.

**Före: Google Maps**

I Google Maps kan panellager skapas med hjälp av `google.maps.ImageMapType` klassen.

```javascript
map.overlayMapTypes.insertAt(0, new google.maps.ImageMapType({
    getTileUrl: function (coord, zoom) {
        return "https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/" + zoom + "/" + coord.x + "/" + coord.y;
    },
    tileSize: new google.maps.Size(256, 256),
    opacity: 0.8
}));
```

<center>

![Panellager för Google Maps](media/migrate-google-maps-web-app/google-maps-tile-layer.png)</center>

**Efter: Azure Maps**

Lägg till ett panellager på kartan på samma sätt som alla andra lager. Använd en formaterad URL som har i x, y, zooma platshållare; `{x}`, `{y}` `{z}` för att tala om för lagret var du ska komma åt panelerna. Azure Maps-panellager `{quadkey}` `{bbox-epsg-3857}`stöder `{subdomain}` också , och platshållare.

> [!TIP]
> I Azure Maps kan lager enkelt återges under andra lager, inklusive baskartlager. Ofta är det önskvärt att återge panellager under kartetiketterna så att de är lätta att läsa. Metoden `map.layers.add` tar in en andra parameter som är id för det lager där du kan infoga det nya lagret nedan. Om du vill infoga ett panellager under kartetiketterna använder du den här koden:`map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

<center>

![Azure Maps-panellager](media/migrate-google-maps-web-app/azure-maps-tile-layer.png)</center>

> [!TIP]
> Panelbegäranden kan fångas `transformRequest` med hjälp av kartans alternativ. På så sätt kan du ändra eller lägga till rubriker i begäran om så önskas.

**Ytterligare resurser:**

- [Lägga till panelskikt](map-add-tile-layer.md)
- [Dela lagerklass](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)
- [Alternativ för lager med panel](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

### <a name="show-traffic"></a>Visa trafik

Trafikdata kan överlagras både På Azure- och Google Maps.

**Före: Google Maps**

Överlagra trafikdata på kartan med hjälp av trafiklagret.

```javascript
var trafficLayer = new google.maps.TrafficLayer();
trafficLayer.setMap(map);
```

<center>

![Google Maps trafik](media/migrate-google-maps-web-app/google-maps-traffic.png)</center>

**Efter: Azure Maps**

Azure Maps innehåller flera olika alternativ för att visa trafik. Visa trafikincidenter, till exempel avstängda vägar och olyckor som ikoner på kartan. Överlagra trafikflödet och färgkodade vägar på kartan. Färgerna kan ändras baserat på den bokförda hastighetsgränsen, i förhållande till den normala förväntade fördröjningen eller absolut fördröjning. Incidentdata i Azure Maps uppdateras varje minut och flödesdata uppdateras varannan minut.

Tilldela önskade värden `setTraffic` för alternativ.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

<center>

![Azure Maps trafik](media/migrate-google-maps-web-app/azure-maps-traffic.png)</center>

Om du klickar på en av trafikikonerna i Azure Maps visas ytterligare information i ett popup-fönster.

<center>

![Azure Maps trafikincident](media/migrate-google-maps-web-app/azure-maps-traffic-incident.png)</center>

**Ytterligare resurser:**

- [Visa trafik på kartan](map-show-traffic.md)
- [Alternativ för överlagring av trafik](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)

### <a name="add-a-ground-overlay"></a>Lägg till ett jordöverlägg

Både Azure och Google Maps stöder överlagring georeferenced bilder på kartan. Georeferenced bilder flytta och skala när du panorera och zooma kartan. I Google Maps kallas georefererade bilder marköverlägg medan de i Azure Maps kallas bildlager. De är bra för att bygga planritningar, överlagra gamla kartor eller bilder från en drönare.

**Före: Google Maps**

Ange URL:en till den bild som du vill lägga över och en markeringsram för att binda bilden på kartan. Det här exemplet överlagrar en kartbild av [Newark New Jersey från 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) på kartan.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.740, -74.18),
                zoom: 12
            });

            var imageBounds = {
                north: 40.773941,
                south: 40.712216,
                east: -74.12544,
                west: -74.22655
            };

            historicalOverlay = new google.maps.GroundOverlay(
                'https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg',
                imageBounds);
            historicalOverlay.setMap(map);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

Om du kör den här koden i en webbläsare visas en karta som ser ut som följande bild:

<center>

![Google Maps bildöverlägg](media/migrate-google-maps-web-app/google-maps-image-overlay.png)</center>

**Efter: Azure Maps**

Använd `atlas.layer.ImageLayer` klassen för att överlagra georeferenced bilder. Den här klassen kräver en URL till en bild och en uppsättning koordinater för bildens fyra hörn. Avbildningen måste vara värd antingen på samma domän eller ha COR aktiverat.

> [!TIP]
> Om du bara har information om norr, söder, öst, väst och rotation, och du inte [`atlas.layer.ImageLayer.getCoordinatesFromEdges`](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest#getcoordinatesfromedges-number--number--number--number--number-) har koordinater för varje hörn av bilden, kan du använda den statiska metoden.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-74.18, 40.740],
                zoom: 12,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an image layer and add it to the map.
                map.layers.add(new atlas.layer.ImageLayer({
                    url: 'newark_nj_1922.jpg',
                    coordinates: [
                        [-74.22655, 40.773941], //Top Left Corner
                        [-74.12544, 40.773941], //Top Right Corner
                        [-74.12544, 40.712216], //Bottom Right Corner
                        [-74.22655, 40.712216]  //Bottom Left Corner
                    ]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Avbildningsöverlagring i Azure Maps](media/migrate-google-maps-web-app/azure-maps-image-overlay.png)</center>

**Ytterligare resurser:**

- [Lägga till en bild som överlägg](map-add-image-layer.md)
- [Klassen Bildlager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

## <a name="add-kml-to-the-map"></a>Lägg till KML på kartan

Både Azure och Google Maps kan importera och återge KML-, KMZ- och GeoRSS-data på kartan. Azure Maps stöder också GPX, GML, spatial CSV-filer, GeoJSON, Välkänd text (WKT), Web Mapping Services (WMS), Web Mapping Tile Services (WMTS) och Web Feature Services (WFS). Azure Maps läser filerna lokalt i minnet och kan i de flesta fall hantera mycket större KML-filer. 

**Före: Google Maps**


```javascript
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(0, 0),
                zoom: 1
            });

             var layer = new google.maps.KmlLayer({
              url: 'https://googlearchive.github.io/js-v2-samples/ggeoxml/cta.kml',
              map: map
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

Om du kör den här koden i en webbläsare visas en karta som ser ut som följande bild:

<center>

![Google Maps bildöverlägg](media/migrate-google-maps-web-app/google-maps-kml.png)</center>

**Efter: Azure Maps**

I Azure Maps är GeoJSON det viktigaste dataformatet som används i webben SDK, ytterligare rumsliga dataformat kan enkelt integreras i den [rumsliga IO-modulen](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/). Denna modul har funktioner för både läsning och skrivning av rumsliga data och innehåller även ett enkelt datalager som enkelt kan återge data från något av dessa rumsliga dataformat. Om du vill läsa data i en spatial datafil skickar du bara in `atlas.io.read` en URL eller rådata som sträng eller blob till funktionen. Detta returnerar alla tolkade data från filen som sedan kan läggas till på kartan. KML är lite mer komplext än de flesta rumsliga dataformat eftersom det innehåller mycket mer styling information. Klassen `SpatialDataLayer` stöder rendering flesta av dessa stilar, men ikoner bilder måste laddas i kartan innan du läser in funktionen data, och mark överlägg måste läggas till som lager på kartan separat. När data läses in via en URL bör den finnas på en REK-aktiverad slutpunkt, eller så ska en proxytjänst skickas in som ett alternativ till läsfunktionen. 

```javascript
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <!-- Add reference to the Azure Maps Spatial IO module. -->
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

    <script type='text/javascript'>
        var map, datasource, layer;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
            
                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Add a simple data layer for rendering the data.
                layer = new atlas.layer.SimpleDataLayer(datasource);
                map.layers.add(layer);

                //Read a KML file from a URL or pass in a raw KML string.
                atlas.io.read('https://googlearchive.github.io/js-v2-samples/ggeoxml/cta.kml').then(async r => {
                    if (r) {

                        //Check to see if there are any icons in the data set that need to be loaded into the map resources.
                        if (r.icons) {
                            //For each icon image, create a promise to add it to the map, then run the promises in parrallel.
                            var imagePromises = [];

                            //The keys are the names of each icon image.
                            var keys = Object.keys(r.icons);

                            if (keys.length !== 0) {
                                keys.forEach(function (key) {
                                    imagePromises.push(map.imageSprite.add(key, r.icons[key]));
                                });

                                await Promise.all(imagePromises);
                            }
                        }

                        //Load all features.
                        if (r.features && r.features.length > 0) {
                            datasource.add(r.features);
                        }

                        //Load all ground overlays.
                        if (r.groundOverlays && r.groundOverlays.length > 0) {
                            map.layers.add(r.groundOverlays);
                        }

                        //If bounding box information is known for data, set the map view to it.
                        if (r.bbox) {
                            map.setCamera({ bounds: r.bbox, padding: 50 });
                        }
                    }
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Avbildningsöverlagring i Azure Maps](media/migrate-google-maps-web-app/azure-maps-kml.png)</center>

**Ytterligare resurser:**

- [funktionen atlas.io.read](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io?view=azure-maps-typescript-latest#read-string---arraybuffer---blob--spatialdatareadoptions-)
- [SimpleDataLayer (SimpleDataLayer)](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)
- [SimpleDataLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

## <a name="additional-code-samples"></a>Ytterligare kodexempel

Följande är några ytterligare kodexempel relaterade till Google Maps-migrering:

- [Ritningsverktyg](map-add-drawing-toolbar.md)
- [Begränsa mappning till panorering av två fingrar](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Map%20to%20Two%20Finger%20Panning)
- [Begränsa zoomning av rullningshjul](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Scroll%20Wheel%20Zoom)
- [Skapa en fullskärmskontroll](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Create%20a%20Fullscreen%20Control)

**Tjänster:**

- [Använda modulen Azure Maps-tjänster](how-to-use-services-module.md)
- [Sök efter orienteringspunkter](map-search-location.md)
- [Hämta information från en koordinat (omvänd geokod)](map-get-information-from-coordinate.md)
- [Visa anvisningar från A till B](map-route.md)
- [Sök efter automatisk uggst med JQuery UI](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

## <a name="google-maps-v3-to-azure-maps-web-sdk-class-mapping"></a>Google Maps V3 till Azure Maps Web SDK-klassmappning

Följande bilaga innehåller en korsreferens för de vanliga klasserna i Google Maps V3 och motsvarande Azure Maps Web SDK.

### <a name="core-classes"></a>Kärnklasser

| Google Maps   | Azure Maps  |
|---------------|-------------|
| `google.maps.Map` | [Atlas. Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindow` | [Atlas. Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindowOptions` | [Atlas. PopupOptions (olika)](https://docs.microsoft.com/) |
| `google.maps.LatLng`  | [atlas.data.Position](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest)  |
| `google.maps.LatLngBounds` | [atlas.data.BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) |
| `google.maps.MapOptions`  | [Atlas. CameraOptions (KameraAlternativ)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions?view=azure-iot-typescript-latest)<br/>[Atlas. CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions?view=azure-iot-typescript-latest)<br/>[Atlas. ServiceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions?view=azure-iot-typescript-latest)<br/>[Atlas. StyleOptions (olika)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions?view=azure-iot-typescript-latest)<br/>[Atlas. AnvändareInteractionOptioner](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions?view=azure-iot-typescript-latest) |
| `google.maps.Point`  | [Atlas. Pixel](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.pixel?view=azure-iot-typescript-latest)   |

## <a name="overlay-classes"></a>Överlägg klasser

| Google Maps  | Azure Maps  |
|--------------|-------------|
| `google.maps.Marker` | [Atlas. HtmlMarker (på samma sätt som)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)<br/>[atlas.data.Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)  |
| `google.maps.MarkerOptions`  | [Atlas. HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)<br/>[atlas.layer.SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)<br/>[Atlas. SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)<br/>[Atlas. IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)<br/>[Atlas. TextAlternativ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)<br/>[atlas.layer.BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)<br/>[Atlas. BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.Polygon`  | [atlas.data.Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)               |
| `google.maps.PolygonOptions` |[atlas.layer.PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)<br/> [Atlas. PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)<br/> [atlas.layer.LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)<br/> [Atlas. LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)|
| `google.maps.Polyline` | [atlas.data.LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest)         |
| `google.maps.PolylineOptions` | [atlas.layer.LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-maps-typescript-latest)<br/>[Atlas. LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-maps-typescript-latest) |
| `google.maps.Circle`  | Se [Lägga till en cirkel på kartan](map-add-shape.md#add-a-circle-to-the-map)                                     |
| `google.maps.ImageMapType`  | [Atlas. KakelLager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)         |
| `google.maps.ImageMapTypeOptions` | [Atlas. TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.GroundOverlay`  | [atlas.layer.ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)<br/>[Atlas. ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest) |

## <a name="service-classes"></a>Serviceklasser

Azure Maps Web SDK innehåller en tjänstmodul som kan läsas in separat. Den här modulen avslutar Azure Maps REST-tjänsterna med ett webb-API och kan användas i JavaScript-, TypeScript- och Node.js-program.

| Google Maps | Azure Maps  |
|-------------|-------------|
| `google.maps.Geocoder` | [atlas.service.SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |
| `google.maps.GeocoderRequest`  | [Atlas. SökAdressOptioner](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SökAdressRevrseOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreverseoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SökAdressReverseCrossStreetOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreversecrossstreetoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SökadressStruktureradeOptioner](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressstructuredoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SökAlongRouteOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchalongrouteoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SökFuzzyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchfuzzyoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SökInsideGeometryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchinsidegeometryoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SökNearbyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchnearbyoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SökPOIOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoioptions?view=azure-iot-typescript-latest)<br/>[Atlas. SökPOICategoryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoicategoryoptions?view=azure-iot-typescript-latest) |
| `google.maps.DirectionsService`  | [atlas.service.RouteUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)  |
| `google.maps.DirectionsRequest`  | [Atlas. BeräknaRouteDirectionsOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.calculateroutedirectionsoptions?view=azure-iot-typescript-latest) |
| `google.maps.places.PlacesService` | [atlas.service.SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |

## <a name="libraries"></a>Bibliotek

Bibliotek lägger till ytterligare funktioner på kartan. Många av dessa bibliotek finns i kärnan SDK för Azure Maps. Här är några likvärdiga klasser att använda i stället för dessa Google Maps-bibliotek

| Google Maps           | Azure Maps   |
|-----------------------|--------------|
| Ritningsbibliotek       | [Modul för ritningsverktyg](set-drawing-options.md) |
| Biblioteket Geometri      | [atlas.matematik](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)   |
| Visualiseringsbibliotek | [Värme kartskikt](map-add-heat-map-layer.md) |

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Maps Web SDK.

> [!div class="nextstepaction"]
> [Så här använder du kartkontrollen](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Så här använder du tjänstemodulen](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Så här använder du modulen ritverktyg](set-drawing-options.md)

> [!div class="nextstepaction"]
> [Kodexempel](https://docs.microsoft.com/samples/browse/?products=azure-maps)

