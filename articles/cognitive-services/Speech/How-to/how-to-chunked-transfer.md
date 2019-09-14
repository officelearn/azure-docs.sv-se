---
title: Så här segmenterar du överföring av ljud strömmen | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Använda segmenterade Trasfer för att skicka ljud strömmar till den Taligenkänning i Bing tjänsten
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 60428a7a5e28b7c9d6cd76f31374f24df48423a6
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966494"
---
# <a name="chunked-transfer-encoding"></a>Kodning av segmenterad överföring

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

För att kunna skriva tal till text kan du använda API: et för tal igenkänning i Microsoft för att skicka ljudet som ett helt segment eller för att Chop ljudet i små segment. För effektiv ljud strömning och minskning av avskrifts svars tiden rekommenderar vi att du använder [Chunked Transfer Encoding](https://en.wikipedia.org/wiki/Chunked_transfer_encoding) för att strömma ljudet till tjänsten. Andra implementeringar kan resultera i högre svars tid för användaren. Mer information finns på sidan [ljud strömmar](../concepts.md#audio-streams) .

> [!NOTE]
> Du får inte överföra mer än 10 sekunders ljud i en begäran och den totala varaktigheten för begäran får inte överstiga 14 sekunder.
> [!NOTE]
> Du behöver bara ange den segmenterade överförings kodningen om du använder [REST-API: er](../GetStarted/GetStartedREST.md) för att anropa tal tjänsten. Program som använder [klient bibliotek](../GetStarted/GetStartedClientLibraries.md) behöver inte konfigurera den segmenterade överförings kodningen.

Följande kod visar hur du ställer in den segmenterade överförings kodningen och skickar en ljudfil som delas upp i 1024 byte-segment.

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
