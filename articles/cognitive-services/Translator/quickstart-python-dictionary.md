---
title: 'Snabbstart: Hitta alternativa översättningar – Translator Text, Python'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten hittar du alternativa översättningar och exempel på termer i ett sammanhang med Translator Text-API:et med Python.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/21/2018
ms.author: nolachar
ms.openlocfilehash: 9035f718dda4cd1a8b34aa72272cfa376be99cac
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46122730"
---
# <a name="quickstart-find-alternate-translations-and-usage-with-python"></a>Snabbstart: Hitta alternativa översättningar och alternativ användning med Python

I den här snabbstarten hittar du information om möjliga alternativa översättningar för en term, och även användningsexempel för de alternativa översättningarna, med hjälp av Translator Text-API:et.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver [Python 3.x](https://www.python.org/downloads/) för att köra koden.

För att använda Translator Text-API:et behöver du också en prenumerationsnyckel. Mer information finns i [How to sign up for the Translator Text API](translator-text-how-to-signup.md) (Så här registrerar du dig för Translator Text-API:et).

## <a name="dictionary-lookup-request"></a>Begäran om att slå upp i ordlista

Följande hämtar alternativa översättningar för ett or med hjälp av metoden [Dictionary Lookup](./reference/v3-0-dictionary-lookup.md) (Slå upp i ordlista).

1. Skapa ett Python-projekt i valfri kodredigerare.
2. Lägg till koden nedan.
3. Ersätt värdet `subscriptionKey` med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, uuid, json

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'api.cognitive.microsofttranslator.com'
path = '/dictionary/lookup?api-version=3.0'

params = '&from=en&to=fr';

text = 'great'

def lookup (content):

    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-type': 'application/json',
        'X-ClientTraceId': str(uuid.uuid4())
    }

    conn = http.client.HTTPSConnection(host)
    conn.request ("POST", path + params, content, headers)
    response = conn.getresponse ()
    return response.read ()

requestBody = [{
    'Text' : text,
}]
content = json.dumps(requestBody, ensure_ascii=False).encode('utf-8')
result = lookup (content)

# Note: We convert result, which is JSON, to and from an object so we can pretty-print it.
# We want to avoid escaping any Unicode characters that result contains. See:
# https://stackoverflow.com/questions/18337407/saving-utf-8-texts-in-json-dumps-as-utf8-not-as-u-escape-sequence
output = json.dumps(json.loads(result), indent=4, ensure_ascii=False)

print (output)
```

## <a name="dictionary-lookup-response"></a>Svar på begäran om att slå upp i ordlista

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel:

```json
[
  {
    "normalizedSource": "great",
    "displaySource": "great",
    "translations": [
      {
        "normalizedTarget": "grand",
        "displayTarget": "grand",
        "posTag": "ADJ",
        "confidence": 0.2783,
        "prefixWord": "",
        "backTranslations": [
          {
            "normalizedText": "great",
            "displayText": "great",
            "numExamples": 15,
            "frequencyCount": 34358
          },
          {
            "normalizedText": "big",
            "displayText": "big",
            "numExamples": 15,
            "frequencyCount": 21770
          },
...
        ]
      },
      {
        "normalizedTarget": "super",
        "displayTarget": "super",
        "posTag": "ADJ",
        "confidence": 0.1514,
        "prefixWord": "",
        "backTranslations": [
          {
            "normalizedText": "super",
            "displayText": "super",
            "numExamples": 15,
            "frequencyCount": 12023
          },
          {
            "normalizedText": "great",
            "displayText": "great",
            "numExamples": 15,
            "frequencyCount": 10931
          },
...
        ]
      },
...
    ]
  }
]
```

## <a name="dictionary-examples-request"></a>Begäran om ordlisteexempel

Följande hämtar sammanhangsbaserade exempel på hur du använder en term i en ordlista med hjälp av metoden [Dictionary Examples](./reference/v3-0-dictionary-examples.md) (Ordlisteexempel).

1. Skapa ett Python-projekt i valfri kodredigerare.
2. Lägg till koden nedan.
3. Ersätt värdet `subscriptionKey` med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, uuid, json

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'api.cognitive.microsofttranslator.com'
path = '/dictionary/examples?api-version=3.0'

params = '&from=en&to=fr';

text = 'great'
translation = 'formidable'

def examples (content):

    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-type': 'application/json',
        'X-ClientTraceId': str(uuid.uuid4())
    }

    conn = http.client.HTTPSConnection(host)
    conn.request ("POST", path + params, content, headers)
    response = conn.getresponse ()
    return response.read ()

requestBody = [{
    'Text' : text,
    'Translation' : translation,
}]
content = json.dumps(requestBody, ensure_ascii=False).encode('utf-8')
result = examples (content)

# Note: We convert result, which is JSON, to and from an object so we can pretty-print it.
# We want to avoid escaping any Unicode characters that result contains. See:
# https://stackoverflow.com/questions/18337407/saving-utf-8-texts-in-json-dumps-as-utf8-not-as-u-escape-sequence
output = json.dumps(json.loads(result), indent=4, ensure_ascii=False)

print (output)
```

## <a name="dictionary-examples-response"></a>Svar på ordlisteexempel

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel:

```json
[
  {
    "normalizedSource": "great",
    "normalizedTarget": "formidable",
    "examples": [
      {
        "sourcePrefix": "You have a ",
        "sourceTerm": "great",
        "sourceSuffix": " expression there.",
        "targetPrefix": "Vous avez une expression ",
        "targetTerm": "formidable",
        "targetSuffix": "."
      },
      {
        "sourcePrefix": "You played a ",
        "sourceTerm": "great",
        "sourceSuffix": " game today.",
        "targetPrefix": "Vous avez été ",
        "targetTerm": "formidable",
        "targetSuffix": "."
      },
...
    ]
  }
]
```

## <a name="next-steps"></a>Nästa steg

Utforska exempelkoden för den här snabbstarten och andra, inklusive översättning och transkribering, samt andra Translator Text-exempelprojekt på GitHub.

> [!div class="nextstepaction"]
> [Utforska Python-exempel på GitHub](https://aka.ms/TranslatorGitHub?type=&language=python)
