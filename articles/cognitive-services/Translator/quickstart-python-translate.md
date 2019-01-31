---
title: 'Snabbstart: Översätta text, Python – Translator Text API'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten översätter du text från ett språk till ett annat med hjälp av Translator Text API med Python på mindre än 10 minuter.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 10/17/2018
ms.author: erhopf
ms.openlocfilehash: b5307db01cc7242ba73876409f18d511c2516091
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55458167"
---
# <a name="quickstart-use-the-translator-text-api-to-translate-a-string-using-python"></a>Snabbstart: Använda Translator Text API för att översätta en sträng med hjälp av Python

I den här snabbstarten lär du dig att översätta en textsträng från engelska till italienska och tyska med hjälp av Python och Translator Text REST API.

För den här snabbstarten krävs ett [Azure Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med en Translator Text-resurs. Om du inte har ett konto kan du använda den [kostnadsfria utvärderingsversionen](https://azure.microsoft.com/try/cognitive-services/) för att hämta en prenumerationsnyckel.

## <a name="prerequisites"></a>Nödvändiga komponenter

För den här snabbstarten krävs:

* Python 2.7.x eller 3.x
* En Azure-prenumerationsnyckel för Translator Text

## <a name="create-a-project-and-import-required-modules"></a>Skapa ett projekt och importera nödvändiga moduler

Skapa ett nytt Python-projekt med valfri IDE eller redigeringsprogram. Kopiera sedan det här kodavsnittet till projektet i en fil med namnet `translate-text.py`.

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Om du inte har använt de här modulerna behöver du installera dem innan du kör programmet. För att installera de här paketen kör du: `pip install requests uuid`.

Den första kommentaren instruerar Python-tolken att använda UTF-8-kodning. Sedan importeras de moduler som krävs för att läsa prenumerationsnyckeln från en miljövariabel, skapa HTTP-begäran, skapa en unik identifierare samt hantera det JSON-svar som returneras av Translator Text API.

## <a name="set-the-subscription-key-base-url-and-path"></a>Ange prenumerationsnyckeln, bas-URL och sökvägen

Det här exemplet kommer att försöka läsa din Translator Text-prenumerationsnyckel från miljövariabeln `TRANSLATOR_TEXT_KEY`. Om du inte känner till miljövariabler kan du ange `subscriptionKey` som en sträng och kommentera bort den villkorliga instruktionen.

Kopiera den här koden till projektet:

```python
# Checks to see if the Translator Text subscription key is available
# as an environment variable. If you are setting your subscription key as a
# string, then comment these lines out.
if 'TRANSLATOR_TEXT_KEY' in os.environ:
    subscriptionKey = os.environ['TRANSLATOR_TEXT_KEY']
else:
    print('Environment variable for TRANSLATOR_TEXT_KEY is not set.')
    exit()
# If you want to set your subscription key as a string, uncomment the line
# below and add your subscription key.
#subscriptionKey = 'put_your_key_here'
```

För närvarande är en slutpunkt tillgänglig för Translator Text, och den anges som `base_url`. `path` anger `translate`-vägen och identifierar att vi vill nå version 3 av API:et.

`params` används till att ange utdataspråken. I det här exemplet översätter vi från engelska till italienska och tyska: `it` och `de`.

>[!NOTE]
> Mer information om slutpunkter, vägar och att begära parametrar finns i [Translator Text API 3.0: Översätt](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate).

```python
base_url = 'https://api.cognitive.microsofttranslator.com'
path = '/translate?api-version=3.0'
params = '&to=de&to=it'
constructed_url = base_url + path + params
```

## <a name="add-headers"></a>Lägga till sidhuvuden

Det enklaste sättet att autentisera en begäran är att skicka din prenumerationsnyckel som ett `Ocp-Apim-Subscription-Key`-sidhuvud, vilket är det vi använder i det här exemplet. Alternativt kan du byta din prenumerationsnyckel mot en åtkomsttoken och skicka vidare åtkomsttoken som ett `Authorization`-sidhuvud för att verifiera din begäran. Mer information finns i [Autentisering](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

Kopiera det här kodavsnittet till projektet:

```python
headers = {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

## <a name="create-a-request-to-translate-text"></a>Skapa en begäran om att översätta text

Definiera den sträng (eller de strängar) som du vill översätta:

```python
body = [{
    'text' : 'Hello World!'
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
print(json.dumps(response, sort_keys=True, indent=4, ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>Färdigställa allt

Det var allt – du har skapat ett enkelt program som anropar Translator Text API och returnerar en JSON-svar. Nu är det dags att köra programmet:

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

> [!div class="nextstepaction"]
> [Utforska Python-exempel på GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python)

## <a name="see-also"></a>Se även

Lär dig att använda Translator Text API till att:

* [Translitterera text](quickstart-python-transliterate.md)
* [Identifiera språket efter indata](quickstart-python-detect.md)
* [Hämta alternativa översättningar](quickstart-python-dictionary.md)
* [Hämta en lista över språk som stöds](quickstart-python-languages.md)
* [Fastställa meningslängd utifrån indata](quickstart-python-sentences.md)
