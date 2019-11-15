---
title: Tal-till-text API-referens (REST) – tal service
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder tal-till-text-REST API. I den här artikeln får du lära dig om auktorisering, alternativ frågan, hur du strukturerar en begäran och får ett svar.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 137ab722df280d17fe5ccc5c07acfd323feb6531
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091214"
---
# <a name="speech-to-text-rest-api"></a>REST API för tal-till-text

Som ett alternativ till [tal-SDK](speech-sdk.md)gör det möjligt för tal tjänster att konvertera tal till text med hjälp av en REST API. Varje tillgänglig slutpunkt är associerad med en region. Ditt program kräver en prenumerationsnyckel för den slutpunkt som du tänker använda.

Innan du använder tal-till-text-REST API förstå:

* Begär Anden som använder REST API och sändning av ljud direkt får bara innehålla upp till 60 sekunders ljud.
* Tal till text REST API: et returnerar endast slutliga resultaten. Ofullständiga resultat tillhandahålls inte.

Om det är ett krav för ditt program att skicka längre ljud bör du överväga att använda [talet SDK](speech-sdk.md) eller en filbaserad REST API, t. ex. [batch-avskriftering](batch-transcription.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>Regioner och slutpunkter

Dessa regioner har stöd för tal till text-avskrift med hjälp av REST-API. Kontrollera att du väljer den slutpunkt som matchar din region för prenumerationen.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)] 

## <a name="query-parameters"></a>Frågeparametrar

Dessa parametrar kan ingå i frågesträngen för REST-begäran.

| Parameter | Beskrivning | Obligatoriskt / valfritt |
|-----------|-------------|---------------------|
| `language` | Identifierar talat språk som tolkas. Se [språk som stöds](language-support.md#speech-to-text). | Krävs |
| `format` | Anger resultatformatet. Godkända värden är `simple` och `detailed`. Enkel resultatet inkluderar `RecognitionStatus`, `DisplayText`, `Offset`, och `Duration`. Detaljerad respons omfattar flera resultat med tillförsikt värden och fyra olika representationer. Standardinställningen är `simple`. | Valfritt |
| `profanity` | Anger hur du hanterar svordomar i igenkänningsresultat. Godkända värden är `masked`, vilket ersätter svordomar med asterisker, `removed`, vilket tar bort alla svordomar från resultatet, eller `raw`, som innehåller svordomarna i resultatet. Standardinställningen är `masked`. | Valfritt |

## <a name="request-headers"></a>Begärandehuvud

Den här tabellen innehåller obligatoriska och valfria rubriker för tal till text-begäranden.

|Huvud| Beskrivning | Obligatoriskt / valfritt |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Din prenumerations nyckel för tal tjänster. | Antingen den här rubriken eller `Authorization` krävs. |
| `Authorization` | En autentiseringstoken föregås av ordet `Bearer`. Mer information finns i [Autentisering](#authentication). | Antingen den här rubriken eller `Ocp-Apim-Subscription-Key` krävs. |
| `Content-type` | Beskriver format och codec-enheten för den angivna ljuddata. Godkända värden är `audio/wav; codecs=audio/pcm; samplerate=16000` och `audio/ogg; codecs=opus`. | Krävs |
| `Transfer-Encoding` | Anger att segmenterat ljuddata skickas, i stället för en enskild fil. Använd bara den här rubriken om storlekar ljuddata. | Valfritt |
| `Expect` | Om du använder med chunked skicka `Expect: 100-continue`. Tal tjänsterna bekräftar den första begäran och väntar på ytterligare data.| Krävs om du skickar segmenterade ljuddata. |
| `Accept` | Om det måste vara `application/json`. Tal tjänsterna tillhandahåller resultat i JSON. Vissa ramverk för begäran tillhandahåller ett inkompatibelt standardvärde. Det är en bra idé att alltid inkludera `Accept`. | Valfritt men rekommenderas. |

## <a name="audio-formats"></a>Ljudformat

Ljud skickas i brödtexten i HTTP `POST` begäran. Det måste vara i något av formaten i den här tabellen:

| Format | Codec | Bithastighet | Samplingshastighet |
|--------|-------|---------|-------------|
| WAV | PCM | 16-bitars | 16 kHz, mono |
| OGG | OPUS | 16-bitars | 16 kHz, mono |

>[!NOTE]
>Ovanstående format stöds via REST API och WebSocket i tal tjänsterna. Den [tal SDK](speech-sdk.md) för närvarande endast stöd för WAV formatera med PCM-codec.

## <a name="sample-request"></a>Exempelbegäran

Exemplet nedan innehåller värdnamn och nödvändiga rubriker. Det är viktigt att notera att tjänsten förväntar sig även ljuddata som inte ingår i det här exemplet. Som nämnts tidigare, rekommenderas storlekar, men krävs inte.

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

HTTP-statuskod för varje svar anger lyckad eller vanliga fel.

| HTTP-statuskod | Beskrivning | Möjlig orsak |
|------------------|-------------|-----------------|
| 100 | Fortsätt | Den första begäran har godkänts. Gå vidare med att skicka resten av data. (Används med segmentvis överföring). |
| 200 | OK | Begäran lyckades. svarstexten är ett JSON-objekt. |
| 400 | Felaktig förfrågan | Språk koden har inte angetts, inte ett språk som stöds, ogiltig ljudfil osv. |
| 401 | Behörighet saknas | Prenumerationsnyckel eller auktorisering token är ogiltig i regionen eller ogiltig slutpunkt. |
| 403 | Förbjudet | Prenumerationsnyckel eller auktorisering saknas token. |

## <a name="chunked-transfer"></a>Segmentvis överföring

Segment överföring (`Transfer-Encoding: chunked`) hjälper till att minska svars tiden för igenkänning. Det gör att tal tjänsterna kan börja bearbeta ljud filen medan den överförs. REST API: et tillhandahåller inte partiell eller mellanliggande resultat.

Detta kodexempel visar hur du skickar ljud i segment. Endast det första segmentet ska innehålla ljud filens huvud. `request` ett objekt i HTTPWebRequest är ansluten till rätt REST-slutpunkten. `audioFile` är sökvägen till en ljudfil på disken.

```csharp

    HttpWebRequest request = null;
    request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
    request.SendChunked = true;
    request.Accept = @"application/json;text/xml";
    request.Method = "POST";
    request.ProtocolVersion = HttpVersion.Version11;
    request.Host = host;
    request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
    request.Headers["Ocp-Apim-Subscription-Key"] = args[1];
    request.AllowWriteStreamBuffering = false;

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

## <a name="response-parameters"></a>Svarsparametrar

Resultaten anges som JSON. Den `simple` format innehåller fälten på översta nivån.

| Parameter | Beskrivning  |
|-----------|--------------|
|`RecognitionStatus`|Status, till exempel `Success` för lyckad erkännande. Se nästa tabell.|
|`DisplayText`|Den tolkade texten efter versaler, interpunktion, inverterad text normalisering (konvertering av talade text till kortare former, till exempel 200 för "200" eller "Dr. Smith" för "läkare Smith") och svordomar. Visa endast om åtgärden lyckades.|
|`Offset`|Tid (i 100 nanosekunder enheter) som okänt tal som börjar gälla i ljudströmmen.|
|`Duration`|Tiden (i 100 nanosekunder enheter) för den identifierade tal i ljudströmmen.|

Den `RecognitionStatus` fältet får innehålla dessa värden:

| Status | Beskrivning |
|--------|-------------|
| `Success` | Erkännande lyckades och `DisplayText` fältet finns. |
| `NoMatch` | Tal påträffades i ljudströmmen, men inga ord från målspråket som kunde matchas. Oftast igenkänning av språket är ett annat språk än den användaren som talar. |
| `InitialSilenceTimeout` | Början av ljudströmmen innehöll endast tystnad och tjänsten tidsgränsen för tal. |
| `BabbleTimeout` | Början av ljudströmmen innehöll endast bruset och tjänsten tidsgränsen för tal. |
| `Error` | Igenkänning av tjänsten påträffade ett internt fel och kunde inte fortsätta. Försök igen om det är möjligt. |

> [!NOTE]
> Om ljudet består endast av svordomar, och `profanity` Frågeparametern anges till `remove`, tjänsten inte returnerar ett tal resultat.

`detailed`-formatet innehåller samma data som `simple`-formatet, tillsammans med `NBest`, en lista över alternativa tolkningar av samma resultat. De här resultaten rangordnas från de mest sannolikaste sannolika. Den första posten är samma som det huvudsakliga igenkännings resultatet.  När du använder den `detailed` format, `DisplayText` tillhandahålls som `Display` för varje resultat i den `NBest` lista.

Varje objekt i den `NBest` listan innehåller:

| Parameter | Beskrivning |
|-----------|-------------|
| `Confidence` | Förtroendepoäng för registerposten från 0,0 (inget förtroende) 1.0 (fullt förtroende) |
| `Lexical` | Lexikal form av den tolkade texten: de ord känns igen. |
| `ITN` | (”Canonical”) inverterade-text-normaliserat form av den tolkade texten med phone tal, tal, förkortningar (”läkare smith” till ”dr smith”) och andra omformningen. |
| `MaskedITN` | Formuläret ITN med svordomar maskning tillämpas, om så krävs. |
| `Display` | Visningsformulär för den tolkade texten med skiljetecken och gemener/versaler har lagts till. Den här parametern är samma som `DisplayText` anges när formatet är inställt på `simple`. |

## <a name="sample-responses"></a>Exempel-svar

Ett typiskt svar för `simple` igenkänning:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Ett typiskt svar för `detailed` igenkänning:

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
