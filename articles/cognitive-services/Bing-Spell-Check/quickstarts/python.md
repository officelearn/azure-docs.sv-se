---
title: 'Snabb start: kontrol lera stavningen med REST API och python-Stavningskontroll i Bing'
titleSuffix: Azure Cognitive Services
description: Kom igång med Stavningskontroll i Bing REST API och python för att kontrol lera stavning och grammatik.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: 27e5a336b0a7e6fa0e47e20ad0d3c6204cef7757
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352668"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-python"></a>Snabb start: kontrol lera stavningen med Stavningskontroll i Bing REST API och python

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Använd den här snabbstarten för att göra ditt första anrop till REST API för stavningskontroll i Bing. Det här enkla Python-programmet skickar en begäran till API:et och returnerar en lista över föreslagna korrigeringar. 

Även om det här programmet är skrivet i python är API: et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk. Källkoden till det här programmet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py)

## <a name="prerequisites"></a>Förutsättningar

* Python [3. x](https://www.python.org)

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="initialize-the-application"></a>Initiera programmet

1. Skapa en ny python-fil i din favorit-IDE eller-redigerare och Lägg till följande import uttryck:

   ```python
   import requests
   import json
   ```

2. Skapa variabler för den text som du vill stavningskontrollera, prenumerationsnyckeln och slutpunkten för stavningskontroll i Bing. Du kan använda den globala slut punkten i följande kod eller använda den [anpassade slut domänen](../../../cognitive-services/cognitive-services-custom-subdomains.md) som visas i Azure Portal för din resurs.

    ```python
    api_key = "<ENTER-KEY-HERE>"
    example_text = "Hollo, wrld" # the text to be spell-checked
    endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck"
    ```

## <a name="create-the-parameters-for-the-request"></a>Skapa parametrarna för begäran

1. Skapa en ny ordlista med `text` som nyckel och texten som värde.

    ```python
    data = {'text': example_text}
    ```

2. Lägg till parametrarna för din begäran: 

   1. Tilldela din marknads kod till- `mkt` parametern med `=` operatorn. Marknads koden är koden för landet/regionen som du gör begäran från. 

   1. Lägg till `mode` parametern med `&` operatorn och tilldela sedan läget för stavnings kontroll. Läget kan vara antingen `proof` (fångar de flesta stavfel/grammatikfel) eller `spell` (fångar de flesta stavfel, men inte lika många grammatiska fel). 
 
    ```python
    params = {
        'mkt':'en-us',
        'mode':'proof'
        }
    ```

3. Lägg till ett `Content-Type` sidhuvud och din prenumerations nyckel i `Ocp-Apim-Subscription-Key` rubriken.

    ```python
    headers = {
        'Content-Type': 'application/x-www-form-urlencoded',
        'Ocp-Apim-Subscription-Key': api_key,
        }
    ```

## <a name="send-the-request-and-read-the-response"></a>Skicka begäran och läsa svaret

1. Skicka POST-begäran med hjälp av begär ande biblioteket.

    ```python
    response = requests.post(endpoint, headers=headers, params=params, data=data)
    ```

2. Hämta JSON-svaret och skriv ut det.

    ```python
    json_response = response.json()
    print(json.dumps(json_response, indent=4))
    ```


## <a name="run-the-application"></a>Kör programmet

Om du använder kommando raden använder du följande kommando för att köra programmet:

```bash
python <FILE_NAME>.py
```

## <a name="example-json-response"></a>Exempel på JSON-svar

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel:

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en enkelsidig webbapp](../tutorials/spellcheck.md)

- [Vad är API för stavningskontroll i Bing?](../overview.md)
- [API för stavningskontroll i Bing v7-referens](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)