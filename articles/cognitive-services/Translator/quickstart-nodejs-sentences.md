---
title: Hämta meningslängden med Translator Text och Node.js | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: I den här snabbstarten ska du hämta längden på meningar i text med hjälp av Translator Text-API:et med Node.js i Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/21/2018
ms.author: nolachar
ms.openlocfilehash: 42fe27df2f0d6aacecfe2b9b01ad0061c2fea646
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "43770959"
---
# <a name="quickstart-get-sentence-lengths-with-nodejs"></a>Snabbstart: Hämta meningslängden med Node.js

I den här snabbstarten ska du hämta längden på meningar i text med hjälp av Translator Text-API:et.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver [Node.js 6](https://nodejs.org/en/download/) för att köra den här koden.

För att använda Translator Text-API:et behöver du också en prenumerationsnyckel. Mer information finns i [How to sign up for the Translator Text API](translator-text-how-to-signup.md) (Så här registrerar du dig för Translator Text-API:et).

## <a name="breaksentence-request"></a>BreakSentence-begäran

Följande kod delar in källtexten i meningar med hjälp av metoden [BreakSentence](./reference/v3-0-break-sentence.md).

1. Skapa ett nytt Node.js-projekt i valfri kodredigerare.
2. Lägg till koden nedan.
3. Ersätt värdet `subscriptionKey` med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```javascript
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

let host = 'api.cognitive.microsofttranslator.com';
let path = '/breaksentence?api-version=3.0';

let params = '';

let text = 'How are you? I am fine. What did you do today?';

let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let json = JSON.stringify(JSON.parse(body), null, 4);
        console.log(json);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

let get_guid = function () {
  return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function(c) {
    var r = Math.random() * 16 | 0, v = c == 'x' ? r : (r & 0x3 | 0x8);
    return v.toString(16);
  });
}

let BreakSentences = function (content) {
    let request_params = {
        method : 'POST',
        hostname : host,
        path : path + params,
        headers : {
            'Content-Type' : 'application/json',
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
            'X-ClientTraceId' : get_guid (),
        }
    };

    let req = https.request (request_params, response_handler);
    req.write (content);
    req.end ();
}

let content = JSON.stringify ([{'Text' : text}]);

BreakSentences (content);
```

## <a name="breaksentence-response"></a>BreakSentence-svar

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel:

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "sentLen": [
      13,
      11,
      22
    ]
  }
]
```

## <a name="next-steps"></a>Nästa steg

Utforska exempelkoden för den här snabbstarten och andra, inklusive översättning och transkribering, samt andra Translator Text-exempelprojekt på GitHub.

> [!div class="nextstepaction"]
> [Utforska Node.js-exempel på GitHub](https://aka.ms/TranslatorGitHub?type=&language=javascript)
