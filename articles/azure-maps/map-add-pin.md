---
title: Lägg till ett symbol lager i Azure Maps | Microsoft Docs
description: Så här lägger du till symboler i Java Script Map
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 3bce4922a33648f5d7c0d211dba126f35603239b
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68849287"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Lägga till ett symbol lager till en karta

Den här artikeln visar hur du kan återge punkt data från en data källa som ett symbol lager på en karta. Symbol lager återges med hjälp av WebGL och stöder mycket större uppsättningar av punkter än HTML-markörer, men stöder inte traditionella CSS-och HTML-element för formatering.  

> [!TIP]
> Symbol lager som standard återger koordinaterna för alla Geometries i en data källa. För att begränsa lagret så att det bara återger punkt geometri funktioner anger `filter` du egenskapen för lagret till `['==', ['geometry-type'], 'Point']` eller `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` om du även vill inkludera Multipoint-funktioner.

## <a name="add-a-symbol-layer"></a>Lägga till ett symbolskikt

<iframe height='500' scrolling='no' title='Byt PIN-sökväg' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>Fäst punkten</a> för Penn växlar genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Det första kod blocket ovan skapar ett kart objekt. Du kan se [skapa en karta](./map-create.md) för instruktioner.

I det andra kodblock skapas ett data käll objekt med hjälp av klassen [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) . En geometrisk JSON- [funktion](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature) som innehåller en [punkt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) geometri omsluts av [form](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) klassen så att den blir lättare att uppdatera, sedan skapas och läggs till i data källan.

Det tredje blocket kod skapar en [händelse lyssnare](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) och uppdaterar Punktens koordinater vid mus klickning med hjälp av form klassen [setCoordinates](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) metod.

Ett [symbol lager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) använder text eller ikoner för att återge punktbaserade data i [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) som symboler på kartan.  Data källan, klicka på händelse lyssnare och symbol skiktet skapas och läggs till i kartan i `ready` funktionen [Event Listener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) för att se till att punkten visas efter att kartan lästs in och är redo att nås.

> [!TIP]
> Som standard optimerar symbol lager åter givningen av symboler genom att dölja symboler som överlappar varandra för prestanda. När du zoomar in dolda symboler blir de synliga. Om du vill inaktivera den här funktionen och återge alla symboler hela tiden, `allowOverlap` anger du egenskapen `iconOptions` för alternativen `true`till.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Lägga till en anpassad ikon i ett symbol lager

Symbol lager återges med WebGL. Alla resurser, till exempel ikon bilder, måste läsas in i WebGL-kontexten. Det här exemplet visar hur du lägger till en anpassad ikon i kart resurserna och sedan använder den för att återge punkt data med en anpassad symbol på kartan. För symbol skiktets egenskap krävs att ett uttryck anges. `textField` I det här fallet vill vi återge temperatur egenskapen men eftersom den är ett tal måste den konverteras till en sträng. Dessutom vill vi lägga till "°F". Ett uttryck kan användas för detta. `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Ikon för anpassad symbol bild' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>bild ikonen för den anpassade symbolen</a> för penna genom<a href='https://codepen.io/azuremaps'>@azuremaps</a>att Azure Maps () på <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Azure Maps Web SDK innehåller flera anpassningsbara bildmallar som du kan använda med symbol lagret. Mer information finns i dokumentet om [att använda bilder för mallar](how-to-use-image-templates-web-sdk.md) .

## <a name="customize-a-symbol-layer"></a>Anpassa ett symbol lager 

Symbol skiktet har många tillgängliga format alternativ. Här är ett verktyg för att testa dessa olika format alternativ.

<br/>

<iframe height='700' scrolling='no' title='Symbol lager alternativ' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se alternativen för Penn <a href='https://codepen.io/azuremaps/pen/PxVXje/'>symbol lager</a> efter Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TexTOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

Se följande artiklar för fler kod exempel som du kan lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Lägg till en popup](map-add-popup.md)

> [!div class="nextstepaction"]
> [Använd data drivna format uttryck](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Använda bildmallar](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Lägg till en form](map-add-shape.md)

> [!div class="nextstepaction"]
> [Lägg till ett bubbel-lager](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Lägg till HTML-tillverkare](map-add-bubble-layer.md)
