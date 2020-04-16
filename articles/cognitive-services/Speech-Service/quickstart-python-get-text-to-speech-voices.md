---
title: Lista text-till-tal-röster, Python - Taltjänst
titleSuffix: Azure Cognitive Services
description: I den här artikeln får du lära dig hur du får en fullständig lista över standardröster och neurala röster för en region/slutpunkt med Python. Listan returneras som JSON och rösttillgängligheten varierar beroende på region.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: how-to
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: b388c8d8b61e2fc638ae2bce5bc6d9eeb25ee0d4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401021"
---
# <a name="get-the-list-of-text-to-speech-voices-using-python"></a>Hämta listan över text-till-tal-röster med Python

I den här artikeln får du lära dig hur du får en fullständig lista över standardröster och neurala röster för en region/slutpunkt med Python. Listan returneras som JSON och rösttillgängligheten varierar beroende på region. En lista över regioner som stöds finns i [regioner](regions.md).

Den här artikeln kräver ett [Azure Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med en taltjänstresurs. Om du inte har ett konto kan du använda den [kostnadsfria utvärderingsversionen](get-started.md) för att hämta en prenumerationsnyckel.

## <a name="prerequisites"></a>Krav

* Python 2.7.x eller 3.x
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual <span class="docon docon-navigate-external x-hidden-focus"> </span>Studio, </a> <a href="https://code.visualstudio.com/download" target="_blank">Visual <span class="docon docon-navigate-external x-hidden-focus"> </span>Studio-kod </a>eller din favorittextredigerare
* En Azure-prenumerationsnyckel för taltjänsten

## <a name="create-a-project-and-import-required-modules"></a>Skapa ett projekt och importera nödvändiga moduler

Skapa ett nytt Python-projekt med valfri IDE eller redigeringsprogram. Kopiera sedan det här kodavsnittet till projektet i en fil med namnet `get-voices.py`.

```python
import requests
```

> [!NOTE]
> Om du inte har använt de här modulerna behöver du installera dem innan du kör programmet. För att installera de här paketen kör du: `pip install requests`.

Begäranden används för HTTP-begäranden till text-till-tal-tjänsten.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Ange prenumerationsnyckeln och skapa en fråga för TTS

I de närmaste avsnitten ska du skapa metoder för att hantera auktorisering, anropa API:et för text till tal och validera svaret. Låt oss börja med att skapa en klass. Det är här vi lägger våra metoder för tokenutbyte och anropar API:et för text-till-tal.

```python
class GetVoices(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.access_token = None
```

Det `subscription_key` är din unika nyckel från Azure-portalen.

## <a name="get-an-access-token"></a>Hämta en åtkomsttoken

Den här slutpunkten kräver en åtkomsttoken för autentisering. För att få en åtkomsttoken krävs ett utbyte. Det här exemplet utbyter prenumerationsnyckeln `issueToken` för taltjänsten för en åtkomsttoken med slutpunkten.

Det här exemplet förutsätter att prenumerationen på taltjänsten finns i regionen västra USA. Om du använder en annan region uppdaterar `fetch_token_url`du värdet för . En fullständig lista finns i [Regioner](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Kopiera den här `GetVoices` koden till klassen:

```python
def get_token(self):
    fetch_token_url = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    headers = {
        'Ocp-Apim-Subscription-Key': self.subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    self.access_token = str(response.text)
```

> [!NOTE]
> Mer information om autentisering finns [i Autentisera med en åtkomsttoken](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="make-a-request-and-save-the-response"></a>Gör en begäran och spara svaret

Här ska du skapa begäran och spara listan över returnerade röster. Först måste du ställa `base_url` `path`in och . Det här exemplet förutsätter att du använder slutpunkten för västra USA. Om resursen är registrerad i en annan `base_url`region kontrollerar du att du uppdaterar . Mer information finns i [Taltjänstområden](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Lägg sedan till obligatoriska rubriker för begäran. Slutligen ska du göra en begäran till tjänsten. Om begäran lyckas och en 200-statuskod returneras skrivs svaret till filen.

Kopiera den här `GetVoices` koden till klassen:

```python
def get_voices(self):
    base_url = 'https://westus.tts.speech.microsoft.com'
    path = '/cognitiveservices/voices/list'
    get_voices_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token
    }
    response = requests.get(get_voices_url, headers=headers)
    if response.status_code == 200:
        with open('voices.json', 'wb') as voices:
            voices.write(response.content)
            print("\nStatus code: " + str(response.status_code) +
                  "\nvoices.json is ready to view.\n")
    else:
        print("\nStatus code: " + str(
            response.status_code) + "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>Färdigställa allt

Nästan klart. Det sista steget är att instansiera din klass och ringa dina funktioner.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = GetVoices(subscription_key)
    app.get_token()
    app.get_voices()
```

## <a name="run-the-sample-app"></a>Kör exempelappen

Det är allt, du är redo att köra provet. Från kommandoraden (eller en terminalsession) går du till projektkatalogen och kör:

```console
python get-voices.py
```

## <a name="clean-up-resources"></a>Rensa resurser

Se till att ta bort all konfidentiell information från exempelappens källkod, till exempel prenumerationsnycklar.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska Python-exempel på GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Se även

* [Referens för text till tal-API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Skapa anpassade röstteckensnitt](how-to-customize-voice-font.md)
* [Spela in röstexempel för att skapa en anpassad röst](record-custom-voice-samples.md)
