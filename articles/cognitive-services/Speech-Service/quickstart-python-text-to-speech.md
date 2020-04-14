---
title: Konvertera text till tal, Python - Taltjänst
titleSuffix: Azure Cognitive Services
description: I den här artikeln får du lära dig hur du konverterar text till tal med Python och REST-API:et för text-till-tal. Exempeltexten som ingår i den här guiden är strukturerad som SSML (Speech Synthesis Markup Language). På så sätt kan du välja röst och språk för talsvaret.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: how-to
ms.date: 04/13/2020
ms.author: dapine
ms.openlocfilehash: 6ea3f63bea0701dcd0f4b38f6584816d9a1bfbc7
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258446"
---
# <a name="convert-text-to-speech-using-python"></a>Konvertera text till tal med Python

I den här artikeln får du lära dig hur du konverterar text till tal med Python och REST-API:et för text till tal. Förfrågastexten i den här guiden är strukturerad som [SSML (Speech Synthesis Markup Language),](speech-synthesis-markup.md)vilket gör att du kan välja röst och språk för svaret.

Den här artikeln kräver ett [Azure Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med en taltjänstresurs. Om du inte har ett konto kan du använda den [kostnadsfria utvärderingsversionen](get-started.md) för att hämta en prenumerationsnyckel.

## <a name="prerequisites"></a>Krav

* Python 2.7.x eller 3.x
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual <span class="docon docon-navigate-external x-hidden-focus"> </span>Studio, </a> <a href="https://code.visualstudio.com/download" target="_blank">Visual <span class="docon docon-navigate-external x-hidden-focus"> </span>Studio-kod </a>eller din favorittextredigerare
* En Azure-prenumerationsnyckel för taltjänsten

## <a name="create-a-project-and-import-required-modules"></a>Skapa ett projekt och importera nödvändiga moduler

Skapa ett nytt Python-projekt med valfri IDE eller redigeringsprogram. Kopiera sedan det här kodavsnittet till projektet i en fil med namnet `tts.py`.

```python
import os
import requests
import time
from xml.etree import ElementTree
```

> [!NOTE]
> Om du inte har använt de här modulerna behöver du installera dem innan du kör programmet. För att installera de här paketen kör du: `pip install requests`.

Dessa moduler används för att skriva talsvaret till en fil med en tidsstämpel, konstruera HTTP-begäran och anropa API:et för text-till-tal.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Ange prenumerationsnyckeln och skapa en fråga för TTS

I de närmaste avsnitten ska du skapa metoder för att hantera auktorisering, anropa API:et för text till tal och validera svaret. Låt oss börja med att lägga till en kod som ser till att det här exemplet fungerar med Python 2.7.x och 3.x.

```python
try:
    input = raw_input
except NameError:
    pass
```

Därefter ska vi skapa en klass. Det är här vi lägger våra metoder för tokenutbyte och anropar API:et för text-till-tal.

```python
class TextToSpeech(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.tts = input("What would you like to convert to speech: ")
        self.timestr = time.strftime("%Y%m%d-%H%M")
        self.access_token = None
```

Det `subscription_key` är din unika nyckel från Azure-portalen. `tts`uppmanas användaren att skriva text som ska konverteras till tal. Den här indata är en stränglitteral, så tecken behöver inte fly. Slutligen `timestr` får den aktuella tiden, som vi använder för att namnge din fil.

## <a name="get-an-access-token"></a>Hämta en åtkomsttoken

REST API för text-till-tal kräver en åtkomsttoken för autentisering. För att få en åtkomsttoken krävs ett utbyte. Det här exemplet utbyter prenumerationsnyckeln `issueToken` för taltjänsten för en åtkomsttoken med slutpunkten.

Det här exemplet förutsätter att prenumerationen på taltjänsten finns i regionen västra USA. Om du använder en annan region uppdaterar `fetch_token_url`du värdet för . En fullständig lista finns i [Regioner](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Kopiera den här `TextToSpeech` koden till klassen:

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

Här ska du skapa begäran och spara talsvaret. Först måste du ställa `base_url` `path`in och . Det här exemplet förutsätter att du använder slutpunkten för västra USA. Om resursen är registrerad i en annan `base_url`region kontrollerar du att du uppdaterar . Mer information finns i [Taltjänstområden](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Därefter måste du lägga till obligatoriska rubriker för begäran. Se till att `User-Agent` du uppdaterar med namnet på din resurs `X-Microsoft-OutputFormat` (finns i Azure-portalen) och ange din önskade ljudutdata. En fullständig lista över utdataformat finns i [Ljudutdata](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Konstruera sedan begärandetexten med hjälp av SSML (Speech Synthesis Markup Language). Det här exemplet definierar strukturen `tts` och använder indata som du skapade tidigare.

>[!NOTE]
> I det `Guy24kRUS` här exemplet används röstteckensnittet. En fullständig lista över röster/språk som tillhandahålls av Microsoft finns i [Språkstöd](language-support.md).
> Om du är intresserad av att skapa en unik, igenkännbar röst för ditt varumärke läser [du Skapa anpassade röstteckensnitt](how-to-customize-voice-font.md).

Slutligen ska du göra en begäran till tjänsten. Om begäran lyckas och en 200-statuskod returneras skrivs talsvaret till en tidsstämplad fil.

Kopiera den här `TextToSpeech` koden till klassen:

```python
def save_audio(self):
    base_url = 'https://westus.tts.speech.microsoft.com/'
    path = 'cognitiveservices/v1'
    constructed_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token,
        'Content-Type': 'application/ssml+xml',
        'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
        'User-Agent': 'YOUR_RESOURCE_NAME'
    }
    xml_body = ElementTree.Element('speak', version='1.0')
    xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
    voice = ElementTree.SubElement(xml_body, 'voice')
    voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
    voice.set(
        'name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)')
    voice.text = self.tts
    body = ElementTree.tostring(xml_body)

    response = requests.post(constructed_url, headers=headers, data=body)
    if response.status_code == 200:
        with open('sample-' + self.timestr + '.wav', 'wb') as audio:
            audio.write(response.content)
            print("\nStatus code: " + str(response.status_code) +
                  "\nYour TTS is ready for playback.\n")
    else:
        print("\nStatus code: " + str(response.status_code) +
              "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>Färdigställa allt

Nästan klart. Det sista steget är att instansiera din klass och ringa dina funktioner.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = TextToSpeech(subscription_key)
    app.get_token()
    app.save_audio()
```

## <a name="run-the-sample-app"></a>Kör exempelappen

Det är allt, du är redo att köra din text-till-tal exempelapp. Från kommandoraden (eller en terminalsession) går du till projektkatalogen och kör:

```console
python tts.py
```

När du uppmanas till det skriver du in det du vill konvertera från text till tal. Om det lyckas finns talfilen i projektmappen. Spela upp den med din favorit mediaspelare.

## <a name="clean-up-resources"></a>Rensa resurser

Se till att ta bort all konfidentiell information från exempelappens källkod, till exempel prenumerationsnycklar.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska Python-exempel på GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Se även

* [Referens för text till tal-API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Använda Python och Speech SDK för att konvertera text-till-tal](quickstarts/speech-to-text-from-microphone.md)
* [Skapa anpassade röstteckensnitt](how-to-customize-voice-font.md)
* [Spela in röstexempel för att skapa en anpassad röst](record-custom-voice-samples.md)
