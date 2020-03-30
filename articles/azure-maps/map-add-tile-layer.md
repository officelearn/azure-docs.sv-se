---
title: Lägga till ett panellager på en karta | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du överlagrar ett panellager på en karta med hjälp av Microsoft Azure Maps Web SDK. Med panellager kan du återge bilder på en karta.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 61d7a11df499e6b740adb45968721b6a9bb1af22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988608"
---
# <a name="add-a-tile-layer-to-a-map"></a>Lägga till ett panelskikt till en karta

I den här artikeln visas hur du överlagrar ett panellager på kartan. Med panellager kan du lägga över avbildningar ovanpå Azure Maps-baskartpaneler. Mer information om Azure Maps plattsättningssystem finns i [Zooma nivåer och panelrutnät](zoom-levels-and-tile-grid.md).

Ett panellager läses in i paneler från en server. Dessa bilder kan antingen göras i föråtergivning eller återges dynamiskt. Förrederade bilder lagras som alla andra bilder på en server med hjälp av en namngivningskonvention som panellagret förstår. Dynamiskt renderade bilder använder en tjänst för att läsa in bilderna nära realtid. Det finns tre olika namngivningskonventioner för paneltjänster som stöds av klassen Azure [Maps TileLayer:](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) 

* X, Y, Zoom notation - X är kolumnen, Y är raden position kakel i kakel rutnätet, och Zoom notation ett värde baserat på zoomnivå.
* Quadkey notation - Kombinerar x, y och zooma information till ett enda strängvärde. Det här strängvärdet blir en unik identifierare för en enda panel.
* Begränsningsram - Ange en bild i formatet `{west},{south},{east},{north}`Markeringsramkoordinater: . Det här formatet används ofta av [WMS (Web Mapping Services).](https://www.opengeospatial.org/standards/wms)

> [!TIP]
> En [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) är ett bra sätt att visualisera stora datauppsättningar på kartan. Inte bara kan ett panellager genereras från en bild, vektordata kan också återges som ett panellager också. Genom att återge vektordata som ett panellager behöver kartkontrollen bara läsa in panelerna som är mindre i filstorlek än de vektordata som de representerar. Den här tekniken används ofta för att återge miljontals rader med data på kartan.

Panel-URL:en som skickas till ett panellager måste vara en http- eller https-URL till en TileJSON-resurs eller en url-mall för panelen som använder följande parametrar: 

* `{x}`- X-placering på plattan. Också `{y}` behov `{z}`och .
* `{y}`- Y-position på plattan. Också `{x}` behov `{z}`och .
* `{z}`- Zooma nivå av kakel. Också `{x}` behov `{y}`och .
* `{quadkey}`- Tile quadkey-identifierare baserat på namngivningskonventionen för Bing Maps-panelsystemet.
* `{bbox-epsg-3857}`- En markeringsramssträng `{west},{south},{east},{north}` med formatet i EPSG 3857 Spatial Reference System.
* `{subdomain}`- En platshållare för underdomänvärdena, `subdomain` om det anges att testamentet ska läggas till.

## <a name="add-a-tile-layer"></a>Lägga till ett panelskikt

 Det här exemplet visar hur du skapar ett panellager som pekar på en uppsättning paneler. I det här exemplet används x-, y-, zooma plattsättningssystemet. Källan till detta kakel lager är ett väder radar overlay från [Iowa Environmental Mesonet i Iowa State University](https://mesonet.agron.iastate.edu/ogc/). När du tittar på radardata, helst användare skulle tydligt se etiketterna i städer när de navigerar på kartan. Detta kan implementeras genom att infoga panellagret under `labels` lagret.

```javascript
//Create a tile layer and add it to the map below the label layer.
//Weather radar tiles from Iowa Environmental Mesonet of Iowa State University.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

Nedan visas det fullständiga kodexemplet för ovanstående funktioner.

<br/>

<iframe height='500' scrolling='no' title='Panellager med X, Y och Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>Pennpanellagret med X, Y och</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>Z av Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-tile-layer"></a>Anpassa ett panellager

Klassen kakellager har många formateringsalternativ. Här är ett verktyg för att prova dem.

<br/>

<iframe height='700' scrolling='no' title='Alternativ för lager med panel' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se alternativen för <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>pennpanellager</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>av Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [KakelLager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

Se följande artiklar för fler kodexempel att lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Lägga till ett avbildningsskikt](./map-add-image-layer.md)
