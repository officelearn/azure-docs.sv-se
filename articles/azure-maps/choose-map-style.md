---
title: Kart stils funktioner | Microsoft Azure Maps
description: I den här artikeln får du lära dig mer om utförande av funktioner som är tillgängliga i Microsoft Azure Maps-webbsdk.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: b92e03c4e5346dd39eaba84cfeeedb93e418678c
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911800"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Välj en kart stil i Azure Maps

Många av de [kart format som stöds i Azure Maps](./supported-map-styles.md) finns i webb-SDK: n. Den här artikeln visar hur du använder de formatmallar som är relaterade till att ange ett format för kart belastning, ange en ny stil och använda format väljar kontrollen.

## <a name="set-style-on-map-load"></a>Ställ in stil på kart inläsning

I följande kod anges `style` alternativ för kartan till `grayscale_dark` vid initiering.

<br/>

<iframe height='500' scrolling='no' title='Ange formatet på kart inläsning' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan som <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>anger formatet på kart inläsning</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="update-the-style"></a>Uppdatera formatet

I följande kod, efter att en kart instans har lästs in, uppdateras kart formatet från `road` till `satellite` med hjälp av kartans [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) -funktion.

<br/>

<iframe height='500' scrolling='no' title='Uppdaterar formatet' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan som <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>uppdaterar formatet</a> genom att Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-the-style-picker"></a>Lägg till format väljare

Följande kod lägger till en [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) till kartan så att användaren enkelt kan växla mellan olika kart format. 

<br/>

<iframe height='500' scrolling='no' title='Lägga till format väljare' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan genom <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>att lägga till format väljaren genom att</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Som standard visar format väljar kontrollen alla formatmallar som är tillgängliga när du använder S0-pris nivån för Azure Maps som standard. Om du vill minska antalet formatmallar i den här listan skickar du en matris med de format som du vill ska visas i listan till alternativet `mapStyle` i stil väljaren. Om du använder S1 och vill visa alla tillgängliga stilar, anger du alternativet `mapStyles` för format väljaren som ska `"all"`.

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Lägg till kontroller i dina kartor:

> [!div class="nextstepaction"]
> [Lägg till kart kontroller](map-add-controls.md)

> [!div class="nextstepaction"]
> [Lägg till en PIN-kod](map-add-pin.md)
