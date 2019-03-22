---
title: Kom igång med Bing-Taligenkänning för taligenkänning med hjälp av REST | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: 'Använd REST för att komma åt den tal-API: T i Microsoft Cognitive Services för att omvandla talat ljud till text.'
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ead4026ecec4878c69bc21a9ebc989eaf3d69a13
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259949"
---
# <a name="quickstart-use-the-bing-speech-recognition-rest-api"></a>Snabbstart: Använd Bing-taligenkänning REST API

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Med molnbaserad Bing Speech-tjänsten, kan du utveckla program med hjälp av REST-API för att omvandla talat ljud till text.

## <a name="prerequisites"></a>Förutsättningar

### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Prenumerera på API för taligenkänning och få en kostnadsfri utvärderingsprenumeration-nyckel

API för taligenkänning är en del av Cognitive Services (tidigare projekt Oxford). Du kan få kostnadsfri utvärderingsprenumeration nycklar från den [Cognitive Services-prenumeration](https://azure.microsoft.com/try/cognitive-services/) sidan. När du har valt API för taligenkänning, Välj **hämta API-nyckel** att hämta nyckeln. Returnerar den primära och sekundära nyckeln. Båda nycklarna är knutna till samma kvot, så du kan använda någon av nycklarna.

> [!IMPORTANT]
>* Få en prenumerationsnyckel. Innan du kan komma åt REST API, måste du ha en [prenumerationsnyckel](https://azure.microsoft.com/try/cognitive-services/).
>
>* Använd din prenumerationsnyckel. Ersätt YOUR_SUBSCRIPTION_KEY med din egen prenumerationsnyckel i följande REST-exempel.
>
>* Referera till den [autentisering](../how-to/how-to-authentication.md) för hur du hämtar en prenumerationsnyckel.

### <a name="prerecorded-audio-file"></a>Inspelade ljudfil

I det här exemplet använder vi en ljudfil för att visa hur du använder REST-API. Registrera en ljudfil för dig själv om en kort mening. Anta till exempel ”vad är vädret som idag”? eller ”hitta roliga filmer kan du titta på”. API för taligenkänning har även stöd för extern mikrofon indata.

> [!NOTE]
> I exemplet krävs att ljud registreras som en WAV-filer med **PCM-kanal (mono), 16 KHz**.

## <a name="build-a-recognition-request-and-send-it-to-the-speech-recognition-service"></a>Skapa en för begäran och skicka den till tjänsten för taligenkänning

Nästa steg för taligenkänning är att skicka en POST-begäran till tal HTTP-slutpunkter med rätt begärande sidhuvud och brödtext.

### <a name="service-uri"></a>Tjänstens URI

Tjänsten för taligenkänning URI definieras baserat på [erkännande lägen](../concepts.md#recognition-modes) och [språk](../concepts.md#recognition-languages):

```HTTP
https://speech.platform.bing.com/speech/recognition/<RECOGNITION_MODE>/cognitiveservices/v1?language=<LANGUAGE_TAG>&format=<OUTPUT_FORMAT>
```

`<RECOGNITION_MODE>` Anger läge för taligenkänning och måste vara något av följande värden: `interactive`, `conversation`, eller `dictation`. Det är en nödvändig resurssökväg i URI: N. Mer information finns i [erkännande lägen](../concepts.md#recognition-modes).

`<LANGUAGE_TAG>` är en obligatorisk parameter i frågesträngen. Den definierar målspråk för ljud konvertering: till exempel `en-US` för engelska (USA). Mer information finns i [språk](../concepts.md#recognition-languages).

`<OUTPUT_FORMAT>` är en valfri parameter i frågesträngen. De tillåtna värdena är `simple` och `detailed`. Som standard tjänsten returnerar resultat i `simple` format. Mer information finns i [utdataformat](../concepts.md#output-format).

I följande tabell visas några exempel på service URI: er.

| Igenkänningsläge  | Språk | Utdataformat | Tjänstens URI |
|---|---|---|---|
| `interactive` | pt-BR | Standard | https:\//speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| `conversation` | en-US | Detaljerad | https:\//speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed |
| `dictation` | fr-FR | Enkel | https:\//speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR&format=simple |

> [!NOTE]
> Tjänstens URI krävs endast när ditt program använder REST API: er för att anropa tjänsten för taligenkänning. Om du använder en av de [klientbibliotek](GetStartedClientLibraries.md), du vanligtvis inte behöver veta vilka URI: N används. Klientbiblioteken kan använda olika service URI: er, som gäller bara för en viss klient-biblioteket. Mer information finns i klientbiblioteket för ditt val.

### <a name="request-headers"></a>Begärandehuvud

Följande fält måste anges i huvudet för begäran:

- `Ocp-Apim-Subscription-Key`: Varje gång som du anropa tjänsten, måste du ange din prenumerationsnyckel i den `Ocp-Apim-Subscription-Key` rubrik. Taltjänsten stöder också skicka auktorisering token i stället för prenumerationsnycklar. Mer information finns i [Autentisering](../How-to/how-to-authentication.md).
- `Content-type`: Den `Content-type` fältet beskriver format och codec för ljud dataström. För närvarande endast WAV-fil och PCM Mono 16000 kodning stöds. Content-type-värdet för det här formatet är `audio/wav; codec=audio/pcm; samplerate=16000`.

Fältet `Transfer-Encoding` är valfritt. Om du anger det här fältet till `chunked`, kan du beskära ljudet i små segment. Mer information finns i [Segmentvis överföring](../How-to/how-to-chunked-transfer.md).

Följande är ett exempel förfrågnings-huvud:

```HTTP
POST https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: speech.platform.bing.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="send-a-request-to-the-service"></a>Skicka en begäran till tjänsten

I följande exempel visas hur du skickar en begäran för igenkänning av tal till tal REST-slutpunkter. Den använder den `interactive` erkännande läge.

> [!NOTE]
> Ersätt `YOUR_AUDIO_FILE` med sökvägen till din inspelade ljudfil. Ersätt `YOUR_SUBSCRIPTION_KEY` med din egen prenumerationsnyckel.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authorization token returned by the token service.
$RecoRequestHeader = @{
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY';
  'Transfer-Encoding' = 'chunked';
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

Exemplet använder curl på Linux med bash. Om det inte är tillgänglig på din plattform, kan du behöva installera curl. Exemplet fungerar även på Cygwin på Windows, Git Bash, zsh och andra gränssnitt.

> [!NOTE]
> Behåll den `@` före namnet på ljudfil när `YOUR_AUDIO_FILE` med sökvägen till din inspelade ljudfil, eftersom det anger att värdet för `--data-binary` är ett filnamn i stället för data.

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

// Send an audio file by 1024 byte chunks
using (FileStream fs = new FileStream(YOUR_AUDIO_FILE, FileMode.Open, FileAccess.Read))
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

---

## <a name="process-the-speech-recognition-response"></a>Bearbeta tal igenkänning av svaret

När begäran bearbetades, returnerar Speech Service resultaten i ett svar som JSON-format.

> [!NOTE]
> Om den tidigare koden returnerar ett fel, se [felsökning](../troubleshooting.md) att hitta orsaken.

Följande kodavsnitt visar ett exempel på hur du kan läsa svaret från strömmen.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell
# show the response in JSON format
ConvertTo-Json $RecoResponse
```

# <a name="curltabcurl"></a>[curl](#tab/curl)

I det här exemplet returnerar curl direkt svarsmeddelandet i en sträng. Om du vill visa den i JSON-format kan du använda fler verktyg, till exempel jq.

```
curl -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE | jq
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
/*
* Get the response from the service.
*/
Console.WriteLine("Response:");
using (WebResponse response = request.GetResponse())
{
    Console.WriteLine(((HttpWebResponse)response).StatusCode);

    using (StreamReader sr = new StreamReader(response.GetResponseStream()))
    {
        responseString = sr.ReadToEnd();
    }

    Console.WriteLine(responseString);
    Console.ReadLine();
}
```

---

I följande exempel är ett JSON-svar:

```json
OK
{
  "RecognitionStatus": "Success",
  "Offset": 22500000,
  "Duration": 21000000,
  "NBest": [{
    "Confidence": 0.941552162,
    "Lexical": "find a funny movie to watch",
    "ITN": "find a funny movie to watch",
    "MaskedITN": "find a funny movie to watch",
    "Display": "Find a funny movie to watch."
  }]
}
```

## <a name="limitations"></a>Begränsningar

REST API har vissa begränsningar:

- Det stöder ljudström bara upp till 15 sekunder.
- Det stöder inte mellanresultat under erkännande. Användarna får den slutliga igenkänningsresultatet.

Ta bort dessa begränsningar med tal [klientbibliotek](GetStartedClientLibraries.md). Eller du kan arbeta direkt med den [tal WebSocket-protokoll](../API-Reference-REST/websocketprotocol.md).

## <a name="whats-next"></a>Nästa steg

- Om du vill se hur du använder REST-API i C#, Java, etc. kan se dessa [programexempel](../samples.md).
- För att hitta och åtgärda fel, se [felsökning](../troubleshooting.md).
- Om du vill använda mer avancerade funktioner, se hur du kommer igång med hjälp av tal [klientbibliotek](GetStartedClientLibraries.md).

### <a name="license"></a>Licens

Alla kognitiva tjänster SDK: er och exempel har en licens för MIT-licensen. Mer information finns i [licens](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
