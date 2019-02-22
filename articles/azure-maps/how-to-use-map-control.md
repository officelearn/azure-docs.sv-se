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
ms.openlocfilehash: 1d8d78ceb9cdb5e5dd205306eac00c8ca89e8d4a
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2019
ms.locfileid: "56587947"
---
# <a name="use-the-azure-maps-map-control"></a>Använda Kartkontrollen Azure Maps

Kartkontroll klientens Javascript-biblioteket kan du återge kartor och inbäddade Azure Maps-funktioner till din webbplats eller mobila program.

## <a name="create-a-new-map-in-a-web-page"></a>Skapa en ny karta på en webbsida

Du kan bädda in en karta på en webbsida med hjälp av klientens Javascript-bibliotek för Kartkontrollen.

1. Skapa en ny HTML-fil.

2. Läs in modulen Azure Maps-webb-SDK. Detta kan göras med hjälp av ett av två alternativ;
    
    a. Använd den globalt värdbaserade CDN-versionen av Azure Maps Web SDK genom att lägga till URL-slutpunkter i formatmall och skript referenser i den `<head>` elementet i filen:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=2" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=2"></script>
    ```

    b. Du kan också läsa in Azure mappar webbtjänst-SDK-källkoden lokalt med hjälp av den [azure maps control](https://www.npmjs.com/package/azure-maps-control) NPM paketera och lägga upp den med din app. Det här paketet innehåller också TypeScript definitioner.

    > npm install azure-maps-kontroll

    Lägg till referenser till Azure Maps formatmall och skript källa referenser till den `<head>` elementet i filen:

    ```html
    <link rel="stylesheet" href="node_modules/azure-maps-control/dist/css/atlas.min.css" type="text/css" />
    <script src="node_modules/azure-maps-control/dist/js/atlas.min.js"></script>
    ```

3. Lägg till följande för att rendera kartan så att det fyller den fullständiga brödtexten ska `<style>` elementet så att den `<head>` element.

    ```html
    <style>
        html, body {
            margin: 0;
        }
    
        #myMap {
            height: 100vh;
            width: 100vw;
        }
    </style>
    ```

4. I tabellen på sidan, lägger du till en `<div>` element och ge den ett `id` av **myMap**. 

    ```html
    <body>
        <div id="myMap"></div>
    </body>
    ```

5. Definiera ett nytt avsnitt i html-text och skapa ett skript för att initiera kartkontrollen. Använd din egen nyckel för Azure Maps-konto eller autentiseringsuppgifter för Azure Active Directory (AAD) för att autentisera en karta med hjälp av [autentiseringsalternativ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions). Om du vill skapa ett konto eller hitta din nyckel finns i [så här hanterar du ditt Azure Maps-konto och dina nycklar](how-to-manage-account-keys.md). Den **språk** alternativet anger språket som ska användas för kartetiketter och kontroller. Mer information om språk som stöds finns i [språk som stöds](supported-languages.md). Om du använder en prenumerationsnyckel för autentisering.

    ```html
    <script type='text/javascript'>
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });
    </script>
    ```

    Om du använder Azure Active Directory (AAD) för autentisering:

    ```html
    <script type='text/javascript'>
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'aad',
                clientId: '<Your AAD Client Id>',
                aadAppId: '<Your AAD App Id',
                aadTenant: 'msft.ccsctp.net'
            }
        });
    </script>
    ```

6. Du kan också hända att lägga till följande meta taggen element till chefen för sidan till hjälp:

    ```html
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    
    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />
    ```

7. Sätter samman allt HTML-fil bör se ut ungefär så här:

    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>
    
        <meta charset="utf-8" />
        
        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    
        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />
    
        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=2" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=2"></script>
    
        <style>
            html, body {
                margin: 0;
            }
        
            #myMap {
                height: 100vh;
                width: 100vw;
            }
        </style>
    </head>
    <body>
        <div id="myMap"></div>
        
        <script type='text/javascript'>
            //Create an instance of the map control and set some options.
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });
        </script>
    </body>
    </html>
    ```

8. Öppna filen i din webbläsare och visa renderade kartan. Det bör se ut så här:

    <iframe height="700" style="width: 100%;" scrolling="no" title="Så här använder du kartkontroll" src="//codepen.io/azuremaps/embed/yZpEYL/?height=557&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se pennan <a href='https://codepen.io/azuremaps/pen/yZpEYL/'>så här använder du kartkontroll</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
    </iframe>

## <a name="next-steps"></a>Nästa steg

Lär dig hur du skapar och interagera med en karta:

> [!div class="nextstepaction"]
> [Skapa en karta](map-create.md)

Lär dig mer om att redigera en karta:

> [!div class="nextstepaction"]
> [Välj kartan format](choose-map-style.md)
