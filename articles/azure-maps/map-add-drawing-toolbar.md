---
title: Lägga till ett verktygsfält för ritning på en karta | Microsoft Azure Maps
description: Så här lägger du till ett verktygsfält för teckning i en karta med Azure Maps Web SDK
author: philmea
ms.author: philmea
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: bebf1ddfbca3aec5a551193609381cf3510bc3ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334487"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>Lägga till ett verktygsverktygsverktyg i en karta

I den här artikeln visas hur du använder modulen Ritverktyg och visar verktygsfältet rita på kartan. [Kontrollen DrawingToolbar](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) lägger till ritarmfältet på kartan. Du får lära dig hur du skapar kartor med bara ett och alla ritverktyg och hur du anpassar återgivningen av ritningsformerna i rithanteraren.

## <a name="add-drawing-toolbar"></a>Lägga till verktygsfält för ritning

Följande kod skapar en förekomst av rithanteraren och visar verktygsfältet på kartan.

```javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

Nedan är det fullständiga kodexemplet för funktionen ovan:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Lägga till verktygsfält för ritning" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>verktygsfältet Lägg till</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>ritning av Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="limit-displayed-toolbar-options"></a>Begränsa alternativ i verktygsfältet

Följande kod skapar en förekomst av rithanteraren och visar verktygsfältet med bara ett polygonritningsverktyg på kartan. 

```javascript
//Create an instance of the drawing manager and display the drawing toolbar with polygon drawing tool.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'light',
            buttons: ["draw-polygon"]
        })
    });
```

Nedan är det fullständiga kodexemplet för funktionen ovan:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Lägga till ett polygonritningsverktyg" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se pennan <a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>Lägg till ett polygonritningsverktyg</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="change-drawing-rendering-style"></a>Ändra återgivningsformat för ritning

Formatet på de former som ritas kan anpassas genom att hämta de underliggande lagren i ritningshanteraren med hjälp av `drawingManager.getLayers()` funktionen och sedan ange alternativ på de enskilda lagren. Draghandtagen som visas för koordinater när du redigerar en form är HTML-markörer. Stilen på draghandtagen kan anpassas genom att `dragHandleStyle` `secondaryDragHandleStyle` skicka HTML-marköralternativ till ritningshanterarens och alternativ.  

Följande kod hämtar renderingslagren från rithanteraren och ändrar deras alternativ för att ändra återgivningsformat för ritning. I det här fallet återges punkter med en blå markörikon. Linjerna är röda och fyra pixlar breda. Polygoner kommer att ha en grön fyllningsfärg och en orange kontur. Sedan ändras formaten för draghandtagen till fyrkantiga ikoner. 

```javascript
//Get rendering layers of drawing manager.
var layers = drawingManager.getLayers();

//Change the icon rendered for points.
layers.pointLayer.setOptions({
    iconOptions: {
        image: 'marker-blue'
    }
});

//Change the color and width of lines.
layers.lineLayer.setOptions({
    strokeColor: 'red',
    strokeWidth: 4
});

//Change fill color of polygons.
layers.polygonLayer.setOptions({
    fillColor: 'green'
});

//Change the color of polygon outlines.
layers.polygonOutlineLayer.setOptions({
    strokeColor: 'orange'
});

//Update the style of the drag handles that appear when editting.
drawingManager.setOptions({
    //Primary drag handle that represents coordinates in the shape.
    dragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="15" height="15" viewBox="0 0 15 15" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="15" height="15" style="stroke:black;fill:white;stroke-width:4px;"/></svg>',
        draggable: true
    },

    //Secondary drag hanle that represents mid-point coordinates that users can grab to add new cooridnates in the middle of segments.
    secondaryDragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="10" height="10" viewBox="0 0 10 10" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="10" height="10" style="stroke:white;fill:black;stroke-width:4px;"/></svg>',
        draggable: true
    }
});  
```

Nedan är det fullständiga kodexemplet för funktionen ovan:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Ändra återgivningsformat för ritning" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>ritåtergivningsstilen pen</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>change av Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder ytterligare funktioner i ritverktygsmodulen:

> [!div class="nextstepaction"]
> [Hämta formdata](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Reagera på rithändelser](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Interaktionstyper och kortkommandon](drawing-tools-interactions-keyboard-shortcuts.md)

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Verktygsfältet Rita](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Rithanterare](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)
