---
title: Ändra kartans format i Azure Maps | Microsoft Azure Maps
description: I den här artikeln får du lära dig mer om formatmallar som är tillgängliga i Microsoft Azure Maps-webbsdk.
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: b1c5d9b5cd2b6b9bfecf8a0af79699061003eec1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80335692"
---
# <a name="change-the-style-of-the-map"></a>Ändra formatet för kartan

Kartan stöder flera olika [stil alternativ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) som kan ställas in när kartan initieras eller senare med hjälp av Maps- `setStyle` funktionen. Den här artikeln visar hur du använder de här stil alternativen för att anpassa Kartornas utseende. Lär dig hur du anger ett format vid inläsning av en karta och lär dig att ange en ny kart stil med hjälp av format väljar kontrollen.

## <a name="set-the-style-options"></a>Ange format alternativ 

Stil alternativ kan skickas till kartan när den initieras eller uppdateras senare med hjälp av Maps- `setStyle` funktionen.

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

Följande verktyg visar hur de olika stil alternativen ändrar hur kartan återges. Om du vill se 3D-byggnaderna zoomar du in nära en större stad. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Alternativ för kart stil" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>stil alternativen</a> för pen-kartor genom Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="choose-a-base-map-style"></a>Välj en grundläggande kart stil

Ett av de vanligaste alternativen för kart stil används för att ändra format mal len för bas kartan som är formaterad. Många av de [kart format som stöds i Azure Maps](supported-map-styles.md) finns i webb-SDK: n. 

### <a name="set-base-map-style-on-map-load"></a>Ange bas kart stil på kart inläsning


Du kan ange kart stil när kartan initieras genom att ställa in `style` alternativet. I följande kod `style` är alternativet för kartan inställt `grayscale_dark` på vid initiering.

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additiona map options
);
```

<br/>

<iframe height='500' scrolling='no' title='Ange formatet på kart inläsning' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan som <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>anger formatet på kart inläsning</a> av Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="update-the-base-map-style"></a>Uppdatera bas kart formatet

 Map-formatet kan uppdateras med hjälp av `setStyle` funktionen och ställer in `style` alternativet på önskad kart stil.

```javascript
map.setStyle({ style: 'satellite' });
```

I följande kod, efter att en kart instans har lästs in, uppdateras kart formatet från `road` till `satellite` med hjälp av funktionen [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setstyle-styleoptions-) .

<br/>

<iframe height='500' scrolling='no' title='Uppdaterar formatet' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan som <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>uppdaterar formatet</a> genom att Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="add-the-style-picker"></a>Lägg till format väljare

Format väljar kontrollen är en lätt att använda-knapp med utfällbar panel som kan användas av slutanvändaren för att ändra kart formatet. Format väljaren har två olika layoutalternativ. Som standard använder format väljaren `icons` layouten och visar alla kart format som en vågrät rad med ikoner. 

<center>

![Ikon layout för format väljare](media/choose-map-style/style-picker-icon-layout.png)</center>

Det andra alternativet för layout anropas `list` och visar en rullnings bar lista över kart format.  

<center>

![Layout för format väljare](media/choose-map-style/style-picker-list-layout.png)</center>


Följande kod visar hur du skapar en instans av format väljar kontrollen och lägger till den i det övre högra hörnet på kartan. Format väljaren är inställd på att ha en mörk stil och visar ett valt par kart format med hjälp av list skiktet.

```javascript
map.controls.add(new atlas.control.StyleControl({
    mapStyles: ['road', 'night', 'grayscale_dark', 'grayscale_light'],
    layout: 'list',
    style: 'dark'
}), {
    position: 'top-right'
}); 
```

Följande kod lägger till en stil väljar kontroll med standardinställningarna till kartan, så att användaren enkelt kan byta mellan olika kart format. Växla kopplings formatet genom att använda kart stils kontrollen nära det övre högra hörnet.

<br/>

<iframe height='500' scrolling='no' title='Lägga till format väljare' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan genom <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>att lägga till format väljaren</a> per Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> När du använder S0 pris nivå för Azure Maps visas som standard alla tillgängliga formatmallar i format väljaren. Om du vill minska antalet formatmallar i den här listan skickar du en matris med de format som du vill ska visas i listan till `mapStyle` alternativet för format väljaren. Om du använder S1 och vill visa alla tillgängliga stilar väljer du `mapStyles` alternativet för format väljare till `"all"` .

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [StyleControlOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.stylecontroloptions)

Lägg till kontroller i dina kartor:

> [!div class="nextstepaction"]
> [Lägg till kartkontroller](map-add-controls.md)

> [!div class="nextstepaction"]
> [Lägg till en nål](map-add-pin.md)
