---
title: Omvandla text till tal-, python-tal-tjänst
titleSuffix: Azure Cognitive Services
description: I den här artikeln får du lära dig hur du konverterar text till tal med python och text till tal-REST API. Exempel texten som ingår i den här guiden är strukturerad som SSML (Speech syntes Markup Language). På så sätt kan du välja röst-och språk för tal svaret.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: how-to
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: 171fdb033cba422d8ba580da3ab54db88ca20872
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81400823"
---
# <a name="convert-text-to-speech-using-python"></a>Konvertera text till tal med python

I den här artikeln får du lära dig hur du konverterar text till tal med python och text till tal-REST API. Begär ande texten i den här guiden är strukturerad som [SSML (Speech syntes Markup Language)](speech-synthesis-markup.md), vilket gör att du kan välja röst och språk för svaret.

Den här artikeln kräver ett [Azure Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med en Speech service-resurs. Om du inte har ett konto kan du använda den [kostnadsfria utvärderingsversionen](get-started.md) för att hämta en prenumerationsnyckel.

## <a name="prerequisites"></a>Krav

* Python 2.7.x eller 3.x
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual Studio <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>, <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio Code <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>eller din favorit text redigerare
* En Azure-prenumerations nyckel för tal tjänsten

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

Dessa moduler används för att skriva tal svaret till en fil med en tidsstämpel, konstruera HTTP-begäran och anropa text-till-tal-API: et.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Ange prenumerations nyckeln och skapa en prompt för TTS

I de följande avsnitten skapar du metoder för att hantera auktorisering, anropa text till tal-API: et och verifiera svaret. Nu ska vi börja med att lägga till kod som ser till att det här exemplet fungerar med python 2.7. x och 3. x.

```python
try:
    input = raw_input
except NameError:
    pass
```

Nu ska vi skapa en klass. Här ska vi lägga våra metoder för token Exchange och anropa text till tal-API: et.

```python
class TextToSpeech(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.tts = input("What would you like to convert to speech: ")
        self.timestr = time.strftime("%Y%m%d-%H%M")
        self.access_token = None
```

`subscription_key` Är din unika nyckel från Azure Portal. `tts`användaren uppmanas att ange text som ska konverteras till tal. Den här indatamängden är en tecken sträng, så tecknen behöver inte vara undantagna. Slutligen `timestr` hämtar den aktuella tiden som vi ska använda för att namnge din fil.

## <a name="get-an-access-token"></a>Hämta en åtkomsttoken

Text till tal-REST API kräver en åtkomsttoken för autentisering. För att få en åtkomsttoken krävs ett utbyte. Det här exemplet utbyter din röst tjänst prenumerations nyckel för en åtkomsttoken `issueToken` med hjälp av slut punkten.

Det här exemplet förutsätter att din röst tjänst prenumeration är i regionen USA, västra. Uppdatera värdet för `fetch_token_url`om du använder en annan region. En fullständig lista finns i [regioner](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Kopiera den här koden till `TextToSpeech` -klassen:

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
> Mer information om autentisering finns i [autentisera med en åtkomsttoken](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="make-a-request-and-save-the-response"></a>Gör en begäran och spara svaret

Här skapar du begäran och sparar tal svaret. Först måste du ange `base_url` och. `path` I det här exemplet förutsätter vi att du använder slut punkten västra USA. Om din resurs är registrerad i en annan region, se till att du uppdaterar `base_url`. Mer information finns i avsnittet om [tal service områden](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Därefter måste du lägga till nödvändiga rubriker för begäran. Se till att du uppdaterar `User-Agent` med namnet på din resurs (finns i Azure Portal) och Ställ in `X-Microsoft-OutputFormat` på önskad ljud uppspelning. En fullständig lista över utdataformat finns i [ljud utmatningar](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Konstruera sedan begär ande texten med hjälp av tal syntes Markup Language (SSML). Det här exemplet definierar strukturen och använder `tts` indatamängden som du skapade tidigare.

>[!NOTE]
> I `Guy24kRUS` det här exemplet används röst teckensnittet. En fullständig lista över de röster/språk som tillhandahålls av Microsoft finns i [språk stöd](language-support.md).
> Om du är intresse rad av att skapa en unik, identifierbar röst för ditt varumärke, se [skapa anpassade röst teckensnitt](how-to-customize-voice-font.md).

Slutligen ska du skapa en begäran till tjänsten. Om begäran lyckas och en status kod för 200 returneras, skrivs tal svaret till en tidsstämplad fil.

Kopiera den här koden till `TextToSpeech` -klassen:

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

Nästan klart. Det sista steget är att skapa en instans av klassen och anropa dina funktioner.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = TextToSpeech(subscription_key)
    app.get_token()
    app.save_audio()
```

## <a name="run-the-sample-app"></a>Kör exempelappen

Då är det dags att köra din text-till-tal-exempel App. Från kommandoraden (eller en terminalsession) går du till projektkatalogen och kör:

```console
python tts.py
```

När du uppmanas till det skriver du det du vill konvertera från text till tal. Om det lyckas finns tal filen i projektmappen. Spela upp den med din favorit medie spelare.

## <a name="clean-up-resources"></a>Rensa resurser

Se till att ta bort all konfidentiell information från exempelappens källkod, till exempel prenumerationsnycklar.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska Python-exempel på GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Se även

* [Referens för text till tal-API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Använda python-och tal-SDK för att konvertera text till tal](quickstarts/speech-to-text-from-microphone.md)
* [Skapa anpassade röst teckensnitt](how-to-customize-voice-font.md)
* [Spela in röst exempel för att skapa en anpassad röst](record-custom-voice-samples.md)
