---
title: Så här använder du Kartkontroll för Azure Maps | Microsoft Docs
description: Lär dig hur du använder Azure Maps Kartkontroll klientens Javascript-biblioteket.
author: dsk-2015
ms.author: dkshir
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 850f9b28c112c11fd98a8abc81a1811cd26d81cc
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166040"
---
# <a name="use-the-azure-maps-map-control"></a>Använda Kartkontrollen Azure Maps

Kartkontroll klientens Javascript-biblioteket kan du återge kartor och inbäddade Azure Maps-funktioner till din webbplats eller mobila program.

## <a name="create-a-new-map-in-a-web-page"></a>Skapa en ny karta på en webbsida

Du kan bädda in en karta på en webbsida med hjälp av klientens Javascript-bibliotek för Kartkontrollen.

1. Skapa en ny fil och ge den namnet **MapSearch.html**.

2. Lägg till Azure Maps formatmall och skript källa referenser till den `<head>` elementet i filen:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>
    ```

3. För att rendera en ny karta i webbläsaren ska du lägga till en **#map** refererar till i den `<style>` element:

    ```html
    <style>
        #map {
            width: 100%;
            height: 100%;
        }
    </style>
    ```

4. Definiera ett nytt avsnitt i html-text och skapa ett skript för att initiera kartkontrollen. Använd din egen nyckel för Azure Maps-konto i skriptet. Om du vill skapa ett konto eller hitta din nyckel finns i [så här hanterar du ditt Azure Maps-konto och dina nycklar](how-to-manage-account-keys.md). Den **språkinställning** metoden anger språket som ska användas för kartetiketter och kontroller. Mer information om språk som stöds finns i [språk som stöds](https://docs.microsoft.com/azure/azure-maps/supported-languages).

    ```html
    <div id="map">
        <script>
            atlas.setSubscriptionKey("<_your account key_>");
            atlas.setLanguage("en");
            var map = new atlas.Map("map", {
                center: [-122.33263,47.59093],
                zoom: 12
            });
        </script>
    </div>
    ```

5. Öppna filen i din webbläsare och visa renderade kartan.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du skapar en karta med ett fullständigt exempel:

> [!div class="nextstepaction"]
> [Skapa en karta](map-create.md)

Lär dig mer om att redigera en karta:

> [!div class="nextstepaction"]
> [Välj kartan format](choose-map-style.md)
