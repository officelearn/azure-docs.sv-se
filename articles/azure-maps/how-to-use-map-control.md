---
title: Komma igång med webb kart kontroll | Microsoft Azure Maps
description: Lär dig hur du använder Microsoft Azure Maps kart kontrolls JavaScript-bibliotek på klient sidan för att återge kartor och inbäddade Azure Maps-funktioner i webb-eller mobil programmet.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 6becb504671c1fa380207fda9d7d553fca8ceddf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80335238"
---
# <a name="use-the-azure-maps-map-control"></a>Använda kartkontrollen i Azure Maps

Med Kartkontroll på klient sidans JavaScript-bibliotek kan du återge kartor och inbäddade Azure Maps funktioner i ditt webb-eller mobil program.

## <a name="create-a-new-map-in-a-web-page"></a>Skapa en ny karta på en webb sida

Du kan bädda in en karta på en webb sida med hjälp av Kartkontroll JavaScript-bibliotek på klient sidan.

1. Skapa en ny HTML-fil.

2. Läs in Azure Maps Web SDK. Du kan välja ett av två alternativ.

    * Använd den globalt värdbaserade CDN-versionen av Azure Maps Web SDK genom att lägga till referenser till Java Script och StyleSheet i `<head>` HTML-filens element:

        ```HTML
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
        ```

    * Läs in Azure Maps Web SDK-källkod lokalt med [Azure-Maps-Control NPM-](https://www.npmjs.com/package/azure-maps-control) paketet och var värd för den med din app. Det här paketet innehåller även TypeScript-definitioner.

        > **NPM installera Azure-Maps-Control**

       Lägg sedan till referenser till filen med Azure Maps-formatmall och skript källa till `<head>` filens element:

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

3. Om du vill rendera kartan så att den fyller hela sidans brödtext lägger du till följande `<style>` element i `<head>` elementet.

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

4. Lägg till ett-element i sidans huvuddel `<div>` och ge det ett `id` av **myMap**.

   ```HTML
    <body>
        <div id="myMap"></div>
    </body>
   ```

5. Om du vill initiera kart kontrollen definierar du en ny skript-tagg i HTML-texten. Skicka in på `id` kartan `<div>` eller en `HTMLElement` (till exempel `document.getElementById('myMap')` ) som den första parametern när du skapar en instans av `Map` klassen. Använd dina egna Azure Maps konto nyckel-eller Azure Active Directory-autentiseringsuppgifter (AAD) för att autentisera kartan med hjälp av [autentiserings alternativ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions). 

   Om du behöver skapa ett konto eller hitta din nyckel följer du instruktionerna i [skapa ett konto](quick-demo-map-app.md#create-an-account-with-azure-maps) och [Hämta primär nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account) . 

   Alternativet **språk** anger vilket språk som ska användas för kart etiketter och kontroller. Mer information om språk som stöds finns i [språk som stöds](supported-languages.md). Om du använder en prenumerations nyckel för autentisering använder du följande:

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

   En lista över exempel som visar hur du integrerar Azure Active Directory (AAD) med Azure Maps hittar du [här](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples). 
    
   Mer information finns i [autentisering med Azure Maps](azure-maps-authentication.md) -dokument och även [Azure Maps Azure AD-autentisering](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples).

6. Du kan också lägga till följande meta tag-element i sidhuvudet på din sida:

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. Att placera allt tillsammans i HTML-filen bör se ut ungefär så här:

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

8. Öppna filen i webbläsaren och visa den återgivna kartan. Det bör se ut som på bilden nedan:

   ![Kart bild som visar åter givnings resultat](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map"></a>Lokalisera kartan

Azure Maps tillhandahåller två olika sätt att ställa in språk och regional vy för den renderade kartan. Det första alternativet är att lägga till den här informationen i det globala `atlas` namn området, vilket leder till att alla instanser av kart kontroll i appen används som standard för de här inställningarna. Följande ställer in språket på franska ("fr-FR") och den regionala vyn till "Auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

Det andra alternativet är att skicka den här informationen till kart alternativen när du läser in kartan så här:

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
> Med webb-SDK är det möjligt att läsa in flera kart instanser på samma sida med olika språk-och region inställningar. Dessutom kan de här inställningarna uppdateras när kartan har lästs in med hjälp av `setStyle` kartans funktion. 

Här är ett exempel på Azure Maps med språket "fr-FR" och den regionala vyn inställt på "Auto".

![Kart bild som visar etiketter på franska](./media/how-to-use-map-control/websdk-localization.png)

En fullständig lista över språk som stöds och nationella vyer beskrivs [här](supported-languages.md).

## <a name="azure-government-cloud-support"></a>Azure Government Cloud Support

Azure Maps Web SDK stöder Azure Government molnet. Alla JavaScript-och CSS-URL: er som används för att komma åt Azure Maps Web SDK förblir desamma. Följande uppgifter måste göras för att ansluta till Azure Government Cloud-versionen av Azure Mapss plattformen.

När du använder den interaktiva kart kontrollen lägger du till följande kodrad innan du skapar en instans av `Map` klassen. 

```javascript
atlas.setDomain('atlas.azure.us');
```

Se till att använda Azure Maps autentiseringsinformation från Azure Government moln plattform när du autentiserar kartan och tjänsterna.

När du använder modulen tjänster måste domänen för tjänsterna anges när du skapar en instans av en API URL-slutpunkt. Följande kod skapar till exempel en instans av `SearchURL` klassen och pekar domänen i Azure Government molnet.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Ändra URL-domänen till om du vill få åtkomst till Azure Maps REST-tjänster direkt `atlas.azure.us` . Om du till exempel använder Search API-tjänsten, ändra URL-domänen från `https://atlas.microsoft.com/search/` till `https://atlas.azure.us/search/` .

## <a name="next-steps"></a>Nästa steg

Lär dig hur du skapar och interagerar med en karta:

> [!div class="nextstepaction"]
> [Skapa en karta](map-create.md)

Lär dig hur du formaterar en karta:

> [!div class="nextstepaction"]
> [Välj ett kartformat](choose-map-style.md)

Lägga till mer data i kartan:

> [!div class="nextstepaction"]
> [Skapa en karta](map-create.md)

> [!div class="nextstepaction"]
> [Kodexempel](https://docs.microsoft.com/samples/browse/?products=azure-maps)

En lista över exempel som visar hur du integrerar Azure Active Directory (AAD) med Azure Maps finns i:

> [!div class="nextstepaction"]
> [Exempel på Azure AD-autentisering](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
