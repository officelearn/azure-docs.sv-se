---
title: 'Snabbstart: Hämta en lista över språk som stöds, Node.js – Translator Text'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten hämtar du en lista över språk som stöds för översättning, transkribering och ordlistesökningar med hjälp av Translator Text-API:t med Node.js.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: df637ee16d2116696af42fa1b162e1c77a880c19
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64922190"
---
# <a name="quickstart-use-the-translator-text-api-to-get-a-list-of-supported-languages-with-nodejs"></a>Snabbstart: Använda Translator Text API för att hämta en lista över språk som stöds med Node.js

I den här snabbstarten lär du dig hur du gör en GET-begäran som returnerar en lista över språk som stöds med hjälp av Node.js och Translator Text REST API.

## <a name="prerequisites"></a>Nödvändiga komponenter

För den här snabbstarten krävs:

* [Nod 8.12.x eller senare](https://nodejs.org/en/)

## <a name="create-a-project-and-import-required-modules"></a>Skapa ett projekt och importera nödvändiga moduler

Skapa ett nytt projekt med valfri IDE eller redigeringsprogram. Kopiera sedan det här kodavsnittet till projektet i en fil med namnet `get-languages.js`.

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> Om du inte har använt de här modulerna behöver du installera dem innan du kör programmet. För att installera de här paketen kör du: `npm install request uuidv4`.

De här modulerna krävs för att skapa HTTP-begäran och skapa en unik identifierare för `'X-ClientTraceId'`-sidhuvudet.

## <a name="configure-the-request"></a>Konfigurera begäran

Metoden `request()`, som görs tillgänglig via begärandemodulen, gör att vi kan skicka HTTP-metoden, URL:en, begärandeparametrarna, sidhuvudena och JSON-brödtexten som ett `options`-objekt. I det här kodavsnittet konfigurerar vi begäran:

>[!NOTE]
> Mer information om slutpunkter, vägar och att begära parametrar finns i [Translator Text API 3.0: Språk](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

```javascript
let options = {
    method: 'GET',
    baseUrl: 'https://api.cognitive.microsofttranslator.com/',
    url: 'languages',
    qs: {
      'api-version': '3.0',
    },
    headers: {
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    json: true,
};
```

## <a name="make-the-request-and-print-the-response"></a>Göra begäran och skriva ut svaret

Nu skapar vi begäran med hjälp av metoden `request()`. Det tar det `options`-objekt som vi skapade i föregående avsnitt som det första argumentet, och skriver sedan ut det förenklade JSON-svaret.

```javascript
request(options, function(err, res, body){
    console.log(JSON.stringify(body, null, 4));
});
```

>[!NOTE]
> I det här exemplet definierar vi HTTP-begäran i `options`-objektet. Dock har begärandemodulen även stöd för bekvämlighetsmetoder såsom `.post` och `.get`. Mer information finns i [bekvämlighetsmetoder](https://github.com/request/request#convenience-methods).

## <a name="put-it-all-together"></a>Färdigställa allt

Det var allt – du har skapat ett enkelt program som anropar Translator Text API och returnerar en JSON-svar. Nu är det dags att köra programmet:

```console
node get-languages.js
```

Om du vill jämföra din kod med vår finns det fullständiga exemplet på [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS).

## <a name="sample-response"></a>Exempelsvar

Hitta en landsförkortning på i den här [Språklista](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

Det här exemplet har trunkerats för att visa ett avsnitt av resultatet:

```json
{
    "translation": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr"
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl"
        },
        ...
    },
    "transliteration": {
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "scripts": [
                {
                    "code": "Arab",
                    "name": "Arabic",
                    "nativeName": "العربية",
                    "dir": "rtl",
                    "toScripts": [
                        {
                            "code": "Latn",
                            "name": "Latin",
                            "nativeName": "اللاتينية",
                            "dir": "ltr"
                        }
                    ]
                },
                {
                    "code": "Latn",
                    "name": "Latin",
                    "nativeName": "اللاتينية",
                    "dir": "ltr",
                    "toScripts": [
                        {
                            "code": "Arab",
                            "name": "Arabic",
                            "nativeName": "العربية",
                            "dir": "rtl"
                        }
                    ]
                }
            ]
        },
      ...
    },
    "dictionary": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
      ...
    }
}
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du har hårdkodat din prenumerationsnyckel i programmet ser du till att ta bort prenumerationsnyckeln när du är klar med den här snabbstarten.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska Node.js-exempel på GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS)

## <a name="see-also"></a>Se också

Utöver språkidentifiering lär du dig hur du använder Translator Text API för att:

* [Översätta text](quickstart-nodejs-translate.md)
* [Translitterera text](quickstart-nodejs-transliterate.md)
* [Identifiera språket efter indata](quickstart-nodejs-detect.md)
* [Hämta alternativa översättningar](quickstart-nodejs-dictionary.md)
* [Fastställa meningslängd utifrån indata](quickstart-nodejs-sentences.md)
