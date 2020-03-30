---
title: Ändra stil på kartan i Azure Maps | Microsoft Azure Maps
description: I den här artikeln får du lära dig mer om formatrelaterade funktioner som är tillgängliga i Microsoft Azure Maps web SDK.
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: b1c5d9b5cd2b6b9bfecf8a0af79699061003eec1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335692"
---
# <a name="change-the-style-of-the-map"></a>Ändra formatet för kartan

Kartan stöder flera olika [formatalternativ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) som kan ställas in när kartan `setStyle` initieras eller senare med hjälp av kartfunktionen. Den här artikeln visar hur du använder dessa formatalternativ för att anpassa kartornas utseende. Lär dig att ställa in en stil när du läser in en karta och lär dig att ställa in en ny kartstil med hjälp av stilväljaren.

## <a name="set-the-style-options"></a>Ange formatalternativ 

Formatalternativ kan skickas till kartan när den initieras eller `setStyle` uppdateras senare med hjälp av kartfunktionen.

```javascript
//Set the style options when creating the map.
var map = new atlas.Map('map', {
    renderWorldCopies: false,
    showBuildingModels: true

    //Additional map options.
};

//Update the style options at anytime using setStyle function.
map.setStyle({
    renderWorldCopies: true,
    showBuildingModels: false
});
```

Följande verktyg visar hur de olika formatalternativen ändrar hur kartan återges. Om du vill se 3D-byggnaderna zoomar du in nära en större stad. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Alternativ för kartformat" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se alternativen för <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>pennkartan</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>från Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="choose-a-base-map-style"></a>Välj ett baskartformat

Ett av de vanligaste alternativen för kartformat används för att ändra stilen på baskartan som är formaterad. Många av [kartformaten som stöds i Azure Maps](supported-map-styles.md) är tillgängliga i Web SDK. 

### <a name="set-base-map-style-on-map-load"></a>Ange baskartformat på kartbelastning


Kartformatet kan anges när kartan initieras `style` genom att ange alternativet. I följande kod `style` är alternativet för kartan `grayscale_dark` inställt på vid initiering.

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additiona map options
);
```

<br/>

<iframe height='500' scrolling='no' title='Ställa in formatet på kartbelastningen' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Inställning stilen på kartbelastning</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="update-the-base-map-style"></a>Uppdatera baskartningsformatet

 Kartformatet `setStyle` kan uppdateras med hjälp `style` av funktionen och ställa in alternativet till önskad kartstil.

```javascript
map.setStyle({ style: 'satellite' });
```

I följande kod, efter att en kartförekomst har `road` lästs in, uppdateras kartformatet från till att `satellite` använda funktionen [setStyle.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setstyle-styleoptions-)

<br/>

<iframe height='500' scrolling='no' title='Uppdatera formatet' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>Uppdatera formatet</a> med<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="add-the-style-picker"></a>Lägga till formatväljaren

Stilväljaren ger en lättanvänd knapp med utfällbar panel som kan användas av slutanvändaren för att ändra kartstilen. Formatväljaren har två olika layoutalternativ. Som standard använder formatväljaren layouten `icons` och visar alla kartformat som en vågrät rad med ikoner. 

<center>

![Ikonlayout för formatväljare](media/choose-map-style/style-picker-icon-layout.png)</center>

Det andra layoutalternativet anropas `list` och visar en rullningsbar lista med kartformat.  

<center>

![Layout för formatväljare](media/choose-map-style/style-picker-list-layout.png)</center>


Följande kod visar hur du skapar en förekomst av formatväljaren och lägger till den i det övre högra hörnet på kartan. Formatväljaren är inställd på att ha ett mörkt format och visa några markerade kartformat med listlagret.

```javascript
map.controls.add(new atlas.control.StyleControl({
    mapStyles: ['road', 'night', 'grayscale_dark', 'grayscale_light'],
    layout: 'list',
    style: 'dark'
}), {
    position: 'top-right'
}); 
```

Följande kod lägger till en stilväljare kontroll med dess standardinställningar till kartan, så att användaren enkelt kan växla mellan de olika kartformaten. Växla kartformatet med hjälp av kartans stilkontroll nära det övre högra hörnet.

<br/>

<iframe height='500' scrolling='no' title='Lägga till formatväljaren' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Lägga till stilväljaren</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>av Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> När du använder prisnivån S0 i Azure Maps listar formatväljarens kontroll som standard alla tillgängliga format. Om du vill minska antalet format i den här listan skickar du en matris `mapStyle` med de format som du vill ska visas i listan till alternativet för formatväljaren. Om du använder S1 och vill visa alla `mapStyles` tillgängliga format anger du `"all"`alternativet för formatväljaren till .

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [StyleOptions (olika)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl (format)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [StyleControlOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.stylecontroloptions)

Lägg till kontroller på dina kartor:

> [!div class="nextstepaction"]
> [Lägg till kartkontroller](map-add-controls.md)

> [!div class="nextstepaction"]
> [Lägg till en nål](map-add-pin.md)
