---
title: Så här använder du Kartkontroll för Azure Maps | Microsoft Docs
description: Lär dig hur du använder Azure Maps Kartkontroll klientens Javascript-biblioteket.
author: dsk-2015
ms.author: dkshir
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 619dda44b5ad74ba1dcb62cfb3318687aa6ec4d5
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2018
ms.locfileid: "43340143"
---
# <a name="how-to-use-the-azure-maps-map-control"></a>Så här använder du Kartkontroll för Azure Maps
Kartkontroll klientens Javascript-biblioteket kan du återge kartor och inbäddade Azure Maps-funktioner till din webbplats eller mobila program. 

## <a name="create-a-new-map-in-a-web-page"></a>Skapa en ny karta på en webbsida

Du kan bädda in en karta på en webbsida med hjälp av klientens Javascript-bibliotek för Kartkontrollen.

1. Skapa en ny fil och ge den namnet MapSearch.html.

2. Lägg till Azure Maps formatmall och skript källa referenser till den `<head>` elementet i filen:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
    ```
    
3. För att göra en ny karta i webbläsaren, lägger du till en **#map** refererar till i den `<style>` element.

    ```html
    #map {
                width: 100%;
                height: 100%;
            }
    ``` 
    
4. Definiera ett nytt avsnitt i html-text och skapa ett skript för att initiera kartkontrollen. Använd din egen nyckel för Azure Maps-konto i skriptet. Om du vill skapa ett konto eller hitta din nyckel finns i [så här hanterar du ditt Azure Maps-konto och dina nycklar](how-to-manage-account-keys.md)

    ```html
    <div id="map">
        <script>
            var MapsAccountKey = "<_your account key_>";
            var map = new atlas.Map("map", {
                "subscription-key": MapsAccountKey,
                center: [-122.33263,47.59093],
                zoom: 12
            });
        </script>
    </div>
    ```
    
5. Öppna filen i din webbläsare och visa renderade kartan.

## <a name="next-steps"></a>Nästa steg

Den här artikeln visade hur du skapar en grundläggande karta med din Azure Maps-nyckel. Flera kodexempel för att lägga till i dina kartor, finns i följande artiklar: 

* [Skapa en karta](map-create.md)
* [Välj kartan format](choose-map-style.md)
