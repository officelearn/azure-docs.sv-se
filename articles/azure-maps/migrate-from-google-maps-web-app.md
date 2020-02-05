---
title: 'Självstudie: Migrera en webbapp från Google Maps | Microsoft Azure Maps'
description: Så här migrerar du en webbapp från Google Maps till Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: d9b873a058410219bc55abc4f575823b519a646b
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989121"
---
# <a name="migrate-a-web-app-from-google-maps"></a>Migrera en webbapp från Google Maps

De flesta Web Apps, som använder Google Maps, använder Google Maps v3 JavaScript SDK. Azure Maps Web SDK är lämplig Azure-baserad SDK för att migrera till. Med Azure Maps Web SDK kan du anpassa interaktiva kartor med ditt eget innehåll och bilder. Du kan köra din app på både webb-och mobil program. Den här kontrollen använder WebGL, så att du kan rendera stora datauppsättningar med höga prestanda. Utveckla med det här SDK: t med Java Script eller TypeScript.

Om du migrerar ett befintligt webb program bör du kontrol lera om det använder ett kart kontroll bibliotek med öppen källkod. Exempel på kart kontroll bibliotek med öppen källkod är: cesium, häfte och openlager. Om det är och du inte vill använda Azure Maps Web SDK, är ett annat alternativ för att migrera ditt program att fortsätta använda kart kontrollen med öppen källkod och ansluta den till Azure Maps panels tjänster ([väg paneler](https://docs.microsoft.com/rest/api/maps/render/getmaptile) \| [satellit paneler](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)). Följande beskriver hur du använder Azure Maps i några vanliga kart kontroll bibliotek med öppen källkod.

- Cesium – en 3D-kart kontroll för webben. [Kod exempel](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [dokumentation](https://cesiumjs.org/)
- Broschyr – förenklad 2D-kart kontroll för webben. [Kod exempel](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [dokumentation](https://leafletjs.com/)
- OpenLayers – en 2D-kart kontroll för webben som stöder projektioner. [Kod exempel](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [dokumentation](https://openlayers.org/)

## <a name="key-features-support"></a>Viktiga funktioner stöder

I följande tabell visas viktiga API-funktioner i Google Maps v3 JavaScript SDK och API-funktionen som stöds i Azure Maps Web SDK.

| Google Maps-funktion     | Stöd för Azure Maps Web SDK |
|-------------------------|:--------------------------:|
| Markering                 | ✓                          |
| Markör kluster       | ✓                          |
| Polylinjer & polygoner    | ✓                          |
| Data lager             | ✓                          |
| Mark överlägg         | ✓                          |
| Värme kartor               | ✓                          |
| Panel lager             | ✓                          |
| KML-skikt               | ✓                          |
| Ritningsverktyg           | ✓                          |
| Netencoder-tjänst        | ✓                          |
| Vägbeskrivnings tjänst      | ✓                          |
| Distans mat ris tjänst | ✓                          |
| Höjnings tjänst       | Planerad                    |

## <a name="notable-differences-in-the-web-sdks"></a>Viktiga skillnader i webb-SDK: er

Följande är några av de viktigaste skillnaderna mellan Google Maps och Azure Maps webb-SDK: er, för att vara medveten om följande:

- Förutom att tillhandahålla en värdbaserad slut punkt för åtkomst till Azure Maps Web SDK är ett NPM-paket också tillgängligt för att bädda in webb-SDK i appar om det är lämpligt. Mer information finns i den här [dokumentationen](how-to-use-map-control.md). Det här paketet innehåller även TypeScript-definitioner.
- När du har skapat en instans av kart klassen i Azure Maps, ska din kod vänta på att Maps-`ready` eller `load` händelse ska aktive ras innan du interagerar med kartan. I den här ordningen ser du till att alla kart resurser har lästs in och är redo att nås.
- Båda plattformarna använder ett liknande överlappande system för bas Maps, men panelerna i Google Maps är 256 pixlar i dimensionen, medan panelerna i Azure Maps är 512 pixlar i dimensionen. För att få samma Map-vy i Azure Maps som Google Maps måste en zoomnivå som används i Google Maps subtraheras av en i Azure Maps.
- Koordinaterna i Google Maps kallas "latitud, longitud" medan Azure Maps använder "longitud, latitud". Azure Maps-formatet justeras med standard `[x, y]` som följs av de flesta GIS-plattformarna.
- Former i Azure Maps Web SDK baseras på det interjson-schemat. Hjälp klasser exponeras genom [ *Atlas. data* område](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data?view=azure-iot-typescript-latest). Det finns även [*atlasen. Shape*](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape) -klass. Den här klassen kan användas för att figursätta inaktuella JSON-objekt och göra dem enkla att uppdatera och underhålla på ett data bindnings sätt.
- Koordinater i Azure Maps definieras som positions objekt. En koordinat anges som en nummer mat ris i formatet `[longitude, latitude]`eller anges med New Atlas. data. position (longitud, latitud).
    > [!TIP]
    > Positions klassen har en statisk hjälp metod för att importera koordinater i formatet "latitud, longitud". Metoden [Atlas. data. position. fromLatLng](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest) kan ofta ersättas med metoden `new google.maps.LatLng` i Google Maps Code.
- I stället för att ange formateringsinformation för varje form som läggs till i kartan Azure Maps separerar format från data. Data lagras i data källor är anslutna till åter givnings lager. Azure Maps koden använder data källor för att rendera data. Den här metoden ger bättre prestanda nytta. Dessutom stöder många lager data drivna format där affärs logik kan läggas till i lager stil alternativ. Detta stöder ändringar av hur enskilda former återges inom ett lager baserat på egenskaper som definierats i formen.

## <a name="web-sdk-side-by-side-examples"></a>Webb-SDK sida vid sida-exempel

I följande samling finns kod exempel för varje plattform som omfattar vanliga användnings fall. Det är avsett att hjälpa dig att migrera ditt webb program från Google Maps v3 JavaScript SDK till Azure Maps Web SDK. Kod exempel som är relaterade till webb program finns i Java Script. Azure Maps även tillhandahålla TypeScript-definitioner som ett ytterligare alternativ genom en [NPM-modul](how-to-use-map-control.md).

### <a name="load-a-map"></a>Läsa in en karta

När du läser in en karta följer du samma uppsättning steg i båda SDK: erna:

- Lägg till en referens till kart-SDK: n.
- Lägg till en `div`-tagg till sidans huvuddel, som fungerar som plats hållare för kartan.
- Skapa en JavaScript-funktion som anropas när sidan har lästs in.
- Skapa en instans av respektive kart klass.

**Vissa viktiga skillnader**

- Google Maps kräver att en konto nyckel anges i skript referensen för API: et. Autentiseringsuppgifter för Azure Maps anges som alternativ för kart klassen. Den här autentiseringsuppgiften kan vara en prenumerations nyckel eller Azure Active Directory information.
- Google Maps tar i en callback-funktion i API-referensen för API: et, som används för att anropa en initierings funktion för att läsa in kartan. Med Azure Maps onLoad-händelsen på sidan användas.
- När du refererar till det `div`-element som kartan återges i, kräver `Map`-klassen i Azure Maps bara `id` svärdet medan Google Maps kräver ett `HTMLElement`-objekt.
- Koordinater i Azure Maps definieras som positions objekt, som kan anges som en enkel siffer mat ris i formatet `[longitude, latitude]`.
- Zoomnings nivån i Azure Maps är en nivå som är lägre än zoomnings nivån i Google Maps. Den här avvikelsen beror på skillnaden i storleks förändrings systemet för de två plattformarna.
- Azure Maps lägger inte till några navigerings kontroller på kart arbets ytan. Som standard har en karta som standard inte zoomnings knappar och kart stils knappar. Det finns dock kontroller för att lägga till en mappnings väljare, zoomnings knappar, kompass-och rotations kontroll och en kanna-kontroll.
- En händelse hanterare läggs till i Azure Maps för att övervaka `ready`s händelsen för kart instansen. Den här händelsen kommer att utlösa när kartan har läst in WebGL-kontexten och alla nödvändiga resurser. Lägg till all kod som du vill köra när kartan är klar med inläsningen till den här händelse hanteraren.

I exemplen nedan visas hur du läser in en grundläggande karta som centreras över New York vid koordinater (longitud:-73,985, latitud: 40,747) och är på zoomnings nivå 12 i Google Maps.

**Före: Google Maps**

Följande kod är ett exempel på hur du visar en Google-karta som är centrerad och zoomad över en plats.

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

Om den här koden körs i en webbläsare visas en karta som ser ut som på följande bild:

<center>

![enkla Google Maps](media/migrate-google-maps-web-app/simple-google-map.png)</center>

**Efter: Azure Maps**

Följande kod visar hur du läser in en karta med samma vy i Azure Maps tillsammans med en kart kontroll och zoomnings knappar.

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

Om den här koden körs i en webbläsare visas en karta som ser ut som på följande bild:

<center>

![enkla Azure Maps](media/migrate-google-maps-web-app/simple-azure-maps.png)</center>

Detaljerad dokumentation om hur du konfigurerar och använder Azure Maps kart kontroll i en webbapp finns [här](how-to-use-map-control.md).

> [!NOTE]
> Till skillnad från Google Maps kräver Azure Maps inte något inledande Center och en zoomnings nivå som ska anges vid inläsning av kartan. Om den här informationen inte anges när du läser in kartan försöker kartan avgöra vilken stad användaren befinner sig i och kommer att centrera och zooma kartan där.

**Ytterligare resurser:**

- Azure Maps innehåller också navigerings kontroller för att rotera och stämma av kart visningen som dokumenteras [här](map-add-controls.md).

### <a name="localizing-the-map"></a>Lokalisera kartan

Om din mål grupp är uppdelad i flera länder eller talar olika språk är det viktigt att lokaliseringen är viktig.

**Före: Google Maps**

För att lokalisera Google Maps läggs parametrarna för språk och region till i

```html
<script type="text/javascript" src=" https://maps.googleapis.com/maps/api/js?callback=initMap&key=[api_key]& language=[language_code]&region=[region_code]" async defer></script>
```

Här är ett exempel på Google Maps med språket inställt på "fr-FR".

<center>

![Google Maps lokalisering](media/migrate-google-maps-web-app/google-maps-localization.png)</center>

**Efter: Azure Maps**

Azure Maps tillhandahåller två olika sätt att ange språk och regional vy för kartan. Det första alternativet är att lägga till den här informationen i namn området för den globala *atlasen* , vilket leder till att alla instanser av kart kontroll i appen används som standard för de här inställningarna. Följande ställer in språket på franska ("fr-FR") och den regionala vyn till "Auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

Det andra alternativet är att skicka den här informationen till kart alternativen när du läser in kartan som:

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
> Med Azure Maps är det möjligt att läsa in flera kart instanser på samma sida med olika språk-och region inställningar. Dessutom är det också möjligt att uppdatera inställningarna i kartan när den har lästs in. En detaljerad lista över språk som stöds i Azure Maps hittar du [här](supported-languages.md).

Här är ett exempel på Azure Maps med språket "fr" och användar regionen inställt på "fr-FR".

<center>

![Azure Maps lokalisering](media/migrate-google-maps-web-app/azure-maps-localization.png)</center>

### <a name="setting-the-map-view"></a>Ställa in Map-vyn

Dynamiska kartor i både Azure och Google Maps kan flyttas program mässigt till nya geografiska platser. Det gör du genom att anropa lämpliga funktioner i Java Script. I exemplen visas hur du gör kartan för att Visa satellit antenn bilder, centrera kartan över en plats och ändra zoomnings nivån till 15 i Google Maps. Följande plats koordinater används: longitud:-111,0225 och latitud: 35,0272.

> [!NOTE]
> Google Maps använder paneler som är 256 pixlar i dimensioner medan Azure Maps använder en större 512-pixel-panel. Detta minskar antalet nätverks begär Anden som krävs av Azure Maps för att läsa in samma mappnings område som Google Maps. Men på grund av hur panelbaserade pyramider fungerar i kart kontroller, innebär de större panelerna i Azure Maps att du kan uppnå samma visnings bara område som en karta i Google Maps, och du måste subtrahera zoomnings nivån som används i Google Maps med en när du använder Azure Maps.

**Före: Google Maps**

Kart kontrollen Google Maps kan flyttas program mässigt med hjälp av metoden `setOptions`. Med den här metoden kan du ange kartans centrum och zoomnings nivå.

```javascript
map.setOptions({
    mapTypeId: google.maps.MapTypeId.SATELLITE,
    center: new google.maps.LatLng(35.0272, -111.0225),
    zoom: 15
});
```

<center>

![Google Maps set View](media/migrate-google-maps-web-app/google-maps-set-view.png)</center>

**Efter: Azure Maps**

I Azure Maps kan kart positionen ändras program mässigt med hjälp av `setCamera`-metoden för kartan och kart formatet kan ändras med hjälp av `setStyle`-metoden. Koordinaterna i Azure Maps är i formatet "longitud, latitud" och värdet för zoomnings nivån subtraheras av ett.

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

![Azure Maps ange vy](media/migrate-google-maps-web-app/azure-maps-set-view.jpeg)</center>

**Ytterligare resurser:**

- [Välj en kart stil](choose-map-style.md)
- [Kart format som stöds](supported-map-styles.md)

### <a name="adding-a-marker"></a>Lägga till en markör

I Azure Maps finns det flera olika sätt att återge punkt data på kartan.

- **HTML-märken** – återger punkter med hjälp av traditionella dom-element. HTML-märken stöder dra.
- **Symbol lagret** – återger punkter med en ikon och/eller text inom WebGL-kontexten.
- **Bubbeldiagram** – återger punkter som cirklar på kartan. Cirklarnas radier kan skalas utifrån egenskaperna i data.

Både symbol-och bubbel lager återges i WebGL-kontexten. Båda skikten kan återge stora uppsättningar av punkter på kartan. Dessa lager kräver att data lagras i en data källa. Data källor och åter givnings lager ska läggas till i kartan när `ready` händelsen har utlösts. HTML-märken återges som DOM-element på sidan och använder inte en data källa. De fler DOM-element som en sida har, desto långsammare blir sidan. Om du återger fler än några hundra punkter på en karta rekommenderar vi att du använder ett av åter givnings lagren i stället.

I följande exempel lägger du till en markör till kartan vid (longitud:-0,2, latitud: 51,5) med siffran 10 som en etikett.

**Före: Google Maps**

Med Google Maps läggs markörer till i kartan med hjälp av klassen `google.maps.Marker` och genom att ange kartan som ett av alternativen.

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

I Azure Maps kan HTML-markörer användas för att visa en punkt på kartan. HTML-märken rekommenderas bara för appar som bara behöver visa ett litet antal punkter på kartan. Om du vill använda en HTML-markör skapar du en instans av klassen `atlas.HtmlMarker`, anger alternativ för text och position och lägger till markören till kartan med hjälp av metoden `map.markers.add`.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

<center>

![Azure Maps HTML-markör](media/migrate-google-maps-web-app/azure-maps-html-marker.png)</center>

**Efter: Azure Maps med ett symbol lager**

När du använder ett symbol lager måste data läggas till i en data källa och data källan som är kopplad till lagret. Dessutom bör data källan och lagret läggas till i kartan när `ready` händelsen har utlösts. Om du vill rendera ett unikt text värde ovanför en symbol måste text informationen lagras som en egenskap för data punkten och den egenskapen måste refereras till i `textField` alternativet för lagret. Den här metoden är lite mer arbete än att använda HTML-markörer, men det har prestanda för delar.

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

![Azure Maps symbol lager](media/migrate-google-maps-web-app/azure-maps-symbol-layer.png)</center>

**Ytterligare resurser:**

- [Skapa en data Källa](create-data-source-web-sdk.md)
- [Lägg till ett symbol lager](map-add-pin.md)
- [Lägg till ett bubbel-lager](map-add-bubble-layer.md)
- [Kluster punkts data](clustering-point-data-web-sdk.md)
- [Lägg till HTML-markörer](map-add-custom-html.md)
- [Använd data drivna format uttryck](data-driven-style-expressions-web-sdk.md)
- [Ikon alternativ för symbol skikt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Text alternativ för symbol skikt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [HTML-markör klass](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [Alternativ för HTML-markör](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-custom-marker"></a>Lägga till en anpassad markör

Anpassade bilder kan användas för att representera punkter på en karta. Följande kart bild använder en anpassad bild för att visa en punkt på kartan. Punkten visas på latitud: 51,5, longitud:-0,2. Ankar punkten förskjutnings positionen för markören så att ikonen för kartnålen justeras med rätt placering på kartan.

<center>

![gul kartnåls bild](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
YLW\_kartnål. png</center>

**Före: Google Maps**

I Google Maps skapas en anpassad markör genom att ange ett `Icon`-objekt som innehåller `url` till bilden, en `anchor` punkt för att justera punkten på kartnåls bilden med koordinaten på kartan. Ankar värdet i Google Maps är i förhållande till bildens övre vänstra hörn.

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

![Google Maps-anpassad markör](media/migrate-google-maps-web-app/google-maps-custom-marker.png)</center>

**Efter: Azure Maps med HTML-markörer**

För att anpassa en HTML-markör i Azure Maps ett HTML-`string` eller `HTMLElement` kan skickas till `htmlContent` alternativet för markören. I Azure Maps används alternativet `anchor` för att ange markörens relativa position, i förhållande till positions koordinaten med en av de nio definierade referens punkterna. De definierade referens punkterna är: "Center", "Top", "bottom", "left", "höger", "Top-Left", "Top-Right", "bottom-left", "längst ned till höger". Innehållet fästs längst ned i mitten av HTML-innehållet som standard. För att göra det enklare att migrera kod från Google Maps, ange `anchor` till "överst till vänster" och Använd sedan alternativet `pixelOffset` med samma förskjutning som används i Google Maps. Förskjutningarna i Azure Maps flyttas i motsatt riktning av Google Maps, så multiplicera dem med minus ett.

> [!TIP]
> Lägg till `pointer-events:none` som ett format på HTML-innehållet för att inaktivera standard beteendet dra i Microsoft Edge som visar en oönskad ikon.

```javascript
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

<center>

![Azure Maps anpassad HTML-markör](media/migrate-google-maps-web-app/azure-maps-custom-html-marker.png)</center>

**Efter: Azure Maps med ett symbol lager**

Symbol lager i Azure Maps stöder anpassade avbildningar också, men bilden måste läsas in i kart resurserna först och tilldelas ett unikt ID. Symbol lagret kan sedan referera till detta ID. Symbolen kan förskjutas för att justeras till rätt punkt på bilden med hjälp av ikonen `offset` alternativet. I Azure Maps används alternativet `anchor` för att ange symbolens relativa position i förhållande till positions koordinaten med en av de nio definierade referens punkterna. De definierade positions koordinaterna är: "Center", "Top", "bottom", "left", "höger", "Top-Left", "Top-Right", "bottom-left", "längst ned till höger". Innehållet fästs längst ned i mitten av HTML-innehållet som standard. För att göra det enklare att migrera kod från Google Maps, ange `anchor` till "överst till vänster" och Använd sedan alternativet `offset` med samma förskjutning som används i Google Maps. Förskjutningarna i Azure Maps flyttas i motsatt riktning av Google Maps, så multiplicera dem med minus ett.

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

![Azure Maps anpassade symbol nivå](media/migrate-google-maps-web-app/azure-maps-custom-icon-symbol-layer.png)</center>

> [!TIP]
> Om du vill skapa en avancerad anpassad åter givning av punkter använder du flera åter givnings lager tillsammans. Anta till exempel att du vill ha flera kartnålar som har samma ikon på olika färgade cirklar. I stället för att skapa en bunt med bilder för varje färg överlägg lägger du till ett symbol lager ovanpå ett bubbeldiagram och använder kartnålarna som referens till samma data källa. Den här metoden är mer effektiv än att skapa och underhålla en massa olika avbildningar.

**Ytterligare resurser:**

- [Skapa en data Källa](create-data-source-web-sdk.md)
- [Lägg till ett symbol lager](map-add-pin.md)
- [Lägg till HTML-markörer](map-add-custom-html.md)
- [Använd data drivna format uttryck](data-driven-style-expressions-web-sdk.md)
- [Ikon alternativ för symbol skikt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Text alternativ för symbol skikt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [HTML-markör klass](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [Alternativ för HTML-markör](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-polyline"></a>Lägga till en polyline

Polylinjer används för att representera en linje eller bana på kartan. I följande exempel visas hur du skapar en streckad polyline på kartan.

**Före: Google Maps**

I Google Maps tar polyline-klassen i en uppsättning alternativ. En matris med koordinater skickas i `path` alternativet för polylinje.

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

I Azure Maps kallas polystreck `LineString` eller `MultiLineString` objekt. Dessa objekt kan läggas till i en data källa och återges med hjälp av ett linje lager.

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

![Azure Maps polylinje](media/migrate-google-maps-web-app/azure-maps-polyline.png)</center>

**Ytterligare resurser:**

- [Lägg till rader till kartan](map-add-line-layer.md)
- [Alternativ för linje skikt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Använd data drivna format uttryck](data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>Lägga till en polygon

Polygoner används för att representera ett område på kartan. Azure Maps och Google Maps har liknande stöd för polygoner. I följande exempel visas hur du skapar en polygon som utgör en triangel baserat på kartans centrum-koordinat.

**Före: Google Maps**

I Google Maps tar klassen polygon i en uppsättning alternativ. En matris med koordinater skickas i `paths` alternativ för polygonen.

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

I Azure Maps kan `Polygon` och `MultiPolygon` objekt läggas till i en data källa och återges på kartan med hjälp av lager. Ytan i en polygon kan återges i ett polygon-lager. Dispositionen för en polygon kan återges med hjälp av ett linje lager.

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

- [Lägg till en polygon till kartan](map-add-shape.md)
- [Lägg till en cirkel till kartan](map-add-shape.md#add-a-circle-to-the-map)
- [Alternativ för polygon-lager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)
- [Alternativ för linje skikt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Använd data drivna format uttryck](data-driven-style-expressions-web-sdk.md)

### <a name="display-an-info-window"></a>Visa ett informations fönster

Ytterligare information för en entitet kan visas på kartan som en `google.maps.InfoWindow` klass i Google Maps. I Azure Maps kan den här funktionen uppnås med hjälp av klassen `atlas.Popup`. I följande exempel lägger du till en markör i kartan och när du klickar på visas ett informations fönster/popup-fönster.

**Före: Google Maps**

Med Google Maps skapas ett informations fönster med hjälp av `google.maps.InfoWindow` konstruktorn.

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

popup-](media/migrate-google-maps-web-app/google-maps-popup.png)</center> för ![Google Maps

**Efter: Azure Maps**

I Azure Maps kan en popup-meny användas för att visa ytterligare information för en plats. Ett HTML-`string` eller `HTMLElement`-objekt kan skickas till alternativet `content` i popup-fönstret. Om du vill kan popup-fönster visas oberoende av vilken form som helst. Det innebär att popup-fönster kräver att ett `position` värde anges. Om du vill visa ett popup-fönster, anropa metoden `open` och skicka i `map` där popup-fönstret ska visas.

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

![Azure Maps-popup](media/migrate-google-maps-web-app/azure-maps-popup.png)</center>

> [!NOTE]
> För att göra samma sak med ett symbol-, bubbeldiagram-, linje-eller polygon lager, kan du bara skicka lagret till Maps-händelseloggen i stället för en markör.

**Ytterligare resurser:**

- [Lägg till en popup](map-add-popup.md)
- [Popup med medie innehåll](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
- [Popup-fönster på former](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
- [Återanvända popup med flera PIN-bara](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
- [Popup-klass](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
- [Popup-alternativ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

### <a name="import-a-geojson-file"></a>Importera en multijson-fil

Google Maps stöder inläsning och dynamisk formatering av geografi-JSON-data via `google.maps.Data`-klassen. Funktionerna i den här klassen justerar mycket mer med den data drivna formateringen av Azure Maps. En viktig skillnad är att med Google Maps kan du ange en motringnings funktion. Affärs logiken för formatering av varje funktion som bearbetas individuellt i UI-tråden. I Azure Maps har lager stöd för att ange data drivna uttryck som format alternativ. Dessa uttryck bearbetas vid åter givnings tid i en separat tråd. Den här metoden ökar åter givningen av prestanda. Den här fördelen är att du märker att större data uppsättningar återges snabbt.

I följande exempel läses ett interjson-flöde över alla jord bävningar under de senaste sju dagarna från USGS DATAUPPSÄTTNINGEN. Den återger dem som skalade cirklar på kartan. Färg och skala för varje cirkel baseras på storleken på varje jord bävning, som lagras i egenskapen `"mag"` för varje funktion i data uppsättningen. Om storleken är större än eller lika med fem blir cirkeln röd. Om den är större än eller lika med tre, men mindre än fem, blir cirkeln orange. Om det är mindre än tre blir cirkeln grön. Radien för varje cirkel blir exponenten för den storlek som multipliceras med 0,1.

**Före: Google Maps**

I Google Maps kan en enda callback-funktion anges i metoden `map.data.setStyle`. Den här metoden används för att tillämpa affärs logik för varje funktion som läses in från en polyjson-feed via metoden `map.data.loadGeoJson`.

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

![Google Maps-injson-](media/migrate-google-maps-web-app/google-maps-geojson.png)</center>

**Efter: Azure Maps**

Interjson är den grundläggande data typen i Azure Maps och kan enkelt importeras till en data källa med hjälp av metoden `datasource.importFromUrl`. Ett bubbeldiagram innehåller funktioner för åter givning av skalade cirklar baserat på egenskaperna för funktionerna i en data källa. I stället för att ha en callback-funktion konverteras affärs logiken till ett uttryck och skickas till format alternativen. Uttryck definierar hur affärs logiken fungerar. Uttryck kan skickas till en annan tråd och utvärderas mot funktions data. Du kan lägga till flera data källor och lager i Azure Maps, var och en med olika affärs logik. Med den här funktionen kan flera data uppsättningar återges på kartan på olika sätt.

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

![Azure Maps-injson-](media/migrate-google-maps-web-app/azure-maps-geojson.png)</center>

**Ytterligare resurser:**

- [Lägg till ett symbol lager](map-add-pin.md)
- [Lägg till ett bubbel-lager](map-add-bubble-layer.md)
- [Kluster punkts data](clustering-point-data-web-sdk.md)
- [Använd data drivna format uttryck](data-driven-style-expressions-web-sdk.md)

### <a name="marker-clustering"></a>Markör kluster

När du visualiserar många data punkter på kartan överlappar de varandra, och kartan ser rörig ut och blir svår att se och använda. Klustring av punkt data kan användas för att förbättra användar upplevelsen och även förbättra prestandan. Kluster plats data är en process för att kombinera punkt data som ligger nära varandra och som representerar dem på kartan som en enda klustrad data punkt. När användaren zoomar in i kartan delas klustren isär i sina enskilda data punkter.

I följande exempel laddar kod in en jord bävning data från den senaste veckan och lägger till den till kartan. Kluster återges som skalade och färgade cirklar beroende på antalet punkter som de innehåller.

> [!NOTE]
> Det finns flera olika algoritmer som används för markör kluster. Google och Azure Maps använder något annorlunda algoritmer. Därför kan det hända att punkt fördelningen i klustren varierar.

**Före: Google Maps**

I Google Maps-markörer kan grupperas genom att läsas in i MarkerClusterer-biblioteket. Kluster ikonerna är begränsade till bilder, som har numren ett till fem som namn och de finns i samma katalog.

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

](media/migrate-google-maps-web-app/google-maps-clustering.png)</center> för ![Google Maps-kluster

**Efter: Azure Maps**

I Azure Maps läggs data till och hanteras av en data källa. Lager ansluter till data källor och återger data i dem. `DataSource`-klassen i Azure Maps innehåller flera kluster alternativ.

- `cluster` – anger data källan till kluster punkt data.
- `clusterRadius`-radien i pixlar till kluster punkter tillsammans.
- `clusterMaxZoom` – den högsta zoomnings nivån i vilken klustring sker. Om du zoomar in mer än den här nivån återges alla punkter som symboler.
- `clusterProperties` – definierar anpassade egenskaper som beräknas med uttryck mot alla punkter i varje kluster och läggs till i egenskaperna för varje kluster punkt.

När klustring är aktiverat skickar data källan klustrade och data punkter som inte är klustrade till lager för åter givning. Data källan kan klustra hundratals tusen data punkter. En klustrad data punkt har följande egenskaper:

| Egenskapsnamn             | Typ    | Beskrivning   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | Anger om funktionen representerar ett kluster. |
| `cluster_id`              | sträng  | Ett unikt ID för klustret som kan användas med data källan `getClusterExpansionZoom`, `getClusterChildren`och `getClusterLeaves` metoder. |
| `point_count`             | nummer  | Antalet platser som klustret innehåller.  |
| `point_count_abbreviated` | sträng  | En sträng som förkortar `point_count` svärdet om det är långt. (till exempel 4 000 blir 4K)  |

`DataSource`-klassen har följande hjälp funktion för att få åtkomst till ytterligare information om ett kluster med hjälp av `cluster_id`.

| Metod | Returtyp | Beskrivning |
|--------|-------------|-------------|
| `getClusterChildren(clusterId: number)` | Funktionen Promise&lt;array&lt;Feature&lt;Geometry, alla&gt; \| former&gt;&gt; | Hämtar underordnade för det aktuella klustret på nästa zoomnings nivå. Dessa underordnade kan vara en kombination av former och under kluster. Under klustren är funktioner med egenskaper som matchar ClusteredProperties. |
| `getClusterExpansionZoom(clusterId: number)` | Promise&lt;Number&gt; | Beräknar en zoomnings nivå där klustret börjar expandera eller dela upp. |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | Funktionen Promise&lt;array&lt;Feature&lt;Geometry, alla&gt; \| former&gt;&gt; | Hämtar alla punkter i ett kluster. Ange `limit` för att returnera en delmängd av punkterna och Använd `offset` för att bläddra igenom punkterna. |

När du återger klustrade data på kartan är det ofta enklast att använda två eller flera lager. I följande exempel används tre lager. Ett bubbeldiagram för ritning som skalas ut färgade cirklar baserat på kluster storleken. Ett symbol lager som återger kluster storleken som text. Och använder det ett andra symbol lager för att återge de ogrupperade punkterna. Det finns många andra sätt att återge klustrade data på. Mer information finns i dokumentationen till [kluster plats data](clustering-point-data-web-sdk.md) .

Du kan importera data från en data källa direkt i Azure Maps med hjälp av funktionen `importDataFromUrl` i `DataSource`-klassen.

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

![Azure Maps klustring](media/migrate-google-maps-web-app/azure-maps-clustering.png)</center>

**Ytterligare resurser:**

- [Lägg till ett symbol lager](map-add-pin.md)
- [Lägg till ett bubbel-lager](map-add-bubble-layer.md)
- [Kluster punkts data](clustering-point-data-web-sdk.md)
- [Använd data drivna format uttryck](data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>Lägg till en värme karta

Värme kartor, även kallade punkt Täthets kartor, är en typ av data visualisering. De används för att representera densiteten för data med hjälp av en uppsättning färger. Och används ofta för att visa data "aktiva punkter" på en karta. Värme kartor är ett bra sätt att återge data uppsättningar med stora punkter.

I följande exempel läses ett interjson-flöde över alla jord bävningar under den senaste månaden från USGS DATAUPPSÄTTNINGEN och återges som en viktad termisk karta där `"mag"`-egenskapen används som vikt.

**Före: Google Maps**

För att skapa en värme karta i Google Maps måste du läsa in biblioteket "visualisering" genom att lägga till `&libraries=visualization` i URL: en för API-skriptet. Värme kart skiktet i Google Maps stöder inte geografi-data direkt. Data måste först hämtas och konverteras till en matris med viktade data punkter.

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

![Google Maps termisk Map](media/migrate-google-maps-web-app/google-maps-heatmap.png)</center>

**Efter: Azure Maps**

I Azure Maps läser du in de interjson-data till en data källa och ansluter data källan till ett termiskt kart skikt. Den egenskap som ska användas för vikten kan skickas till `weight` alternativet med ett uttryck. Du kan importera data från en data källa direkt i Azure Maps med hjälp av funktionen `importDataFromUrl` i `DataSource`-klassen.

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

![Azure Maps termisk karta](media/migrate-google-maps-web-app/azure-maps-heatmap.png)</center>

**Ytterligare resurser:**

- [Lägg till ett värme kart skikt](map-add-heat-map-layer.md)
- [Termisk kart skikts klass](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.heatmaplayer?view=azure-iot-typescript-latest)
- [Alternativ för termisk kart skikt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)
- [Använd data drivna format uttryck](data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>Täcka över ett panel lager

Panel lager kallas även bild överlägg i Google Maps. Med panel lager kan du täcka över stora bilder som har delats upp i mindre bild rutor som överensstämmer med Kartornas placerings system. Det här sättet används ofta för att täcka stora bilder eller stora data mängder.

I följande exempel överlappar ett väder radar panels lager från Iowa Environment-Mesonet för Iowa State University.

**Före: Google Maps**

I Google Maps kan panel lager skapas med hjälp av klassen `google.maps.ImageMapType`.

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

![Google Maps panels lager](media/migrate-google-maps-web-app/google-maps-tile-layer.png)</center>

**Efter: Azure Maps**

I Azure Maps kan ett panel lager läggas till i kartan på samma sätt som andra lager. En formaterad URL som har i x, y, zoomnings plats hållare; `{x}`är `{y}``{z}` används för att berätta för lagret var du ska få åtkomst till panelerna. Azure Maps panel lager stöder också `{quadkey}`-, `{bbox-epsg-3857}`-och `{subdomain}` plats hållare.

> [!TIP]
> I Azure Maps lager kan enkelt återges under andra lager, inklusive bas kart skikt. Det är ofta önskvärt att återge panel lager under kart etiketterna så att de är lätta att läsa. Metoden `map.layers.add` tar i en andra parameter, som är ID: t för det lager där det nya lagret ska infogas. Följande kod kan användas för att infoga ett panel lager under kart etiketterna: `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

<center>

![Azure Maps panels lager](media/migrate-google-maps-web-app/azure-maps-tile-layer.png)</center>

> [!TIP]
> Panel begär Anden kan samlas in med hjälp av alternativet `transformRequest` på kartan. Detta gör att du kan ändra eller lägga till rubriker i begäran om det behövs.

**Ytterligare resurser:**

- [Lägg till panel lager](map-add-tile-layer.md)
- [Panels skikt klass](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)
- [Alternativ för panel lager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

### <a name="show-traffic"></a>Visa trafik

Trafik data kan skrivas över både Azure och Google Maps.

**Före: Google Maps**

I Google Maps kan trafik data överlappa på kartan med hjälp av trafik skiktet.

```javascript
var trafficLayer = new google.maps.TrafficLayer();
trafficLayer.setMap(map);
```

<center>

![Google Maps-trafik](media/migrate-google-maps-web-app/google-maps-traffic.png)</center>

**Efter: Azure Maps**

Azure Maps tillhandahåller flera olika alternativ för att Visa trafik. Trafik incidenter, till exempel väg stängningar och haverier kan visas som ikoner på kartan. Trafikflöde, färgkodade vägar, kan skrivas över på kartan och färgerna kan ändras så att de baseras på den angivna hastighets gränsen, i förhållande till den normala förväntade fördröjningen eller absolut fördröjning. Incident data i Azure Maps uppdateringar varje minut och flödar data uppdateringar var 2: e minut.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

<center>

![Azure Maps trafik](media/migrate-google-maps-web-app/azure-maps-traffic.png)</center>

Om du klickar på en av trafik ikonerna i Azure Maps visas ytterligare information på popup-menyn.

<center>

![Azure Maps trafik incident](media/migrate-google-maps-web-app/azure-maps-traffic-incident.png)</center>

**Ytterligare resurser:**

- [Visa trafik på kartan](map-show-traffic.md)
- [Alternativ för trafik överlägg](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)

### <a name="add-a-ground-overlay"></a>Lägg till ett mark överlägg

Både Azure och Google Maps stöder överlägg av de refererade bilderna på kartan så att de flyttas och skalas när du panorerar och zoomar kartan. I Google Maps kallas dessa för mark överlägg i Azure Maps de kallas för bild lager. Detta är bra för att skapa vånings planer, täcka gamla kartor eller bilder från en drönare.

**Före: Google Maps**

När du skapar ett mark överlägg i Google Maps måste du ange URL: en till bilden som ska överlappa och en avgränsnings ruta för att binda bilden till på kartan. I det här exemplet läggs en kart bild av [Newark New Jersey från 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) på kartan.

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

Om den här koden körs i en webbläsare visas en karta som ser ut som på följande bild:

<center>

![Google Maps bild överlägg](media/migrate-google-maps-web-app/google-maps-image-overlay.png)</center>

**Efter: Azure Maps**

I Azure Maps kan de refererade avbildningarna överordnas med hjälp av `atlas.layer.ImageLayer`-klassen. Den här klassen kräver en URL till en bild och en uppsättning koordinater för bildens fyra hörn. Avbildningen måste vara värd för antingen en domän eller ha CORs aktiverat.

> [!TIP]
> Om du bara har Nord-, syd-, väst-och rotations information och inte koordinerar för varje hörn i bilden, kan du använda den statiska [`atlas.layer.ImageLayer.getCoordinatesFromEdges`](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest#getcoordinatesfromedges-number--number--number--number--number-) metoden.

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

![Azure Maps avbildnings överlägg](media/migrate-google-maps-web-app/azure-maps-image-overlay.png)</center>

**Ytterligare resurser:**

- [Täcka över en bild](map-add-image-layer.md)
- [Bild skikts klass](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

## <a name="additional-code-samples"></a>Ytterligare kod exempel

Här följer några ytterligare kod exempel som är relaterade till Google Maps-migrering:

- [Rit verktyg](map-add-drawing-toolbar.md)
- [Begränsa kartan till två fingrar för panorering](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Map%20to%20Two%20Finger%20Panning)
- [Begränsa zoom av rullnings hjul](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Scroll%20Wheel%20Zoom)
- [Skapa en hel skärms kontroll](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Create%20a%20Fullscreen%20Control)

**Terminal**

- [Använda modulen Azure Maps tjänster](how-to-use-services-module.md)
- [Sök efter orienteringspunkter](map-search-location.md)
- [Hämta information från en koordinat (omvänd landskod)](map-get-information-from-coordinate.md)
- [Visa vägvisningar från A till B](map-route.md)
- [Sök automatiska förslag med JQuery-användargränssnitt](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

## <a name="google-maps-v3-to-azure-maps-web-sdk-class-mapping"></a>Google Maps v3 till Azure Maps klass mappning för webb SDK

Följande bilaga innehåller en kors referens för de ofta använda klasserna i Google Maps v3 och Azure Maps-motsvarigheten till webb-SDK.

### <a name="core-classes"></a>Core-klasser

| Google Maps   | Azure Maps  |
|---------------|-------------|
| `google.maps.Map` | [Tamazight. Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindow` | [Tamazight. Motta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindowOptions` | [Tamazight. PopupOptions](https://docs.microsoft.com/) |
| `google.maps.LatLng`  | [Atlas. data. position](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest)  |
| `google.maps.LatLngBounds` | [Atlas. data. BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) |
| `google.maps.MapOptions`  | [Tamazight. CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions?view=azure-iot-typescript-latest)<br/>[Tamazight. CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions?view=azure-iot-typescript-latest)<br/>[Tamazight. ServiceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions?view=azure-iot-typescript-latest)<br/>[Tamazight. StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions?view=azure-iot-typescript-latest)<br/>[Tamazight. UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions?view=azure-iot-typescript-latest) |
| `google.maps.Point`  | [Tamazight. Stor](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.pixel?view=azure-iot-typescript-latest)   |

## <a name="overlay-classes"></a>Överläggs klasser

| Google Maps  | Azure Maps  |
|--------------|-------------|
| `google.maps.Marker` | [Tamazight. HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)<br/>[Atlas. data. Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)  |
| `google.maps.MarkerOptions`  | [Tamazight. HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)<br/>[Atlas. Layer. SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)<br/>[Tamazight. SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)<br/>[Tamazight. IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)<br/>[Tamazight. TextOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)<br/>[Atlas. Layer. BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)<br/>[Tamazight. BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.Polygon`  | [Atlas. data. polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)               |
| `google.maps.PolygonOptions` |[Atlas. Layer. PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)<br/> [Tamazight. PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)<br/> [Atlas. Layer. LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)<br/> [Tamazight. LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)|
| `google.maps.Polyline` | [Atlas. data. Lin Est ring](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest)         |
| `google.maps.PolylineOptions` | [Atlas. Layer. LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-maps-typescript-latest)<br/>[Tamazight. LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-maps-typescript-latest) |
| `google.maps.Circle`  | Se [lägga till en cirkel till kartan](map-add-shape.md#add-a-circle-to-the-map)                                     |
| `google.maps.ImageMapType`  | [Tamazight. TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)         |
| `google.maps.ImageMapTypeOptions` | [Tamazight. TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.GroundOverlay`  | [Atlas. Layer. ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)<br/>[Tamazight. ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest) |

## <a name="service-classes"></a>Tjänst klasser

Azure Maps Web SDK innehåller en [Services-modul som kan läsas in separat. Den här modulen radbryter Azure Maps REST-tjänster med ett webb-API och kan användas i Java Script-, TypeScript-och Node. js-program.

| Google Maps | Azure Maps  |
|-------------|-------------|
| `google.maps.Geocoder` | [Atlas. service. SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |
| `google.maps.GeocoderRequest`  | [Tamazight. SearchAddressOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressoptions?view=azure-iot-typescript-latest)<br/>[Tamazight. SearchAddressRevrseOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreverseoptions?view=azure-iot-typescript-latest)<br/>[Tamazight. SearchAddressReverseCrossStreetOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreversecrossstreetoptions?view=azure-iot-typescript-latest)<br/>[Tamazight. SearchAddressStructuredOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressstructuredoptions?view=azure-iot-typescript-latest)<br/>[Tamazight. SearchAlongRouteOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchalongrouteoptions?view=azure-iot-typescript-latest)<br/>[Tamazight. SearchFuzzyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchfuzzyoptions?view=azure-iot-typescript-latest)<br/>[Tamazight. SearchInsideGeometryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchinsidegeometryoptions?view=azure-iot-typescript-latest)<br/>[Tamazight. SearchNearbyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchnearbyoptions?view=azure-iot-typescript-latest)<br/>[Tamazight. SearchPOIOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoioptions?view=azure-iot-typescript-latest)<br/>[Tamazight. SearchPOICategoryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoicategoryoptions?view=azure-iot-typescript-latest) |
| `google.maps.DirectionsService`  | [Atlas. service. RouteUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)  |
| `google.maps.DirectionsRequest`  | [Tamazight. CalculateRouteDirectionsOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.calculateroutedirectionsoptions?view=azure-iot-typescript-latest) |
| `google.maps.places.PlacesService` | [Atlas. service. SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |

## <a name="libraries"></a>Bibliotek

Bibliotek lägger till ytterligare funktioner till kartan. Många av dessa finns i kärn SDK för Azure Maps. Här följer några likvärdiga klasser som kan användas i stället för dessa Google Maps-bibliotek

| Google Maps           | Azure Maps   |
|-----------------------|--------------|
| Ritnings bibliotek       | [Modul för ritningsverktyg](set-drawing-options.md) |
| Geometri bibliotek      | [Atlas. matematik](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)   |
| Visualiserings bibliotek | [Termiskt kart skikt](map-add-heat-map-layer.md) |

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Maps Web SDK.

> [!div class="nextstepaction"]
> [Så här använder du Kartkontroll](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Använda modulen tjänster](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Använda modulen verktyg för ritning](set-drawing-options.md)

> [!div class="nextstepaction"]
> [Kodexempel](https://docs.microsoft.com/samples/browse/?products=azure-maps)

