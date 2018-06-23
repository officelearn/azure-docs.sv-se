---
title: C# Snabbstartsguide för Azure kognitiva Services, Microsoft Translator tal API | Microsoft Docs
description: Hämta information och exempel på kod för att snabbt komma igång med Microsoft översättare tal-API i kognitiva Microsoft-tjänster i Azure.
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jaswel
ms.openlocfilehash: 6489a0be72cedffdfa4f7021f889b2d39c1c358f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352197"
---
# <a name="quickstart-for-microsoft-translator-speech-api-with-c"></a>Snabbstart för Microsoft Translator tal API med C# 
<a name="HOLTop"></a>

Den här artikeln visar hur du använder Microsoft översättare tal-API för att översätta ord talas i WAV-filer.

## <a name="prerequisites"></a>Förutsättningar

Du behöver [Visual Studio 2017](https://www.visualstudio.com/downloads/) att köra den här koden i Windows. (Ledigt Community Edition fungerar).

Du behöver en WAV-fil med namnet ”speak.wav” i samma mapp som den körbara filen som du sammanställer från koden nedan. Den här WAV-filen måste vara i standard PCM, 16-bitars, 16kHz monoljud format. Du kan hämta dessa WAV-filer från de [översättare Text tala API](http://docs.microsofttranslator.com/text-translate.html#!/default/get_Speak).

Du måste ha en [kognitiva Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med **Microsoft översättare tal-API**. Du behöver en betald prenumeration nyckeln från din [Azure instrumentpanelen](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Översätta tal

Följande kod översätter tal från ett språk till en annan.

1. Skapa ett nytt C#-projekt i din favorit IDE.
2. Lägg till koden nedan.
3. Ersätt den `key` värde med en giltig snabbtangent för din prenumeration.
4. Kör programmet.

```csharp
using System;
using System.IO;
using System.Net.WebSockets;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

namespace TranslateSpeechQuickStart
{
    class Program
    {
        static string host = "wss://dev.microsofttranslator.com";
        static string path = "/speech/translate";

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "ENTER KEY HERE";

        async static Task Send (ClientWebSocket client, string input_path)
        {
            var audio = File.ReadAllBytes(input_path);
            var audio_out_buffer = new ArraySegment<byte>(audio);
            Console.WriteLine("Sending audio.");
            await client.SendAsync(audio_out_buffer, WebSocketMessageType.Binary, true, CancellationToken.None);

            /* Make sure the audio file is followed by silence.
             * This lets the service know that the audio input is finished. */
            var silence = new byte[3200000];
            var silence_buffer = new ArraySegment<byte>(silence);
            await client.SendAsync(silence_buffer, WebSocketMessageType.Binary, true, CancellationToken.None);

            Console.WriteLine("Done sending.");
            await client.CloseAsync(WebSocketCloseStatus.NormalClosure, "", CancellationToken.None);
        }

        async static Task Receive(ClientWebSocket client, string output_path)
        {
            var inbuf = new byte[102400];
            var segment = new ArraySegment<byte>(inbuf);
            var stream = new FileStream(output_path, FileMode.Create);

            Console.WriteLine("Awaiting response.");
            while (client.State == WebSocketState.Open)
            {
                var result = await client.ReceiveAsync(segment, CancellationToken.None);
                switch (result.MessageType)
                {
                    case WebSocketMessageType.Close:
                        Console.WriteLine("Received close message. Status: " + result.CloseStatus + ". Description: " + result.CloseStatusDescription);
                        await client.CloseAsync(WebSocketCloseStatus.NormalClosure, string.Empty, CancellationToken.None);
                        break;
                    case WebSocketMessageType.Text:
                        Console.WriteLine("Received text.");
                        Console.WriteLine(Encoding.UTF8.GetString(inbuf).TrimEnd('\0'));
                        break;
                    case WebSocketMessageType.Binary:
                        Console.WriteLine("Received binary data: " + result.Count + " bytes.");
                        stream.Write(inbuf, 0, result.Count);
                        break;
                }
            }

            stream.Close();
            stream.Dispose();
        }

        async static void TranslateSpeech()
        {
            var client = new ClientWebSocket();
            client.Options.SetRequestHeader ("Ocp-Apim-Subscription-Key", key);

            string from = "en-US";
            string to = "it-IT";
            string features = "texttospeech";
            string voice = "it-IT-Elsa";
            string api = "1.0";

            string input_path = "speak.wav";
            string output_path = "speak2.wav";

            string uri = host + path +
                "?from=" + from +
                "&to=" + to +
                "&api-version=" + api +
                "&features=" + features +
                "&voice=" + voice;

            Console.WriteLine("uri: " + uri);
            Console.WriteLine("Opening connection.");
            await client.ConnectAsync(new Uri(uri), CancellationToken.None);
            Console.WriteLine("Connection open.");
            Task.WhenAll(Send(client, input_path), Receive(client, output_path)).Wait();
        }

        static void Main()
        {
            TranslateSpeech();
            Console.ReadLine();
        }

    }
}
```

**Översätta tal svar**

En lyckad resultatet är att skapa en fil med namnet ”speak2.wav”. Filen innehåller översättning av orden talas i ”speak.wav”.

[Överst på sidan](#HOLTop)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Översättare tal självstudiekursen](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>Se också 

[Översättare tal, översikt](../overview.md)
[API-referens](http://docs.microsofttranslator.com/speech-translate.html)
