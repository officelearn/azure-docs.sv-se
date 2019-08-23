---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 7f14a12d5de64206f64e8c7205beb2c59c4f1f2a
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906920"
---
[!INCLUDE [Prerequisites](prerequisites-python.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Skapa ett projekt och importera nödvändiga moduler

Skapa ett nytt Python-projekt med valfri IDE eller redigeringsprogram. Kopiera sedan det här kodavsnittet till projektet i en fil med namnet `get-languages.py`.

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Om du inte har använt de här modulerna behöver du installera dem innan du kör programmet. För att installera de här paketen kör du: `pip install requests uuid`.

Den första kommentaren instruerar Python-tolken att använda UTF-8-kodning. Sedan importeras de moduler som krävs för att läsa prenumerationsnyckeln från en miljövariabel, skapa HTTP-begäran, skapa en unik identifierare samt hantera det JSON-svar som returneras av Translator Text API.

## <a name="set-the-endpoint-and-path"></a>Ange slut punkt och sökväg

Det här exemplet försöker läsa Translator Text slut punkten från en miljö variabel: `TRANSLATOR_TEXT_ENDPOINT`. Om du inte känner till miljövariabler kan du ange `endpoint` som en sträng och kommentera bort den villkorliga instruktionen.

```python
endpoint_var_name = 'TRANSLATOR_TEXT_ENDPOINT'
if not endpoint_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(endpoint_var_name))
endpoint = os.environ[endpoint_var_name]
```

Den globala slutpunkten för Translator Text anges som `endpoint`. `path` anger `languages`-vägen och identifierar att vi vill nå version 3 av API:et.

>[!NOTE]
> Mer information om slutpunkter, vägar och begärandeparametrar finns i [Translator Text API 3.0: Språk](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

```python
path = '/languages?api-version=3.0'
constructed_url = endpoint + path
```

## <a name="add-headers"></a>Lägga till sidhuvuden

Begäran om att hämta språk som stöds kräver inte autentisering. Ställ in `Content-type` på `application/json` och lägg till `X-ClientTraceId` för att identifiera din begäran.

Kopiera det här kodavsnittet till projektet:

```python
headers = {
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

Om du använder en Cognitive Services-prenumeration med flera tjänster måste du också ta `Ocp-Apim-Subscription-Region` med i parametrarna för begäran. [Lär dig mer om att autentisera med multi-service](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)-prenumerationen.

## <a name="create-a-request-to-get-a-list-of-supported-languages"></a>Skapa en begäran för att hämta en lista över språk som stöds

Nu ska vi skapa en GET-begäran med `requests`-modulen. Två argument krävs: den sammansatta URL:en och begärandehuvuden:

```python
request = requests.get(constructed_url, headers=headers)
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
python get-languages.py
```

Om du vill jämföra din kod med vår finns det fullständiga exemplet på [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

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

Ta en titt på API-referensen för att förstå allt du kan göra med Translator Text API.

> [!div class="nextstepaction"]
> [API-referens](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
