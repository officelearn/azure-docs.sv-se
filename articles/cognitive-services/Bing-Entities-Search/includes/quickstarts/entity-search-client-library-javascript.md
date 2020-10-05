---
title: Snabb start för Entitetssökning i Bing Java Script Client library
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: b145cc1689ad2c1a39591df0e39bb8d0445333c7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91377365"
---
Använd den här snabb starten för att börja söka efter entiteter med Entitetssökning i Bing klient bibliotek för Java Script. Även om Entitetssökning i Bing har en REST API som är kompatibel med de flesta programmeringsspråk, är klient biblioteket ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js).

## <a name="prerequisites"></a>Förutsättningar

* Den senaste versionen av [Node.js](https://nodejs.org/en/download/).
* [ENTITETSSÖKNING I Bing SDK för Java Script](https://www.npmjs.com/package/@azure/cognitiveservices-entitysearch)
     *  Installera genom att köra `npm install @azure/cognitiveservices-entitysearch`
* `CognitiveServicesCredentials`Klassen från `@azure/ms-rest-azure-js` paketet för att autentisera klienten.
     * Installera genom att köra `npm install @azure/ms-rest-azure-js`

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa en ny JavaScript-fil i valfri IDE eller redigeringsprogram och lägg till följande krav.

    ```javascript
    const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
    const EntitySearchAPIClient = require('@azure/cognitiveservices-entitysearch');
    ```

2. Skapa en instans av `CognitiveServicesCredentials` med hjälp av din prenumerationsnyckel. Skapa sedan en instans av sökklienten med den.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let entitySearchApiClient = new EntitySearchAPIClient(credentials);
    ```

## <a name="send-a-request-and-receive-a-response"></a>Skicka en begäran och ta emot ett svar

1. Skicka en begäran för entitetssökning med `entitiesOperations.search()`. När du har fått ett svar skriver du ut `queryContext`, antalet returnerade resultat och en beskrivning av det första resultatet.

    ```javascript
    entitySearchApiClient.entitiesOperations.search('seahawks').then((result) => {
        console.log(result.queryContext);
        console.log(result.entities.value);
        console.log(result.entities.value[0].description);
    }).catch((err) => {
        throw err;
    });
    ```

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en enkelsidig webbapp](../../tutorial-bing-entities-search-single-page-app.md)

* [Vad är API för entitetsökning i Bing?](../../overview.md)