---
title: Använd modulen Azure Maps Services | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du använder Microsoft Azure Maps REST-tjänsterna med hjälp av Azure Maps-tjänstmodulen.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: e985fdda4638529e8ade2c700456d595ff355e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988744"
---
# <a name="use-the-azure-maps-services-module"></a>Använda modulen Azure Maps-tjänster

Azure Maps Web SDK tillhandahåller en *tjänstmodul*. Den här modulen är ett hjälpbibliotek som gör det enkelt att använda Azure Maps REST-tjänsterna i webb- eller Node.js-program med Hjälp av JavaScript eller TypeScript.

## <a name="use-the-services-module-in-a-webpage"></a>Använda tjänstemodulen på en webbsida

1. Skapa en ny HTML-fil.
1. Läs in azure maps-tjänstmodulen. Du kan läsa in den på ett av två sätt:
    - Använd den globalt värdbaserade Azure Content Delivery Network-versionen av Azure Maps-tjänstmodulen. Lägg till en `<head>` skriptreferens till elementet i filen:

        ```html
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
        ```

    - Du kan också läsa in tjänstmodulen för Azure Maps Web SDK-källkoden lokalt med hjälp av [azure-maps-rest npm-paketet](https://www.npmjs.com/package/azure-maps-rest) och sedan vara värd för den med din app. Det här paketet innehåller även TypeScript-definitioner. Använd det här kommandot:
    
        > **npm install azure-maps-rest**
    
        Lägg sedan till en `<head>` skriptreferens till elementet i filen:

         ```html
        <script src="node_modules/azure-maps-rest/dist/atlas-service.min.js"></script>
         ```

1. Skapa en autentiseringspipeline. Pipelinen måste skapas innan du kan initiera en tjänst-URL-klientslutpunkt. Använd dina egna Azure Maps-kontonyckel eller Azure Active Directory(Azure AD) autentiseringsuppgifter för att autentisera en Azure Maps Search-tjänstklient. I det här exemplet skapas URL-klienten för söktjänsten. 

    Om du använder en prenumerationsnyckel för autentisering:

    ```javascript
    // Get an Azure Maps key at https://azure.com/maps.
    var subscriptionKey = '<Your Azure Maps Key>';

    // Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);

    // Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```

    Om du använder Azure AD för autentisering:

    ```javascript
    // Enter your Azure AD client ID.
    var clientId = "<Your Azure Active Directory Client Id>";

    // Use TokenCredential with OAuth token (Azure AD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);

    // Create a repeating time-out that will renew the Azure AD token.
    // This time-out must be cleared when the TokenCredential object is no longer needed.
    // If the time-out is not cleared, the memory used by the TokenCredential will never be reclaimed.
    var renewToken = async () => {
      try {
        console.log("Renewing token");
        var token = await getAadToken();
        tokenCredential.token = token;
        tokenRenewalTimer = setTimeout(renewToken, getExpiration(token));
      } catch (error) {
        console.log("Caught error when renewing token");
        clearTimeout(tokenRenewalTimer);
        throw error;
      }
    }
    tokenRenewalTimer = setTimeout(renewToken, getExpiration(aadToken));

    // Use tokenCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
      // Use the signed-in auth context to get a token.
      return new Promise((resolve, reject) => {
        // The resource should always be https://atlas.microsoft.com/.
        const resource = "https://atlas.microsoft.com/";
        authContext.acquireToken(resource, (error, token) => {
          if (error) {
            reject(error);
          } else {
            resolve(token);
          }
        });
      })
    }

    function getExpiration(jwtToken) {
      // Decode the JSON Web Token (JWT) to get the expiration time stamp.
      const json = atob(jwtToken.split(".")[1]);
      const decode = JSON.parse(json);

      // Return the milliseconds remaining until the token must be renewed.
      // Reduce the time until renewal by 5 minutes to avoid using an expired token.
      // The exp property is the time stamp of the expiration, in seconds.
      const renewSkew = 300000;
      return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    Mer information finns i [Autentisering med Azure Maps](azure-maps-authentication.md).

1. Följande kod använder den nyligen skapade URL-klienten för Azure Maps Search-tjänsten för att geokoda en adress: "1 Microsoft Way, Redmond, WA". Koden använder `searchAddress` funktionen och visar resultatet som en tabell i sidans brödtext.

    ```javascript
    // Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa')
      .then(response => {
        var html = [];

        // Display the total results.
        html.push('Total results: ', response.summary.numResults, '<br/><br/>');

        // Create a table of the results.
        html.push('<table><tr><td></td><td>Result</td><td>Latitude</td><td>Longitude</td></tr>');

        for(var i=0;i<response.results.length;i++){
          html.push('<tr><td>', (i+1), '.</td><td>', 
            response.results[i].address.freeformAddress, 
            '</td><td>', 
            response.results[i].position.lat,
            '</td><td>', 
            response.results[i].position.lon,
            '</td></tr>');
        }

        html.push('</table>');

        // Add the resulting HTML to the body of the page.
        document.body.innerHTML = html.join('');
    });
    ```

    Här är det fullständiga kodexemplet som körs:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Använda tjänstemodulen" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se pennan <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>Med hjälp av servicesmodulen</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="azure-government-cloud-support"></a>Molnsupport för Azure Government

Azure Maps Web SDK stöder Azure Government-molnet. Alla JavaScript- och CSS-URL:er som används för att komma åt Azure Maps Web SDK förblir desamma, men följande uppgifter måste göras för att ansluta till Azure Government-molnversionen av Azure Maps-plattformen.

När du använder den interaktiva kartkontrollen lägger du till `Map` följande kodrad innan du skapar en förekomst av klassen. 

```javascript
atlas.setDomain('atlas.azure.us');
```

Var noga med att använda en Azure Maps-autentiseringsinformation från Azure Government-molnplattformen när du autentiserar kartan och tjänsterna.

När du använder tjänstemodulen måste domänen för tjänsterna anges när en instans av en API-URL-slutpunkt skapas. Följande kod skapar till exempel en `SearchURL` instans av klassen och pekar domänen till Azure Government-molnet.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Om du direkt öppnar Azure Maps REST-tjänsterna `atlas.azure.us`ändrar du URL-domänen till . Om du till exempel använder sök-API-tjänsten `https://atlas.microsoft.com/search/` `https://atlas.azure.us/search/`ändrar du URL-domänen från till .

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [KartorURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.mapsurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [SökURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [RouteURL (RuttURL)](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [SubscriptionKeyCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [TokenCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.tokencredential?view=azure-maps-typescript-latest)

Fler kodexempel som använder tjänstemodulen finns i följande artiklar:

> [!div class="nextstepaction"]
> [Visa sökresultat på kartan](./map-search-location.md)

> [!div class="nextstepaction"]
> [Hämta information från en koordinat](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Visa anvisningar från A till B](./map-route.md)
