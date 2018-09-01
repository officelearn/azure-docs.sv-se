---
title: Lägga till anpassade html i Azure Maps | Microsoft Docs
description: Hur du lägger till anpassade html till en Javascript-karta
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 5060839be244f55700b40735e598964a0b7b6709
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382053"
---
# <a name="add-custom-html-to-the-map"></a>Lägga till anpassade HTML på kartan

Den här artikeln visar hur du lägger till en anpassad HTML till exempel en bildfil på kartan. 

## <a name="understand-the-code"></a>Förstå koden

<iframe height='466' scrolling='no' title='Lägg till anpassade html på en karta - png' src='//codepen.io/azuremaps/embed/MVoeVw/?height=466&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>lägga till anpassade html på en karta - png</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapar första kodblocket en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

Andra kodblocket skapar ett HTML-element från en avbildning.

Det sista blocket kod använder [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addhtml) funktion i klassen kartan för att lägga till bilden i den angivna positionen för kartan.

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln: 
* [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addhtml)
    
Flera kodexempel för att lägga till i dina kartor, finns i följande artiklar: 
* [Visa sökresultat](./map-search-location.md)
* [Hämta information från en koordinat](./map-get-information-from-coordinate.md)

