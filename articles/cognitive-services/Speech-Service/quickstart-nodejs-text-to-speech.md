---
title: 'Snabb start: konvertera text till tal, Node. js – tal service'
titleSuffix: Azure Cognitive Services
description: I den här snabb starten får du lära dig hur du konverterar text till tal med Node. js och text till tal-REST API. Exempel texten som ingår i den här guiden är strukturerad som SSML (Speech syntes Markup Language). På så sätt kan du välja röst-och språk för tal svaret.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/13/2020
ms.author: erhopf
ms.openlocfilehash: 8cf9641a1b7a5d1aada13522d612458d5032f883
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81258702"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>Snabb start: konvertera text till tal med Node. js

I den här snabb starten får du lära dig hur du konverterar text till tal med Node. js och text till tal-REST API. Begär ande texten i den här guiden är strukturerad som [SSML (Speech syntes Markup Language)](speech-synthesis-markup.md), vilket gör att du kan välja röst och språk för svaret.

Den här snabb starten kräver ett [Azure Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med en Speech service-resurs. Om du inte har ett konto kan du använda den [kostnadsfria utvärderingsversionen](get-started.md) för att hämta en prenumerationsnyckel.

## <a name="prerequisites"></a>Krav

För den här snabbstarten krävs:

* <a href="https://nodejs.org/en/" target="_blank">Node 8.12. x eller senare<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual Studio <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>, <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio Code <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>eller din favorit text redigerare
* En Azure-prenumerationsnyckel för tjänsten Speech. [Hämta en kostnads fri!](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Skapa ett projekt och Kräv beroenden

Skapa ett nytt Node. js-projekt med hjälp av din favorit-IDE eller-redigerare. Kopiera sedan det här kodavsnittet till projektet i en fil med namnet `tts.js`.

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

Text till tal-REST API kräver en åtkomsttoken för autentisering. För att få en åtkomsttoken krävs ett utbyte. Den här funktionen utbyter din röst tjänst prenumerations nyckel för en åtkomsttoken `issueToken` med hjälp av slut punkten.

Det här exemplet förutsätter att din röst tjänst prenumeration är i regionen USA, västra. Uppdatera värdet för `uri`om du använder en annan region. En fullständig lista finns i [regioner](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

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

I nästa avsnitt skapar vi funktionen för att anropa text till tal-API: et och spara det syntetiskt tal svaret.

## <a name="make-a-request-and-save-the-response"></a>Gör en begäran och spara svaret

Här skapar du begäran till text till tal-API: et och sparar tal svaret. I det här exemplet förutsätter vi att du använder slut punkten västra USA. Om din resurs är registrerad i en annan region, se till att du uppdaterar `uri`. Mer information finns i avsnittet om [tal service områden](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Därefter måste du lägga till nödvändiga rubriker för begäran. Se till att du uppdaterar `User-Agent` med namnet på din resurs (finns i Azure Portal) och Ställ in `X-Microsoft-OutputFormat` på önskad ljud uppspelning. En fullständig lista över utdataformat finns i [ljud utmatningar](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Konstruera sedan begär ande texten med hjälp av tal syntes Markup Language (SSML). Det här exemplet definierar strukturen och använder `text` indatamängden som du skapade tidigare.

>[!NOTE]
> I `JessaRUS` det här exemplet används röst teckensnittet. En fullständig lista över de röster/språk som tillhandahålls av Microsoft finns i [språk stöd](language-support.md).
> Om du är intresse rad av att skapa en unik, identifierbar röst för ditt varumärke, se [skapa anpassade röst teckensnitt](how-to-customize-voice-font.md).

Slutligen ska du skapa en begäran till tjänsten. Om begäran lyckas och en status kod för 200 returneras skrivs tal svaret som `TTSOutput.wav`.

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

Nästan klart. Det sista steget är att skapa en asynkron funktion. Den här funktionen kommer att läsa din prenumerations nyckel från en miljö variabel, fråga efter text, hämta en token, vänta tills begäran har slutförts och sedan konvertera text till tal och spara ljudet som. wav.

Om du inte känner till miljövariabler eller vill testa med prenumerations nyckeln hårdkodad som en sträng ersätter `process.env.SPEECH_SERVICE_KEY` du med prenumerations nyckeln som en sträng.

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

Då är det dags att köra din text-till-tal-exempel App. Från kommandoraden (eller en terminalsession) går du till projektkatalogen och kör:

```console
node tts.js
```

När du uppmanas till det skriver du det du vill konvertera från text till tal. Om det lyckas finns tal filen i projektmappen. Spela upp den med din favorit medie spelare.

## <a name="clean-up-resources"></a>Rensa resurser

Se till att ta bort all konfidentiell information från exempelappens källkod, till exempel prenumerationsnycklar.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska Node.js-exempel på GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Se även

* [Referens för text till tal-API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Skapa anpassade röst teckensnitt](how-to-customize-voice-font.md)
* [Spela in röst exempel för att skapa en anpassad röst](record-custom-voice-samples.md)
