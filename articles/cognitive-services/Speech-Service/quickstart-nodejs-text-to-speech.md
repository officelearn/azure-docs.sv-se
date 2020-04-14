---
title: 'Snabbstart: Konvertera text till tal, node.js - Taltjänst'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten får du lära dig hur du konverterar text till tal med Node.js och REST API för text-till-tal. Exempeltexten som ingår i den här guiden är strukturerad som SSML (Speech Synthesis Markup Language). På så sätt kan du välja röst och språk för talsvaret.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/13/2020
ms.author: erhopf
ms.openlocfilehash: 8cf9641a1b7a5d1aada13522d612458d5032f883
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258702"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>Snabbstart: Konvertera text till tal med Node.js

I den här snabbstarten får du lära dig hur du konverterar text till tal med Node.js och REST API för text till tal. Förfrågastexten i den här guiden är strukturerad som [SSML (Speech Synthesis Markup Language),](speech-synthesis-markup.md)vilket gör att du kan välja röst och språk för svaret.

Den här snabbstarten kräver ett [Azure Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med en taltjänstresurs. Om du inte har ett konto kan du använda den [kostnadsfria utvärderingsversionen](get-started.md) för att hämta en prenumerationsnyckel.

## <a name="prerequisites"></a>Krav

För den här snabbstarten krävs:

* <a href="https://nodejs.org/en/" target="_blank">Nod 8.12.x eller senare<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual <span class="docon docon-navigate-external x-hidden-focus"> </span>Studio, </a> <a href="https://code.visualstudio.com/download" target="_blank">Visual <span class="docon docon-navigate-external x-hidden-focus"> </span>Studio-kod </a>eller din favorittextredigerare
* En Azure-prenumerationsnyckel för tjänsten Speech. [Få en gratis!](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Skapa ett projekt och kräva beroenden

Skapa ett nytt Node.js-projekt med din favorit-IDE eller redigerare. Kopiera sedan det här kodavsnittet till projektet i en fil med namnet `tts.js`.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write synthesized speech to a file
const fs = require('fs');
// Requires readline-sync to read command line inputs
const readline = require('readline-sync');
// Requires xmlbuilder to build the SSML body
const xmlbuilder = require('xmlbuilder');
```

> [!NOTE]
> Om du inte har använt de här modulerna behöver du installera dem innan du kör programmet. För att installera de här paketen kör du: `npm install request request-promise xmlbuilder readline-sync`.

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

I nästa avsnitt skapar vi funktionen för att anropa API:et för text-till-tal och spara det syntetiserade talsvaret.

## <a name="make-a-request-and-save-the-response"></a>Gör en begäran och spara svaret

Här ska du skapa begäran till API:et för text-till-tal och spara talsvaret. Det här exemplet förutsätter att du använder slutpunkten för västra USA. Om resursen är registrerad i en annan `uri`region kontrollerar du att du uppdaterar . Mer information finns i [Taltjänstområden](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Därefter måste du lägga till obligatoriska rubriker för begäran. Se till att `User-Agent` du uppdaterar med namnet på din resurs `X-Microsoft-OutputFormat` (finns i Azure-portalen) och ange din önskade ljudutdata. En fullständig lista över utdataformat finns i [Ljudutdata](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Konstruera sedan begärandetexten med hjälp av SSML (Speech Synthesis Markup Language). Det här exemplet definierar strukturen `text` och använder indata som du skapade tidigare.

>[!NOTE]
> I det `JessaRUS` här exemplet används röstteckensnittet. En fullständig lista över röster/språk som tillhandahålls av Microsoft finns i [Språkstöd](language-support.md).
> Om du är intresserad av att skapa en unik, igenkännbar röst för ditt varumärke läser [du Skapa anpassade röstteckensnitt](how-to-customize-voice-font.md).

Slutligen ska du göra en begäran till tjänsten. Om begäran lyckas och en 200-statuskod returneras skrivs `TTSOutput.wav`talsvaret som .

```javascript
// Make sure to update User-Agent with the name of your resource.
// You can also change the voice and output formats. See:
// https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech
function textToSpeech(accessToken, text) {
    // Create the SSML request.
    let xml_body = xmlbuilder.create('speak')
        .att('version', '1.0')
        .att('xml:lang', 'en-us')
        .ele('voice')
        .att('xml:lang', 'en-us')
        .att('name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
        .txt(text)
        .end();
    // Convert the XML into a string to send in the TTS request.
    let body = xml_body.toString();

    let options = {
        method: 'POST',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/v1',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'cache-control': 'no-cache',
            'User-Agent': 'YOUR_RESOURCE_NAME',
            'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
            'Content-Type': 'application/ssml+xml'
        },
        body: body
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('TTSOutput.wav'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>Färdigställa allt

Nästan klart. Det sista steget är att skapa en asynkron funktion. Den här funktionen kommer att läsa din prenumerationsnyckel från en miljövariabel, fråga efter text, hämta en token, vänta på att begäran ska slutföras, sedan konvertera text-till-tal och spara ljudet som en WAV.

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
    // Prompts the user to input text.
    const text = readline.question('What would you like to convert to speech? ');

    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken, text);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>Kör exempelappen

Det är allt, du är redo att köra din text-till-tal exempelapp. Från kommandoraden (eller en terminalsession) går du till projektkatalogen och kör:

```console
node tts.js
```

När du uppmanas till det skriver du in det du vill konvertera från text till tal. Om det lyckas finns talfilen i projektmappen. Spela upp den med din favorit mediaspelare.

## <a name="clean-up-resources"></a>Rensa resurser

Se till att ta bort all konfidentiell information från exempelappens källkod, till exempel prenumerationsnycklar.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska Node.js-exempel på GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Se även

* [Referens för text till tal-API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Skapa anpassade röstteckensnitt](how-to-customize-voice-font.md)
* [Spela in röstexempel för att skapa en anpassad röst](record-custom-voice-samples.md)
