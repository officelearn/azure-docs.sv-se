---
title: Lägg till ett panel lager i Azure Maps | Microsoft Docs
description: Så här lägger du till ett panel lager i Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 3f047ec1aced55038384cbe29bd3a4b8a948dce9
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976456"
---
# <a name="add-a-tile-layer-to-a-map"></a>Lägga till ett panel lager till en karta

Den här artikeln visar hur du kan täcka ett panel lager på kartan. Med panel lager kan du placera bilder ovanpå Azure Maps bas kart paneler. Mer information om Azure Maps displacerings systemet finns i dokumentationen för [zoomnings nivåer och Brick rutnät](zoom-levels-and-tile-grid.md) .

Ett panel lager läses in i paneler från en server. Dessa avbildningar kan antingen förrenderas och lagras på samma sätt som andra bilder på en server med hjälp av en namngivnings konvention som panel lagret förstår, eller en dynamisk tjänst som genererar bilderna i farten. Det finns tre olika namngivnings konventioner för panel tjänster som stöds av Azure Maps [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) -klassen: 

* X-, Y-, zoomnings-och zoomnings nivå, x är kolumnen och Y är panelens rad position i panel rutnätet.
* Quadkey notation – kombination x, y, zoomnings information till ett enda sträng värde som är en unik identifierare för en panel.
* Koordinater för avgränsnings rutor kan användas för att ange en bild i formatet `{west},{south},{east},{north}` som ofta används av [webb mappnings tjänster (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> En [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) är ett bra sätt att visualisera stora data uppsättningar på kartan. Ett panel lager kan inte bara genereras från en bild, men vektor data kan även återges som ett panel lager. Genom att återge vektor data som ett panel lager behöver kart kontrollen bara läsa in panelerna som kan vara mycket mindre i fil storlek än de vektor data de representerar. Den här tekniken används av många som behöver rendera miljon tals rader med data på kartan.

Panel-URL: en som skickas till ett panel lager måste vara en HTTP/HTTPS-URL till en TileJSON-resurs eller en panel-URL-mall som använder följande parametrar: 

* `{x}`-X position i rutan. Du måste `{y}` också `{z}`ha och.
* `{y}`-Y position i panelen. Du måste `{x}` också `{z}`ha och.
* `{z}`-Zoomnings nivå i panelen. Du måste `{x}` också `{y}`ha och.
* `{quadkey}`-Panel quadkey identifierare baserat på Bing Maps-panelens system namngivnings konvention.
* `{bbox-epsg-3857}`– En sträng med avgränsnings rutor med formatet `{west},{south},{east},{north}` i EPSG 3857 rums referens system.
* `{subdomain}`– En plats hållare där under domän värden om de anges kommer att läggas till.

## <a name="add-a-tile-layer"></a>Lägga till ett panelskikt

 Det här exemplet visar hur du skapar ett panel lager som pekar på en uppsättning paneler som använder systemet x, y, zoom-inpassning. Källan till det här panel lagret är ett väderleks överlägg av [Iowa-miljön för Mesonet i Iowa State University](https://mesonet.agron.iastate.edu/ogc/). När du visar radar data kan användarna tydligt se etiketter för städer när de navigerar i kartan, vilket kan göras genom att infoga panel lagret under `labels` lagret.

```javascript
//Create a tile layer and add it to the map below the label layer.
//Weather radar tiles from Iowa Environmental Mesonet of Iowa State University.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

Nedan visas det fullständiga kod exemplet för ovanstående funktioner.

<br/>

<iframe height='500' scrolling='no' title='Panel lager med X, Y och Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se Penn <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>panels lagret med X, Y och Z</a> efter Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-tile-layer"></a>Anpassa ett panel lager

Panel skikts klassen har många format alternativ. Här är ett verktyg för att testa dem.

<br/>

<iframe height='700' scrolling='no' title='Alternativ för panel lager' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se alternativ för Penn <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>panels lager</a> efter Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

Se följande artiklar för fler kod exempel som du kan lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Lägg till ett bild lager](./map-add-image-layer.md)
