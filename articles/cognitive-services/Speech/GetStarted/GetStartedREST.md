---
title: Kom igång med Microsoft Speech Recognition API med hjälp av REST | Microsoft Docs
description: Använda REST för att komma åt Speech Recognition API i Microsoft kognitiva Services för att konvertera tal till text.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 53785cdfd75c23910802f2be20e6305817b3b097
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352269"
---
# <a name="get-started-with-speech-recognition-by-using-the-rest-api"></a>Komma igång med taligenkänning med hjälp av REST-API

Du kan utveckla program med hjälp av REST API konvertera tal till text med molnbaserade tal-tjänsten.

## <a name="prerequisites"></a>Förutsättningar

### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Prenumerera på tal-API och få en kostnadsfri utvärderingsprenumeration nyckel

Dikterings-API: N är en del av kognitiva tjänster (tidigare projekt Oxford). Du kan hämta en kostnadsfri utvärderingsprenumeration nycklar från den [kognitiva abonnemang](https://azure.microsoft.com/try/cognitive-services/) sidan. När du har valt tal-API, Välj **hämta API-nyckel** att hämta nyckeln. Returnerar en primär och en sekundär nyckel. Båda nycklarna är knutna till samma kvoten, så du kan använda antingen nyckel.

> [!IMPORTANT]
>* Hämta en nyckel för prenumerationen. Innan du kan komma åt REST-API, måste du ha en [prenumeration nyckeln](https://azure.microsoft.com/try/cognitive-services/).
>
>* Använd din prenumeration nyckel. Ersätt YOUR_SUBSCRIPTION_KEY med din egen prenumeration nyckel i följande exempel i REST. 
>
>* Referera till den [autentisering](../how-to/how-to-authentication.md) sidan att få en nyckel för prenumerationen.

### <a name="prerecorded-audio-file"></a>Inspelade ljudfil

I det här exemplet använder vi en ljudfil som illustrerar hur du använder REST API. Registrera en ljudfil på dig om ett kort fras. Anta till exempel ”vad är väder som idag”? eller ”hitta roliga filmer kan du titta på”. Taligenkänning API har också stöd för extern mikrofon indata.

> [!NOTE]
> I exemplet krävs att ljud registreras som en WAV-filer med **PCM kanal (mono), 16 KHz**.

## <a name="build-a-recognition-request-and-send-it-to-the-speech-recognition-service"></a>Skapa en recognition begäran och skickar den till tjänsten speech recognition

Nästa steg för taligenkänning är att skicka en POST-begäran till tal HTTP-slutpunkter med rätt begärandehuvudet och brödtext.

### <a name="service-uri"></a>Tjänstens URI

Tjänsten speech recognition URI definieras baserat på [recognition lägen](../concepts.md#recognition-modes) och [språk](../concepts.md#recognition-languages):

```HTTP
https://speech.platform.bing.com/speech/recognition/<RECOGNITION_MODE>/cognitiveservices/v1?language=<LANGUAGE_TAG>&format=<OUTPUT_FORMAT>
```

`<RECOGNITION_MODE>` Anger läget för igenkänning och måste vara något av följande värden: `interactive`, `conversation`, eller `dictation`. Det är en nödvändig resurs sökväg i URI: N. Mer information finns i [Recognition lägen](../concepts.md#recognition-modes).

`<LANGUAGE_TAG>` är en obligatorisk parameter i frågesträngen. Den definierar målspråk för ljud konvertering: till exempel `en-US` för engelska (USA). Mer information finns i [språk](../concepts.md#recognition-languages).

`<OUTPUT_FORMAT>` är en valfri parameter i frågesträngen. De tillåtna värdena är `simple` och `detailed`. Som standard tjänsten returnerar resultat i `simple` format. Mer information finns i [utdataformat](../concepts.md#output-format).

I följande tabell visas några exempel på tjänsten URI: er.

| Recognition läge  | Språk | Utdataformat | Tjänstens URI |
|---|---|---|---|
| `interactive` | pt-BR | Standard | https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| `conversation` | sv-SE | Detaljerad |https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed |
| `dictation` | fr-FR | Enkel | https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR&format=simple |

> [!NOTE]
> Tjänsten URI krävs bara när programmet använder REST API: er för att anropa tjänsten tal igenkänning. Om du använder en av de [klientbiblioteken](GetStartedClientLibraries.md), vanligtvis behöver du inte vet vilka URI används. Klientbiblioteken kan använda olika service URI: er, som gäller bara för en viss klient-biblioteket. Mer information finns i klientbiblioteket önskat.

### <a name="request-headers"></a>Begärandehuvud

Följande fält måste anges i dess huvud:

- `Ocp-Apim-Subscription-Key`: Varje gång du anropar tjänsten, måste du skicka din prenumeration nyckel i den `Ocp-Apim-Subscription-Key` rubrik. Tal tjänsten stöder också klarat behörighetskontrollen token i stället för prenumerationen nycklar. Mer information finns i [autentisering](../How-to/how-to-authentication.md).
- `Content-type`: `Content-type` Beskriver format och codec strömmens ljud. För närvarande endast WAV-filer och PCM Mono 16000 kodning stöds. Content-type-värdet för det här formatet är `audio/wav; codec=audio/pcm; samplerate=16000`.

Den `Transfer-Encoding` fältet är valfritt. Om du anger det här fältet till `chunked`, kan du beskära ljuduppspelningen i små segment. Mer information finns i [Chunked överföring](../How-to/how-to-chunked-transfer.md).

Följande är ett exempel huvud:

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

I följande exempel visas hur du skickar en begäran för igenkänning av tal till tal REST-slutpunkter. Den använder den `interactive` recognition läge.

> [!NOTE]
> Ersätt `YOUR_AUDIO_FILE` med sökvägen till filen inspelade ljud. Ersätt `YOUR_SUBSCRIPTION_KEY` med din egen prenumeration nyckel.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/Powershell)

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

# <a name="curltabcurl"></a>[cURL](#tab/curl)

I exemplet används curl på Linux med bash. Om den inte är tillgänglig på din plattform, kan du behöva installera curl. Exemplet fungerar även på Cygwin på Windows, Git Bash, zsh och andra gränssnitt.

> [!NOTE]
> Behåll den `@` före ljud filnamnet när du ersätter `YOUR_AUDIO_FILE` med sökvägen till filen inspelade ljud, som anger att värdet för `--data-binary` är ett filnamn i stället för data.

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

# <a name="ctabcsharp"></a>[C#](#tab/CSharp)

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

## <a name="process-the-speech-recognition-response"></a>Bearbeta speech recognition svar

När begäran bearbetades, returnerar tal Service resultaten i ett svar som JSON-format.

> [!NOTE]
> Om föregående kod returnerar ett fel, se [felsökning](../troubleshooting.md) att hitta orsaken.

Följande kodavsnitt visar ett exempel på hur du kan läsa svaret från dataströmmen.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/Powershell)

```Powershell
# show the response in JSON format
ConvertTo-Json $RecoResponse
```

# <a name="curltabcurl"></a>[cURL](#tab/curl)

I det här exemplet returnerar curl direkt svarsmeddelandet i en sträng. Du kan använda fler verktyg, till exempel jq om du vill visa i JSON-format.

```
curl -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE | jq
```

# <a name="ctabcsharp"></a>[C#](#tab/CSharp)

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

I följande exempel är en JSON-svar:

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

REST-API har vissa begränsningar:

- Ljudström stöder bara upp till 15 sekunder.
- Det stöder inte mellanresultat under igenkänning. Användarna får endast slutliga recognition resultatet.

Ta bort dessa begränsningar med tal [klientbiblioteken](GetStartedClientLibraries.md). Eller du kan arbeta direkt med den [tal WebSocket-protokollet](../API-Reference-REST/websocketprotocol.md).

## <a name="whats-next"></a>Nästa steg

- Hur du använder REST-API i C#, Java, etc. finns dessa [programexempel](../samples.md).
- Om du vill hitta och åtgärda fel, se [felsökning](../troubleshooting.md).
- Om du vill använda mer avancerade funktioner, se hur du kommer igång med tal [klientbiblioteken](GetStartedClientLibraries.md).

### <a name="license"></a>Licens

Alla kognitiva Services SDK: er och prover licensieras med MIT-licensen. Mer information finns i [licens](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
