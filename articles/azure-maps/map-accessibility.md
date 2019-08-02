---
title: Göra ett tillgängligt program med Azure Maps | Microsoft Docs
description: Så här skapar du ett tillgängligt program med hjälp av Azure Maps
services: azure-maps
author: chgennar
ms.author: chgennar
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
manager: timlt
ms.openlocfilehash: 8865027c895be09150797608e43184f1fdefb267
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638782"
---
# <a name="building-an-accessible-application"></a>Skapa ett tillgängligt program

Den här artikeln visar hur du skapar ett kart program som kan användas av en skärm läsare.

## <a name="understand-the-code"></a>Förstå koden

<iframe height='500' scrolling='no' title='Gör ett tillgängligt program' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>gör ett tillgängligt program</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) i <a href='https://codepen.io'>CodePen</a>.
</iframe>

Kartan är förbyggd med hjälpmedels funktionerna. Användare kan navigera i kartan med tangent bordet. Om en skärm läsare körs meddelar kartan användaren om ändringar i sitt tillstånd.
Användare meddelas till exempel om kart ändringar när kartan är panorerad eller zoomad. All ytterligare information som placeras på bas kartan bör ha motsvarande text information för skärm läsar användare.

Att använda [popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) är ett sätt att åstadkomma detta. I Sök exemplet ovan läggs en popup med text information till kartan för varje PIN-kod som placeras på kartan. Med hjälp av [popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) -metoden [Attach](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#attach) kan popup-fönstret ses av en skärm läsare utan att visuellt Visa popup-fönstret på kartan.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om klassen pop och dess metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Motta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Se fler kod exempel:

> [!div class="nextstepaction"]
> [Kod exempel sida](https://aka.ms/AzureMapsSamples)
