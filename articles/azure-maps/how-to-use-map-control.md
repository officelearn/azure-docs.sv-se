---
title: Komma igång med webbkartkontroll | Microsoft Azure Maps
description: Lär dig hur du använder Microsoft Azure Maps-mappningen styr JavaScript-bibliotek på klientsidan för att rendera kartor och inbäddade Azure Maps-funktioner i ditt webb- eller mobilappprogram.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 6becb504671c1fa380207fda9d7d553fca8ceddf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335238"
---
# <a name="use-the-azure-maps-map-control"></a>Använda kartkontrollen Azure Maps

Med JavaScript-biblioteket på Map Control-klientsidan kan du återge kartor och inbäddade Azure Maps-funktioner i ditt webb- eller mobilapplikation.

## <a name="create-a-new-map-in-a-web-page"></a>Skapa en ny karta på en webbsida

Du kan bädda in en karta på en webbsida med hjälp av JavaScript-biblioteket på Map Control-klientsidan.

1. Skapa en ny HTML-fil.

2. Läs in i Azure Maps Web SDK. Du kan välja ett av två alternativ.

    * Använd den globalt värdbaserade CDN-versionen av Azure Maps Web SDK genom att `<head>` lägga till referenser till JavaScript och formatmallen i elementet i HTML-filen:

        ```HTML
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
        ```

    * Läs in Azure Maps Web SDK-källkoden lokalt med hjälp av [det Azure-maps-control NPM-paketet](https://www.npmjs.com/package/azure-maps-control) och är värd för den med din app. Det här paketet innehåller även TypeScript-definitioner.

        > **npm installera azure-maps-control**

       Lägg sedan till referenser till formatmallen Azure Maps `<head>` och skriptkällans referenser till elementet i filen:

        ```HTML
        <link rel="stylesheet" href="node_modules/azure-maps-control/dist/atlas.min.css" type="text/css"> 
        <script src="node_modules/azure-maps-control/dist/atlas.min.js"></script>
        ```

    > [!Note]
    > Typescript-definitioner kan importeras till ditt program genom att lägga till följande kod:
    >
    > ```Javascript
    > import * as atlas from 'azure-maps-control';
    > ```

3. Om du vill återge kartan så att den fyller `<style>` hela sidan `<head>` lägger du till följande element i elementet.

   ```HTML
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

4. I brödtexten på sidan, lägga till ett `<div>` element och ge det en `id` av **myMap**.

   ```HTML
    <body>
        <div id="myMap"></div>
    </body>
   ```

5. Om du vill initiera kartkontrollen definierar du en ny skripttagg i html-brödtexten. Passera i `id` kartan `<div>` eller `HTMLElement` en (till `document.getElementById('myMap')`exempel ) som den första `Map` parametern när du skapar en förekomst av klassen. Använd dina egna Azure Maps-kontonyckel eller Azure Active Directory (AAD) autentiseringsuppgifter för att autentisera kartan med [autentiseringsalternativ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions). 

   Om du behöver skapa ett konto eller hitta din nyckel följer du instruktionerna i [Skapa ett konto](quick-demo-map-app.md#create-an-account-with-azure-maps) och får [primärnyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account) . 

   **Språkalternativet** anger vilket språk som ska användas för kartetiketter och kontroller. Mer information om språk som stöds finns i [språk som stöds](supported-languages.md). Om du använder en prenumerationsnyckel för autentisering använder du följande:

   ```HTML
    <script type="text/javascript">
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

   Om du använder Azure Active Directory (AAD) för autentisering använder du följande:

   ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'aad',
                clientId: '<Your AAD Client Id>',
                aadAppId: '<Your AAD App Id>',
                aadTenant: '<Your AAD Tenant Id>'
            }
        });
    </script>
   ```

   En lista med exempel som visar hur du integrerar Azure Active Directory (AAD) med Azure Maps finns [här](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples). 
    
   Mer information finns i [dokumentet Autentisering med Azure Maps](azure-maps-authentication.md) och även azure maps Azure [AD-autentiseringsexempel](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples).

6. Du kan också hitta lägga till följande metataggelement i sidhuvudet till hjälp:

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. Att sätta ihop allt din HTML-fil ska se ut ungefär så här kod:

   ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

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

        <script type="text/javascript">
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

8. Öppna filen i webbläsaren och visa den renderade kartan. Det bör se ut som bilden nedan:

   ![Kartbild som visar renderat resultat](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map"></a>Lokalisera kartan

Azure Maps innehåller två olika sätt att ange språk- och regionalvyn för den renderade kartan. Det första alternativet är att lägga `atlas` till den här informationen i det globala namnområdet, vilket resulterar i att alla kartkontrollinstanser i appen som standard används för att använda dessa inställningar. Följande ställer språket till franska ("fr-FR") och den regionala vyn till "Auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

Det andra alternativet är att skicka denna information till kartalternativen när du läser in kartan så här:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'Auto',

    authOptions: {
        authType: 'aad',
        clientId: '<Your AAD Client Id>',
        aadAppId: '<Your AAD App Id>',
        aadTenant: '<Your AAD Tenant Id>'
    }
});
```

> [!Note]
> Med Web SDK är det möjligt att läsa in flera kartinstanser på samma sida med olika språk- och regioninställningar. Dessutom kan dessa inställningar uppdateras när kartan `setStyle` läses in med hjälp av kartans funktion. 

Här är ett exempel på Azure Maps med språket inställt på "fr-FR" och den regionala vyn inställd på "Auto".

![Kartbild som visar etiketter på franska](./media/how-to-use-map-control/websdk-localization.png)

En fullständig lista över språk och regionala vyer som stöds dokumenteras [här](supported-languages.md).

## <a name="azure-government-cloud-support"></a>Molnsupport för Azure Government

Azure Maps Web SDK stöder Azure Government-molnet. Alla JavaScript- och CSS-url:er som används för att komma åt Azure Maps Web SDK förblir desamma. Följande uppgifter måste göras för att ansluta till Azure Government-molnversionen av Azure Maps-plattformen.

När du använder den interaktiva kartkontrollen lägger du till `Map` följande kodrad innan du skapar en förekomst av klassen. 

```javascript
atlas.setDomain('atlas.azure.us');
```

Var noga med att använda Azure Maps autentiseringsinformation från Azure Government molnplattform när du autentiserar kartan och tjänster.

När du använder tjänstemodulen måste domänen för tjänsterna anges när en instans av en API-URL-slutpunkt skapas. Följande kod skapar till exempel en `SearchURL` instans av klassen och pekar domänen till Azure Government-molnet.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Om du direkt öppnar Azure Maps REST-tjänsterna `atlas.azure.us`ändrar du URL-domänen till . Om du till exempel använder sök-API-tjänsten `https://atlas.microsoft.com/search/` `https://atlas.azure.us/search/`ändrar du URL-domänen från till .

## <a name="next-steps"></a>Nästa steg

Lär dig hur du skapar och interagerar med en karta:

> [!div class="nextstepaction"]
> [Skapa en karta](map-create.md)

Läs om hur du formaterar en karta:

> [!div class="nextstepaction"]
> [Välj ett kartformat](choose-map-style.md)

Så här lägger du till mer data på kartan:

> [!div class="nextstepaction"]
> [Skapa en karta](map-create.md)

> [!div class="nextstepaction"]
> [Kodexempel](https://docs.microsoft.com/samples/browse/?products=azure-maps)

En lista med exempel som visar hur du integrerar Azure Active Directory (AAD) med Azure Maps finns i:

> [!div class="nextstepaction"]
> [Exempel på Azure AD-autentisering](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
