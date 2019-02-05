---
title: 'Snabbstart: Translator Speech API Python'
titlesuffix: Azure Cognitive Services
description: Hämta information och kodexempel som hjälper dig att snabbt komma igång med Translator Speech API.
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: quickstart
ms.date: 07/17/2018
ms.author: v-jaswel
ms.openlocfilehash: 3cecb09488c40ee23ea34c75e666788a4ec47fdb
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55459289"
---
# <a name="quickstart-translator-speech-api-with-python"></a>Snabbstart: Translator Speech API med Python
<a name="HOLTop"></a>

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Den här artikeln visar hur du använder Translator Speech API för att översätta ord som sägs i en WAV-fil.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver [Python 3.x](https://www.python.org/downloads/) för att köra koden.

Du måste installera [websocket-client-paketet](https://pypi.python.org/pypi/websocket-client) för Python.

Du behöver ha en WAV-fil med namnet ”speak.wav” i samma mapp som den körbara filen som du kompilerar från koden nedan. Den här .wav-filen ska vara i 16-bitars, 16 kHz PCM-standardformat. 

Du behöver ett [Cognitive Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med **Microsoft Translator Speech API**. Du behöver prenumerationsnyckeln för en betalprenumeration från din [Azure-instrumentpanel](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Översätt tal

Följande kod omvandlar tal från ett språk till ett annat.

1. Skapa ett nytt Python-projekt i valfri IDE.
2. Lägg till koden nedan.
3. Ersätt värdet `key` med en giltig åtkomstnyckel för din prenumeration.
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

**Talöversättningssvar**

Om åtgärden lyckas skapas en fil med namnet ”speak2.wav”. Filen innehåller översättningen av orden som sägs i ”speak.wav”.

[Överst på sidan](#HOLTop)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Translator Speech-självstudie](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>Se även 

[Translator Speech-översikt](../overview.md)
[API-referens](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
