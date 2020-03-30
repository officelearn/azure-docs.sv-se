---
title: Lägg till en popup till en punkt på en karta | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du lägger till en popup till en punkt med Hjälp av Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: cf6424d2a6cbcfb7c5052201b5a9190c81fddaff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055954"
---
# <a name="add-a-popup-to-the-map"></a>Lägga till en popup på kartan

Den här artikeln visar hur du lägger till en popup till en punkt på en karta.

## <a name="understand-the-code"></a>Förstå koden

Följande kod lägger till en `name` `description` punktfunktion, som har och egenskaper, på kartan med hjälp av ett symbollager. En förekomst av [klassen Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup) skapas men visas inte. Mushändelser läggs till i symbollagret för att utlösa öppnandet och stängningen av popup-fönstret. När markörsymbolen hovrar uppdateras `position` popup-egenskapen med markörens `content` position och alternativet uppdateras `name` med `description` viss HTML som radbryts och egenskaperna för punktfunktionen som hovrade. Popup-fönstret visas sedan på kartan `open` med hjälp av dess funktion.

```javascript
//Define an HTML template for a custom popup content laypout.
var popupTemplate = '<div class="customInfobox"><div class="name">{name}</div>{description}</div>';

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

dataSource.add(new atlas.data.Feature(new atlas.data.Point([-122.1333, 47.63]), {
  name: 'Microsoft Building 41', 
  description: '15571 NE 31st St, Redmond, WA 98052'
}));

//Create a layer to render point data.
var symbolLayer = new atlas.layer.SymbolLayer(dataSource);

//Add the polygon and line the symbol layer to the map.
map.layers.add(symbolLayer);

//Create a popup but leave it closed so we can update it and display it later.
popup = new atlas.Popup({
  pixelOffset: [0, -18],
  closeButton: false
});

//Add a hover event to the symbol layer.
map.events.add('mouseover', symbolLayer, function (e) {
  //Make sure that the point exists.
  if (e.shapes && e.shapes.length > 0) {
    var content, coordinate;
    var properties = e.shapes[0].getProperties();
    content = popupTemplate.replace(/{name}/g, properties.name).replace(/{description}/g, properties.description);
    coordinate = e.shapes[0].getCoordinates();

    popup.setOptions({
      //Update the content of the popup.
      content: content,

      //Update the popup's position with the symbol's coordinate.
      position: coordinate

    });
    //Open the popup.
    popup.open(map);
  }
});

map.events.add('mouseleave', symbolLayer, function (){
  popup.close();
});
```

Nedan visas det fullständiga kodexemplet för ovanstående funktioner.

<br/>

<iframe height='500' scrolling='no' title='Lägga till ett popup-fönster med Azure Maps' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Lägg till ett popup-fönster med Azure Maps</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Återanvända en popup med flera punkter

Det finns fall där det bästa sättet är att skapa en popup och återanvända den. Du kan till exempel ha ett stort antal punkter och bara vill visa en popup i taget. Genom att återanvända popup, antalet DOM element som skapats av programmet är kraftigt reducerad, vilket kan ge bättre prestanda. I följande exempel skapas 3-punktsfunktioner. Om du klickar på någon av dem visas en popup med innehållet för den punktfunktionen.

<br/>

<iframe height='500' scrolling='no' title='Återanvända popup med flera pins' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se popup-fönstret <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>Pen Reusing med flera pins</a> från Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-a-popup"></a>Anpassa en popup-bild

Som standard har popup-fönstret en vit bakgrund, en pekarpil längst ned och en stängningsknapp i det övre högra hörnet. Följande exempel ändrar bakgrundsfärgen `fillColor` till svart med hjälp av alternativet popup. Stängningsknappen tas bort `CloseButton` genom att ange alternativet till false. HTML-innehållet i popup använder vadderade av 10 pixlar från kanterna på popup.The HTML content of the popup uses vadderade av 10 pixlar från kanterna på popup.The HTML content of the popup uses vadderade av 10 pixels från kanterna på popup.The HTML content of the popup uses vad Texten är vit, så den dyker upp fint på den svarta bakgrunden.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Anpassad popup" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se pen <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>customized popup</a> av<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-popup-templates-to-the-map"></a>Lägga till popup-mallar på kartan

Popup-mallar gör det enkelt att skapa datadrivna layouter för popup-fönster. Avsnitten nedan visar användningen av olika popup-mallar för att generera formaterat innehåll med hjälp av egenskaper funktioner.

> [!NOTE]
> Som standard kommer allt innehåll som återges att använda popup-mallen att slipas inuti en iframe som en säkerhetsfunktion. Det finns dock begränsningar:
>
> - Alla skript, formulär, pekarlås och de övre navigeringsfunktionerna är inaktiverade. Länkar kan öppnas på en ny flik när du klickar på den. 
> - Äldre webbläsare som inte stöder `srcdoc` parametern på iframes kommer att begränsas till att återge en liten mängd innehåll.
> 
> Om du litar på de data som laddas i popups och eventuellt vill att dessa skript laddas i popups kunna komma åt ditt program, kan du inaktivera detta genom att ställa in popup-mallar `sandboxContent` alternativet till false. 

### <a name="string-template"></a>Strängmall

Strängmallen ersätter platshållare med värden för funktionsegenskaperna. Egenskaperna för funktionen behöver inte tilldelas ett värde av typen String. Innehåller till `value1` exempel ett heltal. Dessa värden skickas sedan till egenskapen content för `popupTemplate`. 

Alternativet `numberFormat` anger formatet på det nummer som ska visas. Om `numberFormat` inte det anges använder koden datumformatet för popup-mallar. Alternativet `numberFormat` formaterar tal med funktionen [Number.toLocaleString.](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) Om du vill formatera `numberFormat` stora tal kan du använda alternativet med funktioner från [NumberFormat.format](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/NumberFormat/format). Kodavsnittet nedan använder `maximumFractionDigits` till exempel för att begränsa antalet bråksiffror till två.

> [!Note]
> Det finns bara ett sätt på vilket strängmallen kan återge bilder. Först måste strängmallen ha en bildtagg i den. Värdet som skickas till bildtaggen ska vara en URL till en bild. Sedan måste strängmallen `isImage` ha inställt på `HyperLinkFormatOptions`true i . Alternativet `isImage` anger att hyperlänken är till för en bild och att hyperlänken ska läsas in i en bildtagg. När du klickar på hyperlänken öppnas bilden.

```javascript
var templateOptions = {
  content: 'This template uses a string template with placeholders.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 1 - String template',
    value1: 1.2345678,
    value2: {
        subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6]
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="propertyinfo-template"></a>Mall PropertyInfo

Mallen PropertyInfo visar tillgängliga egenskaper för funktionen. Alternativet `label` anger vilken text som ska visas för användaren. Om `label` inget anges visas hyperlänken. Och om hyperlänken är en bild visas värdet som tilldelats alt-taggen. Anger `dateFormat` datumets format och om datumformatet inte anges återges datumet som en sträng. Alternativet `hyperlinkFormat` återger klickbara länkar, på `email` samma sätt kan alternativet användas för att återge klickbara e-postadresser.

Innan PropertyInfo-mallen visar egenskaperna för slutanvändaren kontrollerar den rekursivt att egenskaperna verkligen har definierats för den funktionen. Den ignorerar också visning av format- och titelegenskaper. Den `color`visas till exempel inte `size` `anchor`, `strokeOpacity`, `visibility`, och . När egenskapssökvägskontrollen är klar i backend visar mallen PropertyInfo innehållet i ett tabellformat.

```javascript
var templateOptions = {
  content: [
    {
        propertyPath: 'createDate',
        label: 'Created Date'
    },
    {
        propertyPath: 'dateNumber',
        label: 'Formatted date from number',
        dateFormat: {
          weekday: 'long',
          year: 'numeric',
          month: 'long',
          day: 'numeric',
          timeZone: 'UTC',
          timeZoneName: 'short'
        }
    },
    {
        propertyPath: 'url',
        label: 'Code samples',
        hideLabel: true,
        hyperlinkFormat: {
          lable: 'Go to code samples!',
          target: '_blank'
        }
    },
    {
        propertyPath: 'email',
        label: 'Email us',
        hideLabel: true,
        hyperlinkFormat: {
          target: '_blank',
          scheme: 'mailto:'
        }
    }
  ]
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 2 - PropertyInfo',
    createDate: new Date(),
    dateNumber: 1569880860542,
    url: 'https://aka.ms/AzureMapsSamples',
    email: 'info@microsoft.com'
}),

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="multiple-content-templates"></a>Flera innehållsmallar

En funktion kan också visa innehåll med hjälp av en kombination av strängmallen och PropertyInfo-mallen. I det här fallet återger stringmallen platshållarvärden på en vit bakgrund.  Och propertyInfo-mallen återger en bild med full bredd i en tabell. Egenskaperna i det här exemplet liknar de egenskaper som vi förklarade i föregående exempel.

```javascript
var templateOptions = {
  content: [
    'This template has two pieces of content; a string template with placeholders and a array of property info which renders a full width image.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
    [{
      propertyPath: 'imageLink',
      label: 'Image',
      hideImageLabel: true,
      hyperlinkFormat: {
        isImage: true
      }
    }]
  ],
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 3 - Multiple content template',
    value1: 1.2345678,
    value2: {
    subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6],
    imageLink: 'https://azuremapscodesamples.azurewebsites.net/common/images/Pike_Market.jpg'
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="points-without-a-defined-template"></a>Punkter utan en definierad mall

När popup-mallen inte definieras som en String-mall, en PropertyInfo-mall eller en kombination av båda, används standardinställningarna. När `title` och `description` är de enda tilldelade egenskaperna visar popup-mallen en vit bakgrund, en stängningsknapp i det övre högra hörnet. Och på små och medelstora skärmar visar den en pil längst ner. Standardinställningarna visas i en tabell för `title` alla `description`andra egenskaper än . Även när du faller tillbaka till standardinställningarna kan popup-mallen fortfarande manipuleras programmässigt. Användare kan till exempel inaktivera identifiering av hyperlänkar och standardinställningarna gäller fortfarande för andra egenskaper.

Klicka på punkterna på kartan i CodePen. Det finns en punkt på kartan för var och en av följande popup-mallar: Strängmall, PropertyInfo-mall och Mall för flera innehåll. Det finns också tre punkter för att visa hur mallar återges med standardinställningarna.

<br/>

<iframe height='500' scrolling='no' title='PopupTemplates' src='//codepen.io/azuremaps/embed/dyovrzL/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se Pen <a href='https://codepen.io/azuremaps/pen/dyovrzL/'>PopupTemplates</a> by<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reuse-popup-template"></a>Återanvänd popup-mall

I likhet med återanvändning popup, kan du återanvända popup-mallar. Den här metoden är användbar när du bara vill visa en popup-mall i taget, för flera punkter. Genom att återanvända popup-mallen minskas antalet DOM-element som skapas av programmet, vilket sedan förbättrar programmets prestanda. I följande exempel används samma popup-mall för tre punkter. Om du klickar på någon av dem visas en popup med innehållet för den punktfunktionen.

<br/>

<iframe height='500' scrolling='no' title='ÅteranvändaPopupTemplate' src='//codepen.io/azuremaps/embed/WNvjxGw/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se Pen <a href='https://codepen.io/azuremaps/pen/WNvjxGw/'>ReusePopupTemplate</a> by<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="popup-events"></a>Popup-evenemang

Popup-fönster kan öppnas, stängas och dras. Popup-klassen tillhandahåller händelser som hjälper utvecklare att reagera på dessa händelser. I följande exempel markeras vilka händelser som avfyras när användaren öppnar, stänger eller drar popup-fönstret. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Popup-evenemang" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se pen <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>popup-händelserna</a> av<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup)

> [!div class="nextstepaction"]
> [PopupOptions (olika)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions)

> [!div class="nextstepaction"]
> [PopupTemplate (popuptemplate)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popuptemplate)

Se följande bra artiklar för fullständiga kodexempel:

> [!div class="nextstepaction"]
> [Lägga till ett symbolskikt](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Lägga till en HTML-markör](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Lägg till ett linjeskikt](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Lägg till ett polygonskikt](map-add-shape.md)
