---
title: Hur du använder Azure plats baserat Services Kartkontrollen | Microsoft Docs
description: Lär dig hur du använder Azure plats baserat Services Kartkontrollen klientens Javascript-bibliotek.
services: location-based-services
author: kgremban
ms.author: kgremban
ms.date: 11/22/2017
ms.topic: article
ms.service: location-based-services
manager: timlt
ms.openlocfilehash: ee767c9461f79437ab49d2a919bb82e7de8feba7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-use-the-azure-location-based-services-map-control"></a>Hur du använder Azure plats baserat Services Kartkontrollen
Javascript-bibliotek Kartkontrollen på klientsidan kan du återge maps och inbäddade Azure baserad platstjänster funktioner i ditt webb- eller mobila program. 

## <a name="prerequisites"></a>Förutsättningar
Ett Azure plats Services-konto och nyckel. Mer information om hur du skapar ett konto och hämta en nyckel finns [hur du hanterar din Azure plats Services-konto och nycklar](how-to-manage-account-keys.md). 

## <a name="create-a-new-map-in-a-web-page-using-the-map-control-api"></a>Skapa en ny mappning på en webbsida med kartan kontroll-API
Du kan bädda in en karta på en webbsida med hjälp av klientens Javascript-bibliotek för Kartkontrollen.

1. Skapa en ny fil och ge den namnet MapSearch.html.

2. Lägg till Azure baserat platstjänster stylesheet och skript källa referenser till den `<head>` element i filen:

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
    
4. Definiera ett nytt avsnitt i html-brödtext och skapa ett skript för att initiera kartkontrollen. Använda din egen Azure baserad platstjänster kontonyckel i skriptet. 

    ```html
    <div id="map">
        <script>
            var LBSAccountKey = "<_your account key_>";
            var map = new atlas.Map("map", {
                "subscription-key": LBSAccountKey,
                center: [47.59093,-122.33263],
                zoom: 12
            });
        </script>
    </div>
    ```
    
5. Öppna filen i din webbläsare och visa renderade kartan.