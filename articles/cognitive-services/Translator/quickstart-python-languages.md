---
title: 'Snabbstart: Hämta lista över språk som stöds- Python – Translator Text-API'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten hämtar du en lista över språk som stöds för översättning, transkribering och ordlistesökningar med hjälp av Translator Text-API:t med Python.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: e455b0167810c6e08853f3bce665cb6f314eec52
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58181460"
---
# <a name="quickstart-use-the-translator-text-api-to-get-a-list-of-supported-languages-using-python"></a>Snabbstart: Använd Translator Text API för att hämta en lista över språk som stöds med Python

I den här snabbstarten lär du dig hur du gör en GET-begäran som returnerar en lista över språk som stöds med hjälp av Python och Translator Text REST API.

## <a name="prerequisites"></a>Förutsättningar

För den här snabbstarten krävs:

* Python 2.7.x eller 3.x

## <a name="create-a-project-and-import-required-modules"></a>Skapa ett projekt och importera nödvändiga moduler

Skapa ett nytt Python-projekt med valfri IDE eller redigeringsprogram. Kopiera sedan det här kodavsnittet till projektet i en fil med namnet `get-languages.py`.

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Om du inte har använt de här modulerna behöver du installera dem innan du kör programmet. För att installera de här paketen kör du: `pip install requests uuid`.

Den första kommentaren instruerar Python-tolken att använda UTF-8-kodning. Sedan importeras de moduler som krävs för att läsa prenumerationsnyckeln från en miljövariabel, skapa HTTP-begäran, skapa en unik identifierare samt hantera det JSON-svar som returneras av Translator Text API.

## <a name="set-the-base-url-and-path"></a>Ange bas-url och sökvägen

Den globala slutpunkten för Translator Text anges som `base_url`. `path` anger `languages`-vägen och identifierar att vi vill nå version 3 av API:et.

>[!NOTE]
> Mer information om slutpunkter, vägar och begärandeparametrar finns i [Translator Text API 3.0: Språk](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

```python
base_url = 'https://api.cognitive.microsofttranslator.com'
path = '/languages?api-version=3.0'
constructed_url = base_url + path
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

## <a name="create-a-request-to-get-a-list-of-supported-languages"></a>Skapa en begäran för att hämta en lista över språk som stöds

Nu ska vi skapa en GET-begäran med `requests`-modulen. Två argument krävs: den sammansatta URL:en och begärandehuvuden:

```python
request = requests.get(constructed_url, headers=headers)
response = request.json()
```

## <a name="print-the-response"></a>Skriva ut svaret

Det sista steget är att skriva ut resultatet. Det här kodavsnittet gör resultatet prydligare genom att sortera nycklarna, konfigurera indrag samt deklarera objekt- och nyckelseparatorer.

```python
print(json.dumps(response, sort_keys=True, indent=4, ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>Färdigställa allt

Det var allt – du har skapat ett enkelt program som anropar Translator Text API och returnerar en JSON-svar. Nu är det dags att köra programmet:

```console
python get-languages.py
```

Om du vill jämföra din kod med vår finns det fullständiga exemplet på [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="sample-response"></a>Exempelsvar

Hitta en landsförkortning på i den här [Språklista](https://docs.microsoft.com/en-us/azure/cognitive-services/translator/language-support).

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
> [Utforska Python-exempel på GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python)

## <a name="see-also"></a>Se också

Lär dig att använda Translator Text API för att:

* [Översätta text](quickstart-python-translate.md)
* [Translitterera text](quickstart-python-transliterate.md)
* [Identifiera språket efter indata](quickstart-python-detect.md)
* [Hämta alternativa översättningar](quickstart-python-dictionary.md)
* [Fastställa meningslängd utifrån indata](quickstart-python-sentences.md)
