---
title: Node.js Snabbstartsguide för Azure kognitiva Services, Microsoft Translator tal API | Microsoft Docs
description: Hämta information och exempel på kod för att snabbt komma igång med Microsoft översättare tal-API i kognitiva Microsoft-tjänster i Azure.
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jaswel
ms.openlocfilehash: d469fa008ba8acaf505fa09596dd739d5cc7744c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352272"
---
# <a name="quickstart-for-microsoft-translator-speech-api-with-nodejs"></a>Snabbstart för Microsoft Translator tal API med Node.js 
<a name="HOLTop"></a>

Den här artikeln visar hur du använder Microsoft översättare tal-API för att översätta ord talas i WAV-filer.

## <a name="prerequisites"></a>Förutsättningar

Du behöver [Node.js 6](https://nodejs.org/en/download/) att köra den här koden.

Du måste installera den [Websocket paketet](https://www.npmjs.com/package/websocket) för Node.js.

Du behöver en WAV-fil med namnet ”speak.wav” i samma mapp som den körbara filen som du sammanställer från koden nedan. Den här WAV-filen måste vara i standard PCM, 16-bitars, 16 kHz monoljud format. Du kan hämta dessa WAV-filer från de [översättare Text tala API](http://docs.microsofttranslator.com/text-translate.html#!/default/get_Speak).

Du måste ha en [kognitiva Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med **Microsoft översättare tal-API**. Du behöver en betald prenumeration nyckeln från din [Azure instrumentpanelen](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Översätta tal

Följande kod översätter tal från ett språk till en annan.

1. Skapa ett nytt Node.js-projekt i din favorit IDE.
2. Lägg till koden nedan.
3. Ersätt den `key` värde med en giltig snabbtangent för din prenumeration.
4. Kör programmet.

```nodejs
/* To install this dependency, run:
npm install websocket
*/
var wsClient = require('websocket').client;
var fs = require('fs');
/* To install this dependency, run:
npm install stream-buffers
*/
var streamBuffers = require('stream-buffers');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
let key = 'ENTER KEY HERE';

let host = 'wss://dev.microsofttranslator.com';
let path = '/speech/translate';
let params = '?api-version=1.0&from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa';
let uri = host + path + params;

/* The input .wav file is in PCM 16bit, 16kHz, mono format.
You can obtain such a .wav file using the Translator Text Speak API. See:
http://docs.microsofttranslator.com/text-translate.html#!/default/get_Speak
*/
let input_path = 'speak.wav';

let output_path = 'speak2.wav';

function receive(message) {
    console.log ("Received message of type: " + message.type + ".");

    if (message.type == 'utf8') {
        var result = JSON.parse(message.utf8Data)
        console.log("Response type: " + result.type + ".");
        console.log("Recognized input as: " + result.recognition);
        console.log("Translation: " + result.translation);
    }
    else if (message.type == 'binary') {
/* This is needed to make sure message.binaryData is defined. See:
https://stackoverflow.com/questions/17546953/cant-access-object-property-even-though-it-exists-returns-undefined
*/
        let binary_data = JSON.parse (JSON.stringify (message.binaryData));

        if (binary_data.type == 'Buffer') {
/* Put the binary data in a Buffer - do not write it directly to the file. */
            let buffer = new Buffer(binary_data.data, 'binary');
/* Write the contents of the Buffer to the file. */
            fs.writeFile (output_path, buffer, 'binary', function (error) {
/* fs.writeFile calls the error-handling function even if there is no error, in which case error === null. See:
https://stackoverflow.com/questions/44473868/node-js-fs-writefile-err-returns-null
*/
                if (error !== null) {
                    console.log ("Error: " + error);
                }
            });
        }
    }
}

function send(connection, filename) {
    
    var myReadableStreamBuffer = new streamBuffers.ReadableStreamBuffer({
        frequency: 100,
        chunkSize: 32000
    });

/* Make sure the audio file is followed by silence.
This lets the service know that the audio input is finished. */
    myReadableStreamBuffer.put(fs.readFileSync(filename));
    myReadableStreamBuffer.put(new Buffer(3200000));
    myReadableStreamBuffer.stop();

    myReadableStreamBuffer.on('data', function (data) {
        connection.sendBytes(data);
    });

    myReadableStreamBuffer.on('end', function () {
        console.log('Closing connection.');
        connection.close(1000);
    });
}

function connect() {
    var ws = new wsClient();

    ws.on('connectFailed', function (error) {
        console.log('Connection error: ' + error.toString());
    });
                            
    ws.on('connect', function (connection) {
        console.log('Connected.');

        connection.on('message', receive);
        
        connection.on('close', function (reasonCode, description) {
            console.log('Connection closed: ' + reasonCode);
        });

        connection.on('error', function (error) {
            console.log('Connection error: ' + error.toString());
        });
        
        send(connection, input_path);
    });

    ws.connect(uri, null, null, { 'Ocp-Apim-Subscription-Key' : key });
}

connect();
```

**Översätta tal svar**

En lyckad resultatet är att skapa en fil med namnet ”speak2.wav”. Filen innehåller översättning av orden talas i ”speak.wav”.

[Överst på sidan](#HOLTop)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Översättare tal självstudiekursen](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>Se också 

[Översättare tal, översikt](../overview.md)
[API-referens](http://docs.microsofttranslator.com/speech-translate.html)
