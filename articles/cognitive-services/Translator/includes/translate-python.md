---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 217bf672761d5e5dfb2e9112266dc3381b96e3fe
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586679"
---
[!INCLUDE [Prerequisites](prerequisites-python.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Skapa ett projekt och importera nödvändiga moduler

Skapa ett nytt Python-projekt med valfri IDE eller redigeringsprogram. Kopiera sedan det här kodavsnittet till projektet i en fil med namnet `translate-text.py`. Se till att IDE-tolken refererar till rätt version av python för att undvika bibliotek som inte känns igen.

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Om du inte har använt de här modulerna behöver du installera dem innan du kör programmet. För att installera de här paketen kör du: `pip install requests uuid`.

Den första kommentaren instruerar Python-tolken att använda UTF-8-kodning. Sedan importeras de nödvändiga modulerna för att läsa prenumerations nyckeln från en miljö variabel, skapa http-begäran, skapa en unik identifierare och hantera JSON-svaret som returnerades av Translator.

## <a name="set-the-subscription-key-endpoint-and-path"></a>Ange prenumerations nyckel, slut punkt och sökväg

I det här exemplet görs ett försök att läsa prenumerations nyckeln och slut punkten för din översättare från miljövariablerna: `TRANSLATOR_TEXT_KEY` och `TRANSLATOR_TEXT_ENDPOINT` . Om du inte är bekant med miljövariabler kan du ange `subscription_key` och `endpoint` som en sträng och kommentera ut villkors satserna.

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

Den globala slut punkten för översättare har angetts som `endpoint` . `path` anger `translate`-vägen och identifierar att vi vill nå version 3 av API:et.

`params` används till att ange utdataspråken. I det här exemplet översätter vi från engelska till italienska och tyska: `it` och `de`.

>[!NOTE]
> Mer information om slut punkter, vägar och parametrar för begäran finns i [Translator 3,0: Översätt](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate).

```python
path = '/translate?api-version=3.0'
params = '&to=de&to=it'
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

Om du använder en Cognitive Services-prenumeration med flera tjänster måste du också ta med `Ocp-Apim-Subscription-Region` i parametrarna för begäran. [Lär dig mer om att autentisera med multi-service-prenumerationen](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-request-to-translate-text"></a>Skapa en begäran om att översätta text

Definiera den sträng (eller de strängar) som du vill översätta:

```python
body = [{
    'text': 'Hello World!'
}]
```

Nu ska vi skapar en POST-begäran med hjälp av `requests`-modulen. Den tar tre argument: den sammanfogade URL:en, begärandesidhuvudena samt begärandetexten:

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

Det innebär att du har skapat ett enkelt program som anropar översättaren och returnerar ett JSON-svar. Nu är det dags att köra programmet:

```console
python translate-text.py
```

Om du vill jämföra din kod med vår finns det fullständiga exemplet på [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="sample-response"></a>Exempelsvar

```json
[
    {
        "detectedLanguage": {
            "language": "en",
            "score": 1.0
        },
        "translations": [
            {
                "text": "Hallo Welt!",
                "to": "de"
            },
            {
                "text": "Salve, mondo!",
                "to": "it"
            }
        ]
    }
]
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du har hårdkodat din prenumerationsnyckel i programmet ser du till att ta bort prenumerationsnyckeln när du är klar med den här snabbstarten.

## <a name="next-steps"></a>Nästa steg

Ta en titt på API-referensen för att förstå allt du kan göra med Translator.

> [!div class="nextstepaction"]
> [API-referens](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
