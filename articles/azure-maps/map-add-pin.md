---
title: Lägga till en PIN-kod med Azure Maps | Microsoft Docs
description: Hur du lägger till en PIN-kod till en Javascript-karta
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 094abe08c0c88c7561185675ceb8529be2c87a0a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294656"
---
# <a name="add-pins-to-the-map"></a>Lägg till PIN-koder på kartan

Den här artikeln visar hur du lägger till en PIN-kod till en karta.  

## <a name="understand-the-code"></a>Förstå koden

<iframe height='500' scrolling='no' title='Lägga till en PIN-kod på en karta' src='//codepen.io/azuremaps/embed/ZrVpEa/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/ZrVpEa/'>lägga till en PIN-kod på en karta</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Första kodblocket skapar ett Kartobjekt i koden ovan. Du kan se [skapa en karta](./map-create.md) anvisningar.

I det andra kodblocket, en PIN-kod skapas och läggs till kartan. En PIN-kod är en [funktionen](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest) av [punkt](https://docs.microsoft.com/javascript/api/azure-maps-javascript/point?view=azure-iot-typescript-latest) med [PinProperties](https://docs.microsoft.com/javascript/api/azure-maps-javascript/pinproperties?view=azure-iot-typescript-latest) som egenskapen funktionen. Använd `new atlas.data.Feature(new atlas.data.Point())` att skapa en PIN-kod och dess egenskaper. Ett lager för PIN-kod är en matris av PIN-koder. Använd [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins) funktion i kartan klassen för att lägga till ett lager för PIN-kod på kartan och definiera egenskaperna för pin-lagret. Se egenskaperna för ett lager för PIN-kod vid [PinLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/pinlayeroptions?view=azure-iot-typescript-latest). 

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln: 
* [karta](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)
    
Att lägga till i din maps mer kodexempel finns i följande artiklar: 
* [Lägg till ett popup-fönster](./map-add-popup.md)
* [Lägga till en form](./map-add-shape.md)

