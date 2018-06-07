---
title: Lägg till anpassade html i Azure Maps | Microsoft Docs
description: Hur du lägger till anpassade html en Javascript-karta
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 3ee1f394536061cecaa5d775650149b30098560e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34600229"
---
# <a name="add-custom-html-to-the-map"></a>Lägg till anpassade HTML på kartan

Den här artikeln visar hur du lägger till en anpassad HTML som en bildfil på kartan. 

## <a name="understand-the-code"></a>Förstå koden

<iframe height='466' scrolling='no' title='Lägg till anpassade html på en karta - png' src='//codepen.io/azuremaps/embed/MVoeVw/?height=466&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>lägga till anpassade html till en karta - png</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Första kodblocket skapar ett Kartobjekt i koden ovan. Du kan se [skapa en karta](./map-create.md) anvisningar.

Andra kodblocket skapar ett HTML-element från en avbildning.

Senaste kodblocket använder [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addhtml) funktion i kartan klassen för att lägga till avbildningen till den angivna positionen för kartan.

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln: 
* [karta](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addhtml)
    
Att lägga till i din maps mer kodexempel finns i följande artiklar: 
* [Visa sökresultat](./map-search-location.md)
* [Hämta information från en koordinat](./map-get-information-from-coordinate.md)

