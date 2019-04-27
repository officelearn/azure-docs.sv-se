---
title: Skapar ett tillgängliga program med Azure Maps | Microsoft Docs
description: Hur du skapar ett tillgängliga program med Azure Maps
services: azure-maps
keywords: ''
author: chgennar
ms.author: chgennar
ms.date: 09/17/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.openlocfilehash: ef948b4dca3d3800a81ac52f3a73beee2558d21c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769710"
---
# <a name="building-an-accessible-application"></a>Skapar ett tillgängliga program

Den här artikeln visar hur du skapar en karta-program som kan användas av en skärmläsare.

## <a name="understand-the-code"></a>Förstå koden

<iframe height='500' scrolling='no' title='Göra ett tillgängliga program' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>göra ett tillgängligt program</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Kartan är färdiga med hjälpmedelsfunktioner. Användare kan navigera på kartan med hjälp av tangentbordet. Om en skärmläsare körs kartan meddelar användaren om ändringar i sitt tillstånd.
Till exempel meddelas användarna om ändringar för kartan när kartan panoreras eller zoomar. Ytterligare information som placeras på den grundläggande kartan bör ha motsvarande textinformation för användare av skärmläsare.

Med hjälp av [popup-fönstret](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) är ett sätt att åstadkomma detta. I exemplet ovan search läggs ett popup-fönster med textinformation på kartan för varje PIN-kod som är placerad på kartan. Med hjälp av den [popup-](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) [bifoga](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#attach) metoden kan popup-fönstret ska vara synlig för en skärmläsare utan att visa visuellt popup-fönstret på kartan.

## <a name="next-steps"></a>Nästa steg

Läs mer om Popup-klassen och dess metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Popup-fönstret](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Kolla in fler kodexempel:

> [!div class="nextstepaction"]
> [Exempelsida för kod](https://aka.ms/AzureMapsSamples)
