---
title: 'Snabbstart: Hämta meningslängder, Node.js – Translator Text API'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten lär du dig att fastställa meningslängder (i antal tecken) med hjälp av Node.js och Translator Text REST API.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 10/29/2018
ms.author: erhopf
ms.openlocfilehash: 09d0c855aada753b0a3e594055ec188c2698e677
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52998991"
---
# <a name="quickstart-use-the-translator-text-api-to-determine-sentence-length-with-nodejs"></a>Snabbstart: Använda Translator Text API för att fastställa meningslängd med Node.js

I den här snabbstarten lär du dig att fastställa meningslängder (i antal tecken) med hjälp av Node.js och Translator Text REST API.

För den här snabbstarten krävs ett [Azure Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med en Translator Text-resurs. Om du inte har ett konto kan du använda den [kostnadsfria utvärderingsversionen](https://azure.microsoft.com/try/cognitive-services/) för att hämta en prenumerationsnyckel.

## <a name="prerequisites"></a>Nödvändiga komponenter

För den här snabbstarten krävs:

* [Nod 8.12.x eller senare](https://nodejs.org/en/)
* En Azure-prenumerationsnyckel för Translator Text

## <a name="create-a-project-and-import-required-modules"></a>Skapa ett projekt och importera nödvändiga moduler

Skapa ett nytt projekt med valfri IDE eller redigeringsprogram. Kopiera sedan det här kodavsnittet till projektet i en fil med namnet `sentence-length.js`.

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
> Mer information om slutpunkter, vägar och att begära parametrar finns i [Translator Text API 3.0: Bryt mening](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence).

```javascript
let options = {
    method: 'POST',
    baseUrl: 'https://api.cognitive.microsofttranslator.com/',
    url: 'breaksentence',
    qs: {
      'api-version': '3.0',
    },
    headers: {
      'Ocp-Apim-Subscription-Key': subscriptionKey,
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    body: [{
          'text': 'How are you? I am fine. What did you do today?'
    }],
    json: true,
};
```

### <a name="authentication"></a>Autentisering

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
node sentence-length.js
```

Om du vill jämföra din kod med vår finns det fullständiga exemplet på [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS).

## <a name="sample-response"></a>Exempelsvar

```json
[
    {
        "sentLen": [
            13,
            11,
            22
        ]
    }
]
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du har hårdkodat din prenumerationsnyckel i programmet ser du till att ta bort prenumerationsnyckeln när du är klar med den här snabbstarten.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska Node.js-exempel på GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS)

## <a name="see-also"></a>Se även

Utöver språkidentifiering lär du dig hur du använder Translator Text API för att:

* [Översätta text](quickstart-nodejs-translate.md)
* [Translitterera text](quickstart-nodejs-transliterate.md)
* [Identifiera språket efter indata](quickstart-nodejs-detect.md)
* [Hämta alternativa översättningar](quickstart-nodejs-dictionary.md)
* [Hämta en lista över språk som stöds](quickstart-nodejs-languages.md)
