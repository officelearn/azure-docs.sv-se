---
title: Skapar ett tillgängliga program med Azure Maps | Microsoft Docs
description: Hur du skapar ett tillgängliga program med Azure Maps
services: azure-maps
keywords: ''
author: chgennar
ms.author: chgennar
ms.date: 05/18/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.openlocfilehash: 3fe0ba47e2e3529352ca8386dc7531a96a2689af
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746220"
---
# <a name="building-an-accessible-application"></a>Skapar ett tillgängliga program

Den här artikeln visar hur du skapar en karta-program som kan användas av en skärmläsare.

## <a name="understand-the-code"></a>Förstå koden

<iframe height='500' scrolling='no' title='Göra ett tillgängliga program' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>göra ett tillgängligt program</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Kartan är färdiga med vissa hjälpmedelsfunktioner. En användare kan navigera på kartan med hjälp av tangentbordet och om en skärmläsare körs kartan meddelar användaren om ändringar i sitt tillstånd. Till exempel meddelas användaren på kartan nya latitud, longitud, Zooma och ort när kartan panoreras eller zoomar. Ytterligare information som placeras på den grundläggande kartan bör ha motsvarande textinformation för användare av skärmläsare. Med hjälp av [popup-fönstret](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) är ett sätt att åstadkomma detta. I exemplet ovan search läggs ett popup-fönster med textinformation på kartan för varje PIN-kod som är placerad på kartan. Med hjälp av den [popup-](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) bifoga metoden kan popup-fönstret ska vara synlig för en skärmläsare utan att visa visuellt popup-fönstret på kartan.

## <a name="next-steps"></a>Nästa steg

Läs mer om Popup-klassen och dess metoder som används i den här artikeln:

* [Popup-fönstret](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
    * [Koppla](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#attach)
    * [ta bort](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#remove)
    * [Öppna](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open)
    * [Stäng](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#close)

Kolla in våra [exempel teckentabellen](http://aka.ms/AzureMapsSamples) för fler mappning scenarier.
