---
title: 'Snabbstart: Slå upp ord med en tvåspråkig ordlista, Node.js – Translator Text API'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten lär du dig att hitta alternativa översättningar och användningsexempel för en angiven text med hjälp av Node.js och Translator Text REST API.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: 4b8d26868c3880852e0d2f9c43b06ca1b25d094f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58176386"
---
# <a name="quickstart-look-up-words-with-bilingual-dictionary-using-nodejs"></a>Snabbstart: Slå upp ord med en tvåspråkig ordlista med hjälp av Node.js

I den här snabbstarten lär du dig att hitta alternativa översättningar och användningsexempel för en angiven text med hjälp av Node.js och Translator Text REST API.

För den här snabbstarten krävs ett [Azure Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med en Translator Text-resurs. Om du inte har ett konto kan du använda den [kostnadsfria utvärderingsversionen](https://azure.microsoft.com/try/cognitive-services/) för att hämta en prenumerationsnyckel.

## <a name="prerequisites"></a>Förutsättningar

För den här snabbstarten krävs:

* [Nod 8.12.x eller senare](https://nodejs.org/en/)
* En Azure-prenumerationsnyckel för Translator Text

## <a name="create-a-project-and-import-required-modules"></a>Skapa ett projekt och importera nödvändiga moduler

Skapa ett nytt projekt med din favorit-IDE eller redigerare eller skapa en ny mapp på skrivbordet. Kopiera det här kodfragmentet till ditt projekt/mapp till en fil med namnet `alt-translations.js`.

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> Om du inte har använt de här modulerna behöver du installera dem innan du kör programmet. För att installera de här paketen kör du: `npm install request uuidv4`.

De här modulerna krävs för att skapa HTTP-begäran och skapa en unik identifierare för `'X-ClientTraceId'`-sidhuvudet.

## <a name="set-the-subscription-key"></a>Ange prenumerationsnyckeln

Den här koden kommer att försöka läsa din Translator Text-prenumerationsnyckel från miljövariabeln `TRANSLATOR_TEXT_KEY`. Om du inte känner till miljövariabler kan du ange `subscriptionKey` som en sträng och kommentera bort den villkorliga instruktionen.

Kopiera den här koden till projektet:

```javascript
/* Checks to see if the subscription key is available
as an environment variable. If you are setting your subscription key as a
string, then comment these lines out.

If you want to set your subscription key as a string, replace the value for
the Ocp-Apim-Subscription-Key header as a string. */
const subscriptionKey = process.env.TRANSLATOR_TEXT_KEY;
if (!subscriptionKey) {
  throw new Error('Environment variable for your subscription key is not set.')
};
```

## <a name="configure-the-request"></a>Konfigurera begäran

Metoden `request()`, som görs tillgänglig via begärandemodulen, gör att vi kan skicka HTTP-metoden, URL:en, begärandeparametrarna, sidhuvudena och JSON-brödtexten som ett `options`-objekt. I det här kodavsnittet konfigurerar vi begäran:

>[!NOTE]
> Mer information om slutpunkter, vägar och att begära parametrar finns i [Translator Text API 3.0: Slå upp i ordlista](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-dictionary-lookup).

```javascript
let options = {
    method: 'POST',
    baseUrl: 'https://api.cognitive.microsofttranslator.com/',
    url: 'dictionary/lookup',
    qs: {
      'api-version': '3.0',
      'from': 'en',
      'to': 'es'
    },
    headers: {
      'Ocp-Apim-Subscription-Key': subscriptionKey,
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    body: [{
          'text': 'Elephants'
    }],
    json: true,
};
```

### <a name="authentication"></a>Authentication

Det enklaste sättet att autentisera en begäran är att skicka din prenumerationsnyckel som ett `Ocp-Apim-Subscription-Key`-sidhuvud, vilket är det vi använder i det här exemplet. Alternativt kan du byta din prenumerationsnyckel mot en åtkomsttoken och skicka vidare åtkomsttoken som ett `Authorization`-sidhuvud för att verifiera din begäran. Mer information finns i [Autentisering](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

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
node alt-translations.js
```

Om du vill jämföra din kod med vår finns det fullständiga exemplet på [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS).

## <a name="sample-response"></a>Exempelsvar

```json
[
    {
        "displaySource": "elephants",
        "normalizedSource": "elephants",
        "translations": [
            {
                "backTranslations": [
                    {
                        "displayText": "elephants",
                        "frequencyCount": 1207,
                        "normalizedText": "elephants",
                        "numExamples": 5
                    }
                ],
                "confidence": 1.0,
                "displayTarget": "elefantes",
                "normalizedTarget": "elefantes",
                "posTag": "NOUN",
                "prefixWord": ""
            }
        ]
    }
]
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
* [Hämta en lista över språk som stöds](quickstart-nodejs-languages.md)
* [Fastställa meningslängd utifrån indata](quickstart-nodejs-sentences.md)
