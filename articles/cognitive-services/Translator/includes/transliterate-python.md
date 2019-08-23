---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 86ef8f3730fe7ae3ab3428956aaafb86331c5cf5
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906534"
---
[!INCLUDE [Prerequisites](prerequisites-python.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Skapa ett projekt och importera nödvändiga moduler

Skapa ett nytt projekt med hjälp av din favorit-IDE eller-redigerare eller en ny mapp med `transliterate-text.py` en fil med namnet på Skriv bordet. Kopiera sedan kodfragmentet till projektet/filen:

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Om du inte har använt de här modulerna behöver du installera dem innan du kör programmet. För att installera de här paketen kör du: `pip install requests uuid`.

Den första kommentaren instruerar Python-tolken att använda UTF-8-kodning. Sedan importeras de moduler som krävs för att läsa prenumerationsnyckeln från en miljövariabel, skapa HTTP-begäran, skapa en unik identifierare samt hantera det JSON-svar som returneras av Translator Text API.

## <a name="set-the-subscription-key-endpoint-and-path"></a>Ange prenumerations nyckel, slut punkt och sökväg

Det här exemplet försöker läsa Translator text prenumerations nyckel och slut punkt från miljövariablerna: `TRANSLATOR_TEXT_KEY` och `TRANSLATOR_TEXT_ENDPOINT`. Om du inte är bekant med miljövariabler kan du ange `subscription_key` och `endpoint` som en sträng och kommentera ut villkors satserna.

Kopiera den här koden till projektet:

```python
key_var_name = 'TRANSLATOR_TEXT_SUBSCRIPTION_KEY'
if not key_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(key_var_name))
subscription_key = os.environ[key_var_name]

endpoint_var_name = 'TRANSLATOR_TEXT_ENDPOINT'
if not endpoint_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(endpoint_var_name))
endpoint = os.environ[endpoint_var_name]
```

Den globala slutpunkten för Translator Text anges som `endpoint`. `path` anger `transliterate`-vägen och identifierar att vi vill nå version 3 av API:et.

`params` används för att ange indataspråk och indata- och utdataskript. I det här exemplet translittereras japanska med det latinska alfabetet.

>[!NOTE]
> Mer information om slutpunkter, vägar och att begära parametrar finns i [Translator Text API 3.0: Transkribera](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-transliterate).

```python
path = '/transliterate?api-version=3.0'
params = '&language=ja&fromScript=jpan&toScript=latn'
constructed_url = endpoint + path + params
```

## <a name="add-headers"></a>Lägga till sidhuvuden

Det enklaste sättet att autentisera en begäran är att skicka din prenumerationsnyckel som ett `Ocp-Apim-Subscription-Key`-sidhuvud, vilket är det vi använder i det här exemplet. Alternativt kan du byta din prenumerationsnyckel mot en åtkomsttoken och skicka vidare åtkomsttoken som ett `Authorization`-sidhuvud för att verifiera din begäran. Mer information finns i [Autentisering](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

Kopiera det här kodavsnittet till projektet:

```python
headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

Om du använder en Cognitive Services-prenumeration med flera tjänster måste du också ta `Ocp-Apim-Subscription-Region` med i parametrarna för begäran. [Lär dig mer om att autentisera med multi-service](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)-prenumerationen.

## <a name="create-a-request-to-transliterate-text"></a>Skapa en begäran för att transkribera text

Definiera den sträng (eller de strängar) som du vill transkribera:

```python
# Transliterate "good afternoon" from source Japanese.
# Note: You can pass more than one object in body.
body = [{
    'text': 'こんにちは'
}]
```

Nu skapar vi en POST-begäran med hjälp av modulen `requests`. Den tar tre argument: den sammanfogade URL:en, begärandesidhuvudena samt begärandetexten:

```python
request = requests.post(constructed_url, headers=headers, json=body)
response = request.json()
```

## <a name="print-the-response"></a>Skriva ut svaret

Det sista steget är att skriva ut resultatet. Det här kodavsnittet gör resultatet prydligare genom att sortera nycklarna, konfigurera indrag samt deklarera objekt- och nyckelseparatorer.

```python
print(json.dumps(response, sort_keys=True, indent=4,
                 ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>Färdigställa allt

Det var allt – du har skapat ett enkelt program som anropar Translator Text API och returnerar en JSON-svar. Nu är det dags att köra programmet:

```console
python transliterate-text.py
```

Om du vill jämföra din kod med vår finns det fullständiga exemplet på [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="sample-response"></a>Exempelsvar

```json
[
    {
        "script": "latn",
        "text": "konnichiwa"
    }
]
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du har hårdkodat din prenumerationsnyckel i programmet ser du till att ta bort prenumerationsnyckeln när du är klar med den här snabbstarten.

## <a name="next-steps"></a>Nästa steg

Ta en titt på API-referensen för att förstå allt du kan göra med Translator Text API.

> [!div class="nextstepaction"]
> [API-referens](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
