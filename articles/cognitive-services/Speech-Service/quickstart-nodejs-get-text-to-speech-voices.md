---
title: 'Snabb start: Visa text till tal-röster, Node.js tal-tjänst'
titleSuffix: Azure Cognitive Services
description: I den här snabb starten får du lära dig hur du hämtar den fullständiga listan över standard-och neurala-röster för en region/slut punkt med hjälp av Node.js. Listan returneras som JSON och röst tillgängligheten varierar beroende på region.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/13/2020
ms.author: erhopf
ms.custom: devx-track-javascript
ms.openlocfilehash: d7ec5b386a9e62606a8b46c4e66cea85f8098a83
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406834"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>Snabb start: hämta listan med text till tal-röster med Node.js

I den här snabb starten får du lära dig hur du hämtar den fullständiga listan över standard-och neurala-röster för en region/slut punkt med hjälp av Node.js. Listan returneras som JSON och röst tillgängligheten varierar beroende på region. En lista över regioner som stöds finns i [regioner](regions.md).

Den här snabb starten kräver ett [Azure Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med en Speech service-resurs. Om du inte har ett konto kan du använda den [kostnadsfria utvärderingsversionen](get-started.md) för att hämta en prenumerationsnyckel.

## <a name="prerequisites"></a>Förutsättningar

För den här snabbstarten krävs:

* <a href="https://nodejs.org/en/" target="_blank">Node 8.12. x eller senare<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual Studio <span class="docon docon-navigate-external x-hidden-focus"></span> </a>, <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio Code <span class="docon docon-navigate-external x-hidden-focus"></span> </a>eller din favorit text redigerare
* En Azure-prenumerationsnyckel för tjänsten Speech. [Hämta en kostnads fri!](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Skapa ett projekt och Kräv beroenden

Skapa ett nytt Node.js-projekt med hjälp av din favorit-IDE eller-redigerare. Kopiera sedan det här kodavsnittet till projektet i en fil med namnet `get-voices.js`.

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

Text till tal-REST API kräver en åtkomsttoken för autentisering. För att få en åtkomsttoken krävs ett utbyte. Den här funktionen utbyter din röst tjänst prenumerations nyckel för en åtkomsttoken med hjälp av `issueToken` slut punkten.

Det här exemplet förutsätter att din röst tjänst prenumeration är i regionen USA, västra. Uppdatera värdet för om du använder en annan region `uri` . En fullständig lista finns i [regioner](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

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
> Mer information om autentisering finns i [autentisera med en åtkomsttoken](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

I nästa avsnitt skapar vi funktionen för att hämta listan över röster och spara JSON-utdata till filen.

## <a name="make-a-request-and-save-the-response"></a>Gör en begäran och spara svaret

Här skapar du begäran och sparar listan med returnerade röster. I det här exemplet förutsätter vi att du använder slut punkten västra USA. Om din resurs är registrerad i en annan region, se till att du uppdaterar `uri` . Mer information finns i avsnittet om [tal service områden](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Lägg sedan till de rubriker som krävs för begäran. Slutligen ska du skapa en begäran till tjänsten. Om begäran lyckas och en status kod för 200 returneras, skrivs svaret till filen.

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

Nästan klart. Det sista steget är att skapa en asynkron funktion. Den här funktionen kommer att läsa din prenumerations nyckel från en miljö variabel, hämta en token, vänta tills begäran har slutförts och sedan skriva JSON-svaret till filen.

Om du inte känner till miljövariabler eller vill testa med prenumerations nyckeln hårdkodad som en sträng ersätter du `process.env.SPEECH_SERVICE_KEY` med prenumerations nyckeln som en sträng.

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
* [Skapa anpassade röst teckensnitt](how-to-customize-voice-font.md)
* [Spela in röst exempel för att skapa en anpassad röst](record-custom-voice-samples.md)
