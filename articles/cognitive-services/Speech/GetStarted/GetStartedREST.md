---
title: Kom igång med Taligenkänning i Bing igenkännings-API med hjälp av REST | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: 'Använd REST för att komma åt API: t för tal igenkänning i Microsoft Cognitive Services att konvertera talade ljud till text.'
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e962a12c6c27737f95e78e80036e51bac41147d5
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965771"
---
# <a name="quickstart-use-the-bing-speech-recognition-rest-api"></a>Snabbstart: Använd Taligenkänning i Bing igenkännings REST API

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Med den molnbaserade Taligenkänning i Bings tjänsten kan du utveckla program med hjälp av REST API för att konvertera talade ljud till text.

## <a name="prerequisites"></a>Förutsättningar

### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Prenumerera på Speech API och få en prenumerations nyckel för kostnads fri utvärdering

Speech API är en del av Cognitive Services (tidigare Project-Oxford). Du kan få kostnads fria utvärderings prenumerations nycklar från sidan [Cognitive Services prenumeration](https://azure.microsoft.com/try/cognitive-services/) . När du har valt Speech API väljer du **Hämta API-nyckel** för att hämta nyckeln. Den returnerar en primär och sekundär nyckel. Båda nycklarna är knutna till samma kvot, så du kan använda båda nycklarna.

> [!IMPORTANT]
>* Hämta en prenumerations nyckel. Innan du kan komma åt REST API måste du ha en [prenumerations nyckel](https://azure.microsoft.com/try/cognitive-services/).
>
>* Använd din prenumerations nyckel. I följande REST-exempel ersätter du YOUR_SUBSCRIPTION_KEY med din egen prenumerations nyckel.
>
>* På sidan [autentisering](../how-to/how-to-authentication.md) hittar du information om hur du hämtar en prenumerations nyckel.

### <a name="prerecorded-audio-file"></a>Förregistrerad ljudfil

I det här exemplet använder vi en inspelad ljud fil för att illustrera hur du använder REST API. Spela in en ljudfil på egen hand som säger en kort fras. Säg t. ex. säga "Vad är den väder som idag?" eller "hitta roliga filmer att titta på". Tal igenkännings-API: t stöder även extern mikrofon-inläsning.

> [!NOTE]
> Exemplet kräver att ljud spelas in som en WAV-fil med en **enkel kanal (mono) med PCM, 16 kHz**.

## <a name="build-a-recognition-request-and-send-it-to-the-speech-recognition-service"></a>Bygg en begäran om godkännande och skicka den till tjänsten för tal igenkänning

Nästa steg för tal igenkänning är att skicka en POST-begäran till tal-HTTP-slutpunkter med rätt rubrik och brödtext för begäran.

### <a name="service-uri"></a>Tjänstens URI

Tjänst-URI: n för tal igenkänning definieras baserat på [tolknings lägen](../concepts.md#recognition-modes) och [tolknings språk](../concepts.md#recognition-languages):

```HTTP
https://speech.platform.bing.com/speech/recognition/<RECOGNITION_MODE>/cognitiveservices/v1?language=<LANGUAGE_TAG>&format=<OUTPUT_FORMAT>
```

`<RECOGNITION_MODE>`anger tolknings läget och måste vara något av följande värden: `interactive`, `conversation`, eller `dictation`. Det är en obligatorisk resurs Sök väg i URI: n. Mer information finns i [igenkännings lägen](../concepts.md#recognition-modes).

`<LANGUAGE_TAG>`är en obligatorisk parameter i frågesträngen. Den definierar mål språket för ljud konvertering: till exempel `en-US` för engelska (USA). Mer information finns i [tolknings språk](../concepts.md#recognition-languages).

`<OUTPUT_FORMAT>`är en valfri parameter i frågesträngen. De tillåtna värdena är `simple` och `detailed`. Som standard returnerar tjänsten resultatet i `simple` formatet. Mer information finns i [utdataformat](../concepts.md#output-format).

Några exempel på tjänst-URI: er visas i följande tabell.

| Igenkänningsläge  | Språk | Utdataformat | Tjänstens URI |
|---|---|---|---|
| `interactive` | pt-BR | Standard | https:\//speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| `conversation` | en-US | Detaljerad | https:\//speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed |
| `dictation` | fr-FR | Enkel | https:\//speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR&format=simple |

> [!NOTE]
> Tjänste-URI behövs bara när programmet använder REST API: er för att anropa tjänsten för tal igenkänning. Om du använder ett av [klient biblioteken](GetStartedClientLibraries.md)behöver du vanligt vis inte veta vilken URI som används. Klient biblioteken kan använda olika tjänst-URI: er som endast gäller för ett särskilt klient bibliotek. Mer information finns i det klient bibliotek som du väljer.

### <a name="request-headers"></a>Begärandehuvud

Följande fält måste anges i begär ande huvudet:

- `Ocp-Apim-Subscription-Key`: Varje gång du anropar tjänsten måste du skicka prenumerations nyckeln i `Ocp-Apim-Subscription-Key` rubriken. Speech service stöder också att du skickar autentiseringstoken i stället för prenumerations nycklar. Mer information finns i [Autentisering](../How-to/how-to-authentication.md).
- `Content-type`: I `Content-type` fältet beskrivs formatet och codecen för ljud strömmen. För närvarande stöds endast WAV-filer och PCM mono 16000-kodning. Content-Type-värdet för det här formatet `audio/wav; codec=audio/pcm; samplerate=16000`är.

Fältet `Transfer-Encoding` är valfritt. Om du anger det här fältet `chunked`till kan du Chop ljudet i små segment. Mer information finns i [segment överföring](../How-to/how-to-chunked-transfer.md).

Följande är ett exempel på en begäran-rubrik:

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

I följande exempel visas hur du skickar en begäran om tal igenkänning till tal REST-slutpunkter. Den använder `interactive` igenkännings läget.

> [!NOTE]
> Ersätt `YOUR_AUDIO_FILE` med sökvägen till din inspelade ljudfil. Ersätt `YOUR_SUBSCRIPTION_KEY` med din egen prenumerations nyckel.

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

# <a name="curltabcurl"></a>[klammerparentes](#tab/curl)

Exemplet använder sväng i Linux med bash. Om den inte är tillgänglig på din plattform kan du behöva installera en sväng. Exemplet fungerar även på Cygwin i Windows, git bash, zsh och andra gränssnitt.

> [!NOTE]
> Behåll före namnet på ljud filen när du ersätter `YOUR_AUDIO_FILE` med sökvägen till den förregistrerade ljud filen, eftersom det indikerar att värdet för `--data-binary` är ett fil namn i stället för data. `@`

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

## <a name="process-the-speech-recognition-response"></a>Behandla tal igenkännings svaret

När begäran har bearbetats returnerar tal tjänsten resultatet i ett svar som JSON-format.

> [!NOTE]
> Om föregående kod returnerar ett fel, se [fel sökning](../troubleshooting.md) för att hitta den möjliga orsaken.

Följande kodfragment visar ett exempel på hur du kan läsa svaret från data strömmen.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell
# show the response in JSON format
ConvertTo-Json $RecoResponse
```

# <a name="curltabcurl"></a>[klammerparentes](#tab/curl)

I det här exemplet returnerar spiralen direkt svarsmeddelandet i en sträng. Om du vill visa det i JSON-format kan du använda ytterligare verktyg, till exempel JQ.

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

Följande exempel är ett JSON-svar:

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

- Den stöder bara ljud strömmen upp till 15 sekunder.
- Den har inte stöd för mellanliggande resultat under igenkänning. Användare får bara det slutliga igenkännings resultatet.

Använd tal [klient bibliotek](GetStartedClientLibraries.md)för att ta bort dessa begränsningar. Eller så kan du arbeta direkt med [tal-WebSocket-protokollet](../API-Reference-REST/websocketprotocol.md).

## <a name="whats-next"></a>Nästa steg

- Om du vill se hur du använder REST API C#i, Java osv., se dessa [exempel program](../samples.md).
- Information om hur du hittar och korrigerar fel finns i [fel sökning](../troubleshooting.md).
- Om du vill använda mer avancerade funktioner går du till hur du kommer igång med att använda tal [klient bibliotek](GetStartedClientLibraries.md).

### <a name="license"></a>Licens

Alla Cognitive Services SDK: er och exempel licensieras med MIT-licensen. Mer information finns i [License](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
