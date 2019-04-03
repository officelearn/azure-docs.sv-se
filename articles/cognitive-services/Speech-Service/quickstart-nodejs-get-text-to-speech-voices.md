---
title: 'Snabbstart: Lista text till tal röster Node.js – Speech Services'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten lär du dig att hämta en fullständig lista över standard- och neural röster för en region/slutpunkt med hjälp av Node.js. I listan returneras som JSON och röst tillgänglighet varierar beroende på region.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: erhopf
ms.openlocfilehash: 62187ddbe587a81038f8424b079e3c0c313d1ae2
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58887107"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>Snabbstart: Hämta listan över text till tal röster med hjälp av Node.js

I den här snabbstarten lär du dig att hämta en fullständig lista över standard- och neural röster för en region/slutpunkt med hjälp av Node.js. I listan returneras som JSON och röst tillgänglighet varierar beroende på region. En lista över regioner som stöds finns i [regioner](regions.md).

Den här snabbstarten kräver ett [Azure Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med en Speech Services-resurs. Om du inte har ett konto kan du använda den [kostnadsfria utvärderingsversionen](get-started.md) för att hämta en prenumerationsnyckel.

## <a name="prerequisites"></a>Förutsättningar

För den här snabbstarten krävs:

* [Noden 8.12.x eller senare](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) eller valfritt redigeringsprogram
* En Azure-prenumeration-nyckel för Speech Services. [Skaffa ett kostnadsfritt! ](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Skapa ett projekt och kräver beroenden

Skapa ett nytt Node.js-projekt med hjälp av din favorit-IDE eller redigerare. Kopiera sedan det här kodavsnittet till projektet i en fil med namnet `get-voices.js`.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write the list of languagesto a file
const fs = require('fs');
```

> [!NOTE]
> Om du inte har använt de här modulerna behöver du installera dem innan du kör programmet. För att installera de här paketen kör du: `npm install request request-promise`.

## <a name="get-an-access-token"></a>Hämta en åtkomsttoken

Text till tal REST-API kräver en åtkomsttoken för autentisering. Om du vill få en åtkomsttoken, krävs ett utbyte. Den här funktionen utbyter prenumerationsnyckeln Speech Services för en token med den `issueToken` slutpunkt.

Det här exemplet förutsätter att prenumerationen Speech Services är i regionen USA, västra. Om du använder en annan region måste du uppdatera värdet för `uri`. En fullständig lista finns i [regioner](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

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
> Mer information om autentisering finns i [autentisera med åtkomsttoken](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

I nästa avsnitt skapar vi funktionen för att hämta listan över röster och spara JSON-utdata till fil.

## <a name="make-a-request-and-save-the-response"></a>Gör en begäran och spara svaret

Här ska du skapa begäran och spara listan med returnerade röster. Det här exemplet förutsätter att du använder slutpunkten som USA, västra. Om din resurs har registrerats till en annan region, kontrollera att du uppdaterar den `uri`. Mer information finns i [Speech Services regioner](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Lägg sedan till nödvändiga sidhuvuden för begäran. Slutligen ska du göra en begäran till tjänsten. Om begäran lyckas, och statuskoden 200 returneras svaret skrivs till filen.

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

Nästan klart. Det sista steget är att skapa en asynkron funktion. Den här funktionen ska läsa din prenumerationsnyckel från en miljövariabel, hämta en token, vänta tills begäran slutförts och sedan skriva JSON-svar till fil.

Om du inte är bekant med miljövariabler eller föredrar att testa med din prenumeration viktiga hårdkodad som en sträng, ersätter `process.env.SPEECH_SERVICE_KEY` med din prenumerationsnyckel som en sträng.

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

## <a name="see-also"></a>Se också

* [Referens för text till tal-API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Skapa anpassade rösttyper](how-to-customize-voice-font.md)
* [Post voice-exempel för att skapa en anpassad röst](record-custom-voice-samples.md)
