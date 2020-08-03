---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.custom: devx-track-javascript
ms.openlocfilehash: 1f2159f3f4c88c7ddafab63a7780f4dbf41ca130
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405439"
---
[!INCLUDE [Prerequisites](prerequisites-nodejs.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Skapa ett projekt och importera nödvändiga moduler

Skapa ett nytt projekt med valfri IDE eller redigeringsprogram. Kopiera sedan det här kodavsnittet till projektet i en fil med namnet `get-languages.js`.

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> Om du inte har använt de här modulerna behöver du installera dem innan du kör programmet. För att installera de här paketen kör du: `npm install request uuidv4`.

De här modulerna krävs för att skapa HTTP-begäran och skapa en unik identifierare för `'X-ClientTraceId'`-sidhuvudet.

## <a name="set-the-endpoint"></a>Ange slut punkten

I det här exemplet görs ett försök att läsa din översättares slut punkt från en miljö variabel: `TRANSLATOR_TEXT_ENDPOINT` . Om du inte känner till miljövariabler kan du ange `endpoint` som en sträng och kommentera bort den villkorliga instruktionen.

```javascript
lorum ipsum
```

## <a name="configure-the-request"></a>Konfigurera begäran

Metoden `request()`, som görs tillgänglig via begärandemodulen, gör att vi kan skicka HTTP-metoden, URL:en, begärandeparametrarna, sidhuvudena och JSON-brödtexten som ett `options`-objekt. I det här kodfragmentet konfigurerar vi begäran:

>[!NOTE]
> Mer information om slut punkter, vägar och parametrar för begäran finns i [Translator 3,0: språk](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

```javascript
let options = {
    method: 'GET',
    baseUrl: endpoint,
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

Om du använder en Cognitive Services-prenumeration med flera tjänster måste du också ta med `Ocp-Apim-Subscription-Region` i parametrarna för begäran. [Lär dig mer om att autentisera med multi-service-prenumerationen](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="make-the-request-and-print-the-response"></a>Göra en begäran och skriva ut svaret

Nu skapar vi begäran med hjälp av metoden `request()`. Det tar det `options`-objekt som vi skapade i föregående avsnitt som det första argumentet, och skriver sedan ut det förenklade JSON-svaret.

```javascript
request(options, function(err, res, body){
    console.log(JSON.stringify(body, null, 4));
});
```

>[!NOTE]
> I det här exemplet definierar vi HTTP-begäran i `options`-objektet. Dock har begärandemodulen även stöd för bekvämlighetsmetoder såsom `.post` och `.get`. Mer information finns i [bekvämlighetsmetoder](https://github.com/request/request#convenience-methods).

## <a name="put-it-all-together"></a>Färdigställa allt

Det innebär att du har skapat ett enkelt program som anropar översättaren och returnerar ett JSON-svar. Nu är det dags att köra programmet:

```console
node get-languages.js
```

Om du vill jämföra din kod med vår finns det fullständiga exemplet på [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS).

## <a name="sample-response"></a>Exempelsvar

Hitta lands-/region förkortningen i den här [listan över språk](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

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

Ta en titt på API-referensen för att förstå allt du kan göra med Translator.

> [!div class="nextstepaction"]
> [API-referens](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
