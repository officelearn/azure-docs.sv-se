---
title: API-referens för tal till text (REST) - Taltjänst
titleSuffix: Azure Cognitive Services
description: Läs om hur du använder REST-API:et mellan tal och text. I den här artikeln får du lära dig mer om auktoriseringsalternativ, frågealternativ, hur du strukturerar en begäran och får ett svar.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: trbye
ms.openlocfilehash: fbb4d114d1fee21d7950e53b06fc16c96b5c930b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400188"
---
# <a name="speech-to-text-rest-api"></a>REST API för tal-till-text

Som ett alternativ till [Tal-SDK](speech-sdk.md)kan du med taltjänsten konvertera tal till text med hjälp av ett REST API. Varje tillgänglig slutpunkt är associerad med en region. Programmet kräver en prenumerationsnyckel för den slutpunkt som du planerar att använda. REST API är mycket begränsad, och det bör endast användas i fall var [tal SDK](speech-sdk.md) kan inte.

Innan du använder REST API för tal till text, förstå:

* Begäranden som använder REST API och överföra ljud direkt kan bara innehålla upp till 60 sekunder av ljud.
* REST API för tal till text returnerar endast slutresultatet. Partiella resultat tillhandahålls inte.

Om det är ett krav för ditt program att skicka längre ljud bör du överväga att använda [Tal-SDK](speech-sdk.md) eller ett filbaserat REST API, till [exempel batchranskription](batch-transcription.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>Regioner och slutpunkter

Slutpunkten för REST API har följande format:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Ersätt `<REGION_IDENTIFIER>` med identifieraren som matchar regionen för din prenumeration från den här tabellen:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> Språkparametern måste läggas till i URL:en för att undvika att ett 4xx HTTP-fel inte tas emot. Språket som är inställt på amerikansk engelska med slutpunkten för västra USA är till exempel: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`.

## <a name="query-parameters"></a>Frågeparametrar

Dessa parametrar kan inkluderas i frågesträngen för REST-begäran.

| Parameter | Beskrivning | Obligatoriskt/tillval |
|-----------|-------------|---------------------|
| `language` | Identifierar det talade språket som känns igen. Se [Språk som stöds](language-support.md#speech-to-text). | Krävs |
| `format` | Anger resultatformatet. Godkända värden `simple` `detailed`är och . Enkla resultat `RecognitionStatus` `DisplayText`är `Offset`, `Duration`, och . Detaljerade svar inkluderar flera resultat med konfidensvärden och fyra olika representationer. Standardinställningen är `simple`. | Valfri |
| `profanity` | Anger hur svordomar ska hanteras i igenkänningsresultat. Godkända värden `masked`är , som ersätter svordomar med `removed`asterisker, , som tar bort `raw`allt svordomar från resultatet, eller , vilket inkluderar svordomar i resultatet. Standardinställningen är `masked`. | Valfri |
| `cid` | När du använder [portalen Anpassat tal](how-to-custom-speech.md) för att skapa anpassade modeller kan du använda anpassade modeller via deras **slutpunkts-ID** som finns på **distributionssidan.** Använd **slutpunkts-ID** som `cid` argument för frågesträngparametern. | Valfri |

## <a name="request-headers"></a>Begärandehuvuden

I den här tabellen visas obligatoriska och valfria rubriker för tal-till-text-begäranden.

|Huvud| Beskrivning | Obligatoriskt/tillval |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Prenumerationsnyckeln för Taltjänsten. | Antingen det `Authorization` här huvudet eller krävs. |
| `Authorization` | En auktoriseringstoken som föregås av ordet `Bearer`. Mer information finns i [Autentisering](#authentication). | Antingen det `Ocp-Apim-Subscription-Key` här huvudet eller krävs. |
| `Content-type` | Beskriver formatet och codec för de angivna ljuddata. Godkända värden `audio/wav; codecs=audio/pcm; samplerate=16000` `audio/ogg; codecs=opus`är och . | Krävs |
| `Transfer-Encoding` | Anger att segmenterade ljuddata skickas i stället för en enda fil. Använd bara det här huvudet om du segmenterar ljuddata. | Valfri |
| `Expect` | Om du använder segmenterad överföring skickar du `Expect: 100-continue`. Taltjänsten bekräftar den ursprungliga begäran och inväntar ytterligare data.| Krävs om du skickar segmenterade ljuddata. |
| `Accept` | Om det tillhandahålls, `application/json`måste det vara . Taltjänsten ger resultat i JSON. Vissa ramverk för begäran ger ett inkompatibelt standardvärde. Det är god praxis `Accept`att alltid inkludera . | Valfritt, men rekommenderas. |

## <a name="audio-formats"></a>Ljudformat

Ljud skickas i brödtexten `POST` för HTTP-begäran. Den måste vara i något av formaten i den här tabellen:

| Format | Codec | Bitrate | Exempelhastighet  |
|--------|-------|---------|--------------|
| WAV    | PCM   | 16 bitar  | 16 kHz, mono |
| Ogg    | Opus  | 16 bitar  | 16 kHz, mono |

>[!NOTE]
>Ovanstående format stöds via REST API och WebSocket i taltjänsten. [Tal-SDK](speech-sdk.md) stöder för närvarande WAV-formatet med PCM-codec samt [andra format](how-to-use-codec-compressed-audio-input-streams.md).

## <a name="sample-request"></a>Exempel på begäran

Exemplet nedan innehåller värdnamn och obligatoriska rubriker. Det är viktigt att notera att tjänsten också förväntar sig ljuddata, som inte ingår i det här exemplet. Som tidigare nämnts rekommenderas dock inte segmentering.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

## <a name="http-status-codes"></a>HTTP-statuskoder

HTTP-statuskoden för varje svar anger lyckade eller vanliga fel.

| HTTP-statuskod | Beskrivning | Möjlig orsak |
|------------------|-------------|-----------------|
| `100` | Fortsätt | Den ursprungliga begäran har godtagits. Fortsätt med att skicka resten av data. (Används med segmenterad överföring) |
| `200` | OK | Begäran lyckades. svarstexten är ett JSON-objekt. |
| `400` | Felaktig begäran | Språkkod tillhandahålls inte, inte ett språk som stöds, ogiltig ljudfil, etc. |
| `401` | Behörighet saknas | Prenumerationsnyckel eller auktoriseringstoken är ogiltigt i det angivna området eller ogiltig slutpunkt. |
| `403` | Förbjudet | Prenumerationsnyckel eller auktoriseringstoken saknas. |

## <a name="chunked-transfer"></a>Tjockarena överföring

Chunked transfer`Transfer-Encoding: chunked`( ) kan bidra till att minska erkännande svarstiden. Det gör att taltjänsten kan börja bearbeta ljudfilen medan den överförs. REST API ger inte partiella eller interimistiska resultat.

Det här kodexemplet visar hur du skickar ljud i segment. Endast det första segmentet ska innehålla ljudfilens huvud. `request`är `HttpWebRequest` ett objekt som är kopplat till lämplig REST-slutpunkt. `audioFile`är sökvägen till en ljudfil på disken.

```csharp
var request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = host;
request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";
request.AllowWriteStreamBuffering = false;

using (var fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    // Open a request stream and write 1024 byte chunks in the stream one at a time.
    byte[] buffer = null;
    int bytesRead = 0;
    using (var requestStream = request.GetRequestStream())
    {
        // Read 1024 raw bytes from the input audio file.
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        requestStream.Flush();
    }
}
```

## <a name="response-parameters"></a>Svarsparametrar

Resultaten tillhandahålls som JSON. Formatet `simple` innehåller dessa fält på den översta nivån.

| Parameter | Beskrivning  |
|-----------|--------------|
|`RecognitionStatus`|Status, till `Success` exempel för ett lyckat erkännande. Se nästa bord.|
|`DisplayText`|Den tolkade texten efter versaler, interpunktion, omvänd textnormalisering (konvertering av talad text till kortare former, till exempel 200 för "tvåhundra" eller "Dr Smith" för "doctor smith" och svordomar maskering. Närvarande endast på framgång.|
|`Offset`|Tiden (i 100 nanosektenheter) då det erkända talet börjar i ljudströmmen.|
|`Duration`|Varaktigheten (i 100 nanosektenheter) för det erkända talet i ljudströmmen.|

Fältet `RecognitionStatus` kan innehålla följande värden:

| Status | Beskrivning |
|--------|-------------|
| `Success` | Erkännandet lyckades `DisplayText` och fältet är närvarande. |
| `NoMatch` | Tal upptäcktes i ljudströmmen, men inga ord från målspråket matchades. Vanligtvis innebär att igenkänningsspråket är ett annat språk än det som användaren talar. |
| `InitialSilenceTimeout` | I början av ljudströmmen fanns bara tystnad och servicen tajmades i väntan på tal. |
| `BabbleTimeout` | I början av ljudströmmen innehöll endast brus och servicen tajmades i väntan på tal. |
| `Error` | Igenkänningstjänsten påträffade ett internt fel och kunde inte fortsätta. Försök igen om möjligt. |

> [!NOTE]
> Om ljudet bara består av svordomar `profanity` och frågeparametern är inställd `remove`på returnerar tjänsten inte ett talresultat.

Formatet `detailed` innehåller samma data `simple` som formatet, `NBest`tillsammans med , en lista över alternativa tolkningar av samma igenkänningsresultat. Dessa resultat rankas från mest sannolikt att minst sannolikt. Den första posten är samma som det huvudsakliga igenkänningsresultatet.  När du `detailed` använder `DisplayText` formatet, `Display` anges som `NBest` för varje resultat i listan.

Varje objekt `NBest` i listan innehåller:

| Parameter | Beskrivning |
|-----------|-------------|
| `Confidence` | Förtroendet poäng av posten från 0,0 (inget förtroende) till 1,0 (fullt förtroende) |
| `Lexical` | Den lexikala formen av den tolkade texten: de faktiska orden känns igen. |
| `ITN` | Den inverterade text-normaliserade ("kanoniska") formen av den tolkade texten, med telefonnummer, siffror, förkortningar ("doctor smith" till "dr smith" och andra omformningar tillämpas. |
| `MaskedITN` | ITN-formuläret med svordomar maskering tillämpas, om så önskas. |
| `Display` | Visningsformen för den tolkade texten, med interpunktion och versaler tillagda. Den här parametern `DisplayText` är densamma som `simple`när formatet är inställt på . |

## <a name="sample-responses"></a>Exempel på svar

Ett typiskt `simple` svar för erkännande:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Ett typiskt `detailed` svar för erkännande:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

- [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Anpassa akustiska modeller](how-to-customize-acoustic-models.md)
- [Anpassa språkmodeller](how-to-customize-language-model.md)
