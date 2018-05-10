---
title: Hur du använder Azure Maps Kartkontrollen | Microsoft Docs
description: Lär dig hur du använder Azure Maps Kartkontrollen klientens Javascript-bibliotek.
services: azure-maps
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
manager: timlt
ms.openlocfilehash: bbd06aad9052d2a775c35dd08f80462f8ea505a9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-use-the-azure-maps-map-control"></a>Hur du använder Azure Maps Kartkontrollen
Javascript-bibliotek Kartkontrollen på klientsidan kan du återge maps och inbäddade Azure mappar funktioner i ditt webb- eller mobila program. 

## <a name="create-a-new-map-in-a-web-page"></a>Skapa en ny mappning på en webbsida

Du kan bädda in en karta på en webbsida med hjälp av klientens Javascript-bibliotek för Kartkontrollen.

1. Skapa en ny fil och ge den namnet MapSearch.html.

2. Lägg till de Azure Maps stylesheet och skript källa referenserna till den `<head>` element i filen:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
    ```
    
3. För att göra en ny mappning i webbläsaren, lägger du till en **#map** refererar till i den `<style>` element.

    ```html
    #map {
                width: 100%;
                height: 100%;
            }
    ``` 
    
4. Definiera ett nytt avsnitt i html-brödtext och skapa ett skript för att initiera kartkontrollen. Använda din egen nyckel för Azure Maps-konto i skriptet. Om du behöver skapa ett konto eller söka efter din nyckel finns [hur du hanterar din Azure Maps-konto och nycklar](how-to-manage-account-keys.md)

    ```html
    <div id="map">
        <script>
            var MapsAccountKey = "<_your account key_>";
            var map = new atlas.Map("map", {
                "subscription-key": MapsAccountKey,
                center: [47.59093,-122.33263],
                zoom: 12
            });
        </script>
    </div>
    ```
    
5. Öppna filen i din webbläsare och visa renderade kartan.

## <a name="next-steps"></a>Nästa steg

Den här artikeln visades hur du skapar en grundläggande karta med Azure Maps-nyckel. Att lägga till i din maps mer kodexempel finns i följande artiklar: 

* [Skapa en karta](map-create.md)
* [Lägga till en PIN-kod](map-add-pin.md)