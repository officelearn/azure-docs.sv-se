---
title: Lägg till en popup-meny till en plats på en karta | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du lägger till ett popup-fönster till en punkt med hjälp av Microsoft Azure Maps-webbsdk.
author: jingjing-z
ms.author: jinzh
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 588de08666930937c3ad965b2609f8e207b75eca
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2020
ms.locfileid: "78208856"
---
# <a name="add-a-popup-to-the-map"></a>Lägg till en popup till kartan

Den här artikeln visar hur du lägger till en popup-meny till en plats på en karta.

## <a name="understand-the-code"></a>Förstå koden

Följande kod lägger till en punkt funktion som har `name` och `description` egenskaper till kartan med ett symbol lager. En instans av [klassen pop](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) skapas men visas inte. Mus händelser läggs till i symbol lagret för att utlösa öppning och stängning av popup-fönstret. När markör symbolen har hovrat, uppdateras popup-fönstrets `position`-egenskap med markörens position och alternativet `content` uppdateras med HTML-`name` kod och `description` egenskaperna för punkt funktionen har hovras. Popup-fönstret visas sedan på kartan med dess `open`-funktion.

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

Nedan visas det fullständiga kod exemplet för ovanstående funktioner.

<br/>

<iframe height='500' scrolling='no' title='Lägg till ett popup med Azure Maps' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Lägg till ett popup med Azure Maps</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Återanvända en popup med flera punkter

Det finns fall där det bästa sättet är att skapa en popup-meny och återanvända den. Du kan till exempel ha ett stort antal punkter och bara visa en popup i taget. Genom att återanvända popup-fönstret minskas antalet DOM-element som skapats av programmet, vilket kan ge bättre prestanda. I följande exempel skapas tre-punkt-funktioner. Om du klickar på någon av dem visas en popup med innehållet för den punkt funktionen.

<br/>

<iframe height='500' scrolling='no' title='Återanvända popup med flera PIN-bara' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se popup-fönstret för att <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>använda pennan med flera stift</a> genom att Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-a-popup"></a>Anpassa en popup

Som standard har popup-fönstret en vit bakgrund, en pekare pilen längst ned och en stängnings knapp i det övre högra hörnet. Följande exempel ändrar bakgrunds färgen till svart med alternativet `fillColor` i popup-fönstret. Knappen Stäng tas bort genom att ange alternativet `CloseButton` till falskt. HTML-innehållet i popup-fönstret använder utfyllt med 10 pixlar från kanten på popup-fönstret. Texten blir vit, så den visas snyggt på den svarta bakgrunden.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Anpassad popup" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se popup-fönstret för <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>anpassad</a> penna genom att Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-popup-templates-to-the-map"></a>Lägg till pop-mallar på kartan

Popup-mallar gör det enkelt att skapa data drivna layouter för popup-fönster. I avsnitten nedan visas hur du använder olika pop-mallar för att skapa formaterat innehåll med hjälp av egenskaper för funktioner.

### <a name="string-template"></a>Strängkonstant

Sträng mal len ersätter plats hållare med värden för funktions egenskaperna. Egenskaperna för funktionen behöver inte tilldelas ett värde av typen sträng. `value1` innehåller till exempel ett heltal. Dessa värden skickas sedan till `popupTemplate`s innehålls egenskap. 

Alternativet `numberFormat` anger formatet för det tal som ska visas. Om `numberFormat` inte har angetts använder koden datum formatet pop templates. Alternativet `numberFormat` formaterar tal med hjälp av funktionen [number. toLocaleString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) . Överväg att använda alternativet `numberFormat` med Functions från [NumberFormat. format](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/NumberFormat/format)för att formatera stora tal. Kodfragmentet nedan använder till exempel `maximumFractionDigits` för att begränsa antalet bråk tals siffror till två.

> [!Note]
> Det finns bara ett sätt i vilken sträng mal len kan återge bilder. Först måste sträng mal len ha en bildtagg. Värdet som skickas till bildtaggen ska vara en URL till en bild. Sedan måste sträng mal len ha `isImage` angetts till true i `HyperLinkFormatOptions`. Alternativet `isImage` anger att hyperlänken är för en bild och att hyperlänken läses in i en bildtagg. När användaren klickar på hyperlänken öppnas bilden.

```javascript
new atlas.data.Feature(new atlas.data.Point([-20, -20]), {
    title: 'Template 1 - String template',
    value1: 1.2345678,
    value2: {
        subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6],
    popupTemplate: {
        content: 'This template uses a string template with placeholders.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
        numberFormat: {
            maximumFractionDigits: 2
        }
    }
}),
```

### <a name="propertyinfo-template"></a>PropertyInfo-mall

PropertyInfo-mallen visar tillgängliga egenskaper för funktionen. Alternativet `label` anger texten som ska visas för användaren. Om `label` inte har angetts visas hyperlänken. Och om hyperlänken är en bild, visas värdet som är kopplat till taggen "Alt". `dateFormat` anger formatet för datumet, och om datum formatet inte anges kommer datumet att återges som en sträng. Alternativet `hyperlinkFormat` återger klicknings bara länkar, vilket innebär att alternativet `email` kan användas för att rendera e-postadresser som går att klicka på.

Innan PropertyInfo-mallen visar egenskaperna för slutanvändaren, kontrollerar den rekursivt att egenskaperna är definierade för den funktionen. Den ignorerar också egenskaper för stil och rubrik. Till exempel visas inte `color`, `size`, `anchor`, `strokeOpacity`och `visibility`. Så när egenskaps Sök vägens kontroll är slutförd i Server delen visar PropertyInfo-mallen innehållet i tabell format.

```javascript
new atlas.data.Feature(new atlas.data.Point([20, -20]), {
    title: 'Template 2 - PropertyInfo',
    createDate: new Date(),
    dateNumber: 1569880860542,
    url: 'https://aka.ms/AzureMapsSamples',
    email: 'info@microsoft.com',
    popupTemplate: {
        content: [{
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
    }
}),

```

### <a name="multiple-content-templates"></a>Flera innehållsmallar

En funktion kan också visa innehåll med hjälp av en kombination av sträng mal len och PropertyInfo-mallen. I det här fallet återger sträng mal len plats hållare värden på en vit bakgrund.  Dessutom återger PropertyInfo-mallen en hel bredds bild inuti en tabell. Egenskaperna i det här exemplet liknar de egenskaper som vi förklarade i föregående exempel.

```javascript
new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 3 - Multiple content template',
    value1: 1.2345678,
    value2: {
    subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6],
    imageLink: 'https://azuremapscodesamples.azurewebsites.net/common/images/Pike_Market.jpg',
    popupTemplate: {
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
    }
    }),
]);
```

### <a name="points-without-a-defined-template"></a>Punkter utan definierad mall

När popup-mallen inte är definierad som en strängkonstant, en PropertyInfo-mall eller en kombination av båda, används standardinställningarna. När `title` och `description` är de enda tilldelade egenskaperna, visar popup-mallen en vit bakgrund, en stängnings knapp i det övre högra hörnet. Och på små och medel stora skärmar visas en pil längst ned. Standardinställningarna visas i en tabell för andra egenskaper än `title` och `description`. Även om du återgår till standardinställningarna kan popup-mallen fortfarande ändras program mässigt. Användare kan till exempel inaktivera identifiering av hyperlänkar och standardinställningarna gäller fortfarande för andra egenskaper.

Klicka på punkterna på kartan i CodePen. Det finns en punkt på kartan för var och en av följande popup-mallar: String-mall, PropertyInfo-mall och flera innehållsmall. Det finns också tre punkter för att visa hur mallar återges med hjälp av standardinställningarna.

<br/>

<iframe height='500' scrolling='no' title='PopupTemplates' src='//codepen.io/azuremaps/embed/dyovrzL/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se Pen- <a href='https://codepen.io/azuremaps/pen/dyovrzL/'>PopupTemplatesen</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reuse-popup-template"></a>Återanvänd popup-mall

På samma sätt som när du återanvänder popup-fönstret kan du återanvända popup-mallar. Den här metoden är användbar när du bara vill visa en popup-mall i taget, för flera punkter. Genom att använda popup-mallen, minskas antalet DOM-element som skapats av programmet, vilket förbättrar programmets prestanda. I följande exempel används samma popup-mall för tre punkter. Om du klickar på någon av dem visas en popup med innehållet för den punkt funktionen.

<br/>

<iframe height='500' scrolling='no' title='ReusePopupTemplate' src='//codepen.io/azuremaps/embed/WNvjxGw/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se Pen- <a href='https://codepen.io/azuremaps/pen/WNvjxGw/'>ReusePopupTemplateen</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="popup-events"></a>Popup-händelser

Popup-fönster kan öppnas, stängas och dras. Popup-klassen innehåller händelser som hjälper utvecklare att reagera på dessa händelser. I följande exempel visas vilka händelser som utlöses när användaren öppnar, stänger eller drar popup-fönstret. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Popup-händelser" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se popup- <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>händelser</a> för penna genom att Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Motta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

Se följande fantastiska artiklar för fullständiga kod exempel:

> [!div class="nextstepaction"]
> [Lägg till ett symbol lager](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Lägg till en HTML-markör](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Lägg till ett linje lager](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Lägg till ett polygon-lager](map-add-shape.md)
