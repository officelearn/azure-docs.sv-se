---
title: Python-Snabbstart för Azure Cognitive Services och Microsoft Translator-API för Talöversättning | Microsoft Docs
description: Hämta information och exempel på kod som hjälper dig att snabbt komma igång med Microsoft Translator Speech API i Microsoft Cognitive Services på Azure.
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 07/17/2018
ms.author: v-jaswel
ms.openlocfilehash: 8bf904b2029790d64a806fcf4a7e4860579a5a2f
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39204858"
---
# <a name="quickstart-for-microsoft-translator-speech-api-with-python"></a>Snabbstart för Microsoft Translator Speech API med Python 
<a name="HOLTop"></a>

Den här artikeln visar hur du använder Microsoft Translator Speech API för att översätta ord som sägs i en WAV-fil.

## <a name="prerequisites"></a>Förutsättningar

Du behöver [Python 3.x](https://www.python.org/downloads/) att köra den här koden.

Du måste installera den [websocket-client-paketet](https://pypi.python.org/pypi/websocket-client) för Python.

Du behöver en WAV-fil med namnet ”speak.wav” i samma mapp som den körbara filen som du kompilera från koden nedan. Den här .wav-filen ska vara i standard PCM, 16-bitars, 16kHz mono format. Du kan hämta sådan .wav fil från den [Text till tal-API](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#text-to-speech).

Du måste ha en [Cognitive Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med **Microsoft Translator Speech API**. Du behöver en betald prenumerationsnyckel från din [Azure-instrumentpanelen](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Översätt tal

Följande kod omvandlar tal från ett språk till ett annat.

1. Skapa ett nytt Python-projekt i din favorit-IDE.
2. Lägg till koden nedan.
3. Ersätt den `key` värde med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```python
# -*- coding: utf-8 -*-

# To install this package, run:
#   pip install websocket-client
import websocket

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
key = 'ENTER KEY HERE'

host = 'wss://dev.microsofttranslator.com'
path = '/speech/translate';
params = '?api-version=1.0&from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa'
uri = host + path + params

input_file = 'speak.wav'
output_file = 'speak2.wav'

output = bytearray ()

def on_open (client):
    print ("Connected.")

# r = read. b = binary.
    with open (input_file, mode='rb') as file:
        data = file.read()

    print ("Sending audio.")
    client.send (data, websocket.ABNF.OPCODE_BINARY)
# Make sure the audio file is followed by silence.
# This lets the service know that the audio input is finished.
    print ("Sending silence.")
    client.send (bytearray (32000), websocket.ABNF.OPCODE_BINARY)

def on_data (client, message, message_type, is_last):
    global output
    if (websocket.ABNF.OPCODE_TEXT == message_type):
        print ("Received text data.")
        print (message)
# For some reason, we receive the data as type websocket.ABNF.OPCODE_CONT.
    elif (websocket.ABNF.OPCODE_BINARY == message_type or websocket.ABNF.OPCODE_CONT == message_type):
        print ("Received binary data.")
        print ("Is last? " + str(is_last))
        output = output + message
        if (True == is_last):
# w = write. b = binary.
            with open (output_file, mode='wb') as file:
                file.write (output)
                print ("Wrote data to output file.")
            client.close ()
    else:
        print ("Received data of type: " + str (message_type))

def on_error (client, error):
    print ("Connection error: " + str (error))

def on_close (client):
    print ("Connection closed.")

client = websocket.WebSocketApp(
    uri,
    header=[
        'Ocp-Apim-Subscription-Key: ' + key
    ],
    on_open=on_open,
    on_data=on_data,
    on_error=on_error,
    on_close=on_close
)

print ("Connecting...")
client.run_forever()
```

**Omvandla tal svar**

En lyckad resultatet är att skapa en fil med namnet ”speak2.wav”. Filen innehåller översättningen av ord som sägs i ”speak.wav”.

[Överst på sidan](#HOLTop)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Translator Speech självstudien](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>Se också 

[Översikt över Translator Speech](../overview.md)
[API-referens](http://docs.microsofttranslator.com/speech-translate.html)
