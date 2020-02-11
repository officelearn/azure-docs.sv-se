---
title: Omvandla text till tal-, python-tal-tjänst
titleSuffix: Azure Cognitive Services
description: I den här artikeln får du lära dig hur du konverterar text till tal med python och text till tal-REST API. Exempeltext som ingår i den här guiden är strukturerad som tal syntes Markup Language (SSML). På så sätt kan du välja rösten och språket för tal-svaret.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: how-to
ms.date: 02/10/2020
ms.author: dapine
ms.openlocfilehash: 63e6a2a47265eae08a653f3eadaf6bad86dd0635
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119723"
---
# <a name="convert-text-to-speech-using-python"></a>Konvertera text till tal med python

I den här artikeln får du lära dig hur du konverterar text till tal med python och text till tal-REST API. Begär ande texten i den här guiden är strukturerad som [SSML (Speech syntes Markup Language)](speech-synthesis-markup.md), vilket gör att du kan välja röst och språk för svaret.

Den här artikeln kräver ett [Azure Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med en Speech service-resurs. Om du inte har ett konto kan du använda den [kostnadsfria utvärderingsversionen](get-started.md) för att hämta en prenumerationsnyckel.

## <a name="prerequisites"></a>Förutsättningar

* Python 2.7.x eller 3.x
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) eller valfritt redigeringsprogram
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

Dessa moduler används för att skriva tal svaret på en fil med en tidsstämpel, skapa HTTP-begäran och anropa text till tal-API.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Ange prenumerationsnyckeln och skapa en uppmaning för text till tal

I nästa avsnitt ska du skapa metoder för att hantera auktorisering, anropa text till tal-API och verifiera svaret. Låt oss börja med att lägga till kod som gör att det här exemplet fungerar med Python 2.7.x och 3.x.

```python
try:
    input = raw_input
except NameError:
    pass
```

Nu ska vi skapa en klass. Det här är där vi lägger in vår metoder för tokenutbytet och anropa text till tal-API.

```python
class TextToSpeech(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.tts = input("What would you like to convert to speech: ")
        self.timestr = time.strftime("%Y%m%d-%H%M")
        self.access_token = None
```

`subscription_key` är din unika nyckel från Azure Portal. `tts` uppmanas användaren att ange text som ska konverteras till tal. Den här indata är en teckensträng så tecken inte behöver undantas. Slutligen `timestr` hämtar den aktuella tiden som vi ska använda för att namnge din fil.

## <a name="get-an-access-token"></a>Hämta en åtkomsttoken

Text till tal REST-API kräver en åtkomsttoken för autentisering. Om du vill få en åtkomsttoken, krävs ett utbyte. Det här exemplet utbyter din prenumerations nyckel för röst tjänsten för en åtkomsttoken med hjälp av `issueToken` slut punkten.

Det här exemplet förutsätter att din röst tjänst prenumeration är i regionen USA, västra. Om du använder en annan region uppdaterar du värdet för `fetch_token_url`. En fullständig lista finns i [regioner](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Kopiera den här koden till `TextToSpeech`-klassen:

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

Här ska du skapa begäran och spara svaret tal. Först måste du ange `base_url` och `path`. Det här exemplet förutsätter att du använder slutpunkten som USA, västra. Om din resurs är registrerad i en annan region, se till att du uppdaterar `base_url`. Mer information finns i avsnittet om [tal service områden](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Du måste sedan lägga till nödvändiga sidhuvuden för begäran. Se till att du uppdaterar `User-Agent` med namnet på din resurs (finns i Azure Portal) och ange `X-Microsoft-OutputFormat` till önskad ljud uppspelning. En fullständig lista över utdataformat finns i [ljud utmatningar](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Skapa sedan begärandetexten med tal syntes Markup Language (SSML). Det här exemplet definierar strukturen och använder `tts` inmatare som du skapade tidigare.

>[!NOTE]
> I det här exemplet används `Guy24KRUS` röst teckensnitt. En fullständig lista över de röster/språk som tillhandahålls av Microsoft finns i [språk stöd](language-support.md).
> Om du är intresse rad av att skapa en unik, identifierbar röst för ditt varumärke, se [skapa anpassade röst teckensnitt](how-to-customize-voice-font.md).

Slutligen ska du göra en begäran till tjänsten. Om begäran lyckas, och statuskoden 200 returneras svaret tal skrivs till en tidsstämplad-fil.

Kopiera den här koden till `TextToSpeech`-klassen:

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
        'name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
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

Det var allt, är du redo att köra din text till tal-exempelapp. Från kommandoraden (eller en terminalsession) går du till projektkatalogen och kör:

```console
python tts.py
```

När du uppmanas, anger du i vad du vill konvertera från text till tal. Om detta lyckas, finns tal-filen i projektmappen. Spela upp den med din favorit media player.

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
