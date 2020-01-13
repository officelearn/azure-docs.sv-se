---
title: Lägg till en popup-meny till en plats på en karta | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du lägger till ett popup-fönster till en punkt med hjälp av Microsoft Azure Maps-webbsdk.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 79bafb331cb7ad38ea7cad9e510b22886b647764
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911152"
---
# <a name="add-a-popup-to-the-map"></a>Lägg till en popup till kartan

Den här artikeln visar hur du lägger till en popup-meny till en plats på en karta.

## <a name="understand-the-code"></a>Förstå koden

Följande kod lägger till en punkt funktion som har `name` och `description` egenskaper till kartan med ett symbol lager. En instans av [klassen pop](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) skapas men visas inte. Mus händelser läggs till i symbol lagret för att utlösa öppning och stängning när musen hovrar över och ut från symbol markören. När markör symbolen har hovrat, uppdateras popup `name`-fönstrets `position`-egenskap med markörernas position och alternativet `content` uppdateras med HTML-kod och `description` egenskaperna för punkt funktionen har hovras. Popup-fönstret visas sedan på kartan med dess `open`-funktion.

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

<iframe height='500' scrolling='no' title='Lägg till ett popup med Azure Maps' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Lägg till ett popup med Azure Maps</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Återanvända en popup med flera punkter

När du har ett stort antal punkter och bara vill visa en popup i taget, är det bästa sättet att skapa ett popup-fönster och återanvända det i stället för att skapa en popup för varje punkt funktion. Genom att återanvända popup-fönstret minskar antalet DOM-element som skapats av programmet avsevärt vilket kan ge bättre prestanda. I följande exempel skapas tre-punkt-funktioner. Om du klickar på någon av dem visas en popup med innehållet för den punkt funktionen.

<br/>

<iframe height='500' scrolling='no' title='Återanvända popup med flera PIN-bara' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se popup-fönstret för att <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>använda pennan med flera stift</a> genom att Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-a-popup"></a>Anpassa en popup

Som standard har popup-fönstret en vit bakgrund, en pekare längst ned och en stängnings knapp i det övre högra hörnet. Följande exempel ändrar bakgrunds färgen till svart med alternativet `fillColor` i popup-fönstret. Knappen Stäng tas bort genom att ange alternativet `shoCloseButton` till falskt. HTML-innehållet i popup-fönstret använder utfyllda 10 pixlar från kanten på popup-fönstret och texten blir vit så att den visas snyggt på den svarta bakgrunden.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Anpassad popup" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se popup-fönstret för <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>anpassad</a> penna genom att Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="popup-events"></a>Popup-händelser

Popup-fönster kan öppnas, stängas och dras. Popup-klassen innehåller händelser för hjälp utvecklare som reagerar på dessa åtgärder. I följande exempel markeras vilka händelser som ska utlösas när du öppnar, stänger eller drar i popup-fönstret. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Popup-händelser" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se popup- <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>händelser</a> för penna genom att Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

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