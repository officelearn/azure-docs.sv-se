---
title: Hur du med chunked-ljud Stream | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Hur du använder segmenterat trasfer för att skicka ljudström till tjänsten för Bing-tal
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d9796cf60e2695c21d781131c935d24891401efa
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2019
ms.locfileid: "56671905"
---
# <a name="chunked-transfer-encoding"></a>Chunked-kodning

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

För att transkribera tal till text, kan Microsoft API för taligenkänning du skicka ljudet som en hela segment eller beskära ljudet i små segment. För att effektivt strömma ljud och avskrift svarstiden, vi rekommenderar att du använder [Chunked-kodning](https://en.wikipedia.org/wiki/Chunked_transfer_encoding) att strömma ljud till tjänsten. Andra-implementeringar kan leda till användaren som uppfattade svarstider. Mer information finns i den [ljud strömmar](../concepts.md#audio-streams) sidan.

> [!NOTE]
> Du kan inte överföra mer än 10 sekunder ljud varje en begäran och varaktigheten för totalt antal begäran får inte överskrida 14 sekunder.
> [!NOTE]
> Du måste ange den Chunked-kodning bara om du använder den [REST API: er](../GetStarted/GetStartedREST.md) att anropa speech-tjänsten. Program som använder [klientbibliotek](../GetStarted/GetStartedClientLibraries.md) behöver inte konfigurera den Chunked-kodning.

Följande kod visar hur du anger den Chunked-kodning och skicka en ljudfil som chunked i 1024 byte-segment.

```cs

HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = @"speech.platform.bing.com";
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{

    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```
