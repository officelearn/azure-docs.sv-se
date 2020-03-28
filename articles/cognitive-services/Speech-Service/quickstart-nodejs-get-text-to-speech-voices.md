---
title: 'Snabbstart: Lista text-till-tal-röster, node.js - Taltjänst'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten får du lära dig hur du får en fullständig lista över standardröster och neurala röster för en region/slutpunkt med Node.js. Listan returneras som JSON och rösttillgängligheten varierar beroende på region.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: 7a929794ffaea4f863ffaef7227e58c7ccf901f0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74976578"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>Snabbstart: Hämta listan över text-till-tal-röster med Node.js

I den här snabbstarten får du lära dig hur du får en fullständig lista över standardröster och neurala röster för en region/slutpunkt med Node.js. Listan returneras som JSON och rösttillgängligheten varierar beroende på region. En lista över regioner som stöds finns i [regioner](regions.md).

Den här snabbstarten kräver ett [Azure Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med en taltjänstresurs. Om du inte har ett konto kan du använda den [kostnadsfria utvärderingsversionen](get-started.md) för att hämta en prenumerationsnyckel.

## <a name="prerequisites"></a>Krav

För den här snabbstarten krävs:

* [Nod 8.12.x eller senare](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) eller valfritt redigeringsprogram
* En Azure-prenumerationsnyckel för tjänsten Speech. [Få en gratis!](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Skapa ett projekt och kräva beroenden

Skapa ett nytt Node.js-projekt med din favorit-IDE eller redigerare. Kopiera sedan det här kodavsnittet till projektet i en fil med namnet `get-voices.js`.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write the list of languages to a file
const fs = require('fs');
```

> [!NOTE]
> Om du inte har använt de här modulerna behöver du installera dem innan du kör programmet. För att installera de här paketen kör du: `npm install request request-promise`.

## <a name="get-an-access-token"></a>Hämta en åtkomsttoken

REST API för text-till-tal kräver en åtkomsttoken för autentisering. För att få en åtkomsttoken krävs ett utbyte. Den här funktionen utbyter din taltjänstprenumerationsnyckel mot en åtkomsttoken med `issueToken` slutpunkten.

Det här exemplet förutsätter att prenumerationen på taltjänsten finns i regionen västra USA. Om du använder en annan region uppdaterar `uri`du värdet för . En fullständig lista finns i [Regioner](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Kopiera den här koden till projektet:

```javascript
// Gets an access token.
function getAccessToken(subscriptionKey) {
    let options = {
        method: 'POST',
        uri: 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken',
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    }
    return rp(options);
}
```

> [!NOTE]
> Mer information om autentisering finns [i Autentisera med en åtkomsttoken](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

I nästa avsnitt skapar vi funktionen för att få listan över röster och spara JSON-utdata till filen.

## <a name="make-a-request-and-save-the-response"></a>Gör en begäran och spara svaret

Här ska du skapa begäran och spara listan över returnerade röster. Det här exemplet förutsätter att du använder slutpunkten för västra USA. Om resursen är registrerad i en annan `uri`region kontrollerar du att du uppdaterar . Mer information finns i [Taltjänstområden](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Lägg sedan till obligatoriska rubriker för begäran. Slutligen ska du göra en begäran till tjänsten. Om begäran lyckas och en 200-statuskod returneras skrivs svaret till filen.

```javascript
function textToSpeech(accessToken) {
    let options = {
        method: 'GET',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/voices/list',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'Content-Type': 'application/json'
        }
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('voices.json'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>Färdigställa allt

Nästan klart. Det sista steget är att skapa en asynkron funktion. Den här funktionen kommer att läsa din prenumerationsnyckel från en miljövariabel, få en token, vänta på att begäran ska slutföras och sedan skriva JSON-svaret på filen.

Om du inte är bekant med miljövariabler eller föredrar att testa `process.env.SPEECH_SERVICE_KEY` med din prenumerationsnyckel som är hårdkodad som en sträng ersätter du med prenumerationsnyckeln som en sträng.

```javascript
// Use async and await to get the token before attempting
// to convert text to speech.
async function main() {
    // Reads subscription key from env variable.
    // You can replace this with a string containing your subscription key. If
    // you prefer not to read from an env variable.
    // e.g. const subscriptionKey = "your_key_here";
    const subscriptionKey = process.env.SPEECH_SERVICE_KEY;
    if (!subscriptionKey) {
        throw new Error('Environment variable for your subscription key is not set.')
    };
    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>Kör exempelappen

Det var allt. Nu är du redo att köra exempelappen. Från kommandoraden (eller en terminalsession) går du till projektkatalogen och kör:

```console
node get-voices.js
```

## <a name="clean-up-resources"></a>Rensa resurser

Se till att ta bort all konfidentiell information från exempelappens källkod, till exempel prenumerationsnycklar.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska Node.js-exempel på GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Se även

* [Referens för text till tal-API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Skapa anpassade röstteckensnitt](how-to-customize-voice-font.md)
* [Spela in röstexempel för att skapa en anpassad röst](record-custom-voice-samples.md)
