---
title: Hur du Chunked överföring ljudström | Microsoft Docs
description: Hur du använder chunked trasfer för att skicka ljudström till tal-tjänsten
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 7d02340932dfc547893c4c40cbe08978b7b93756
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352170"
---
# <a name="chunked-transfer-encoding"></a>Segmentvis överföringskodning

Om du vill transkribera tal till text, kan Microsoft taligenkänning API du skicka ljuduppspelningen som en hela segmentet eller beskära ljuduppspelningen i små segment. För effektiv direktuppspelning av ljud och minskar latens skrivfel, rekommenderas att du använder [segmentvis överföringskodning](https://en.wikipedia.org/wiki/Chunked_transfer_encoding) för direktuppspelning av ljud till tjänsten. Andra implementeringar kan resultera i högre användaren uppfattas latens. Mer information finns i [ljud dataströmmar](../concepts.md#audio-streams) sidan.

> [!NOTE]
> Du kan inte överföra mer än 10 sekunder ljud varje en begäran och totala begäran kan inte vara längre än 14 sekunder.
> [!NOTE]
> Du måste ange segmentvis överföringskodning endast om du använder den [REST API: er](../GetStarted/GetStartedREST.md) att anropa tjänsten tal. Program som använder [klientbibliotek](../GetStarted/GetStartedClientLibraries.md) inte behöver konfigurera segmentvis överföringskodning.

Följande kod visar hur du anger segmentvis överföringskodning och skicka en ljudfil som chunked i 1024 byte segment.

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
