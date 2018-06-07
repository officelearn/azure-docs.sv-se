---
title: Hur du skapar tillgänglig med Azure Maps | Microsoft Docs
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
ms.openlocfilehash: 2523287669628b8c58028cae9887743c20b6bc5e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655380"
---
# <a name="building-an-accessible-application"></a>Skapa ett program som är tillgänglig

Den här artikeln visar hur du skapar en karta program som kan användas av en skärmläsare.

## <a name="understand-the-code"></a>Förstå koden

<iframe height='500' scrolling='no' title='Ingen tillgänglig koppling' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>ansöka om tillgänglig</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Kartan är färdiga med vissa hjälpmedelsfunktioner. En användare kan navigera på kartan med hjälp av tangentbordet och om en skärmläsare körs kartan meddelar användaren om ändringar i dess tillstånd. Till exempel meddelas användaren på kartan nya, long, Zooma och ort när kartan panoreras eller zoomat. Ytterligare information som är placerad på grundläggande kartan ska ha motsvarande textinformation för användare med skärmläsare. Med hjälp av [Popup](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest) är ett sätt att åstadkomma detta. I exemplet ovan Sök läggs ett popup-fönster med textinformation på kartan för varje PIN-kod som är placerad på kartan. Med hjälp av den [popup-](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest) bifoga metoden gör den popup ska vara synlig för skärmläsare utan att visuellt visa popup-fönstret på kartan.

## <a name="next-steps"></a>Nästa steg

Läs mer om Popup-klassen och dess metoder som används i den här artikeln:

* [Popup-fönster](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)
    * [Koppla](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#attach)
    * [Ta bort](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#remove)
    * [Öppna](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open)
    * [Stäng](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#close)

Kolla in våra [exempel teckentabellen](http://aka.ms/AzureMapsSamples) för scenarier med flera mappning.
