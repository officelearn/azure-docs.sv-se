---
title: Tal till text API-referens (REST) – Speech Services
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder tal till text REST API. I den här artikeln får du lära dig om auktorisering, alternativ frågan, hur du strukturerar en begäran och får ett svar.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: baaa7b1068e13863293e0968cb0bf1ffb198882b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60487239"
---
# <a name="speech-to-text-rest-api"></a>Tal till text REST-API

Som ett alternativ till den [tal SDK](speech-sdk.md), Speech Services kan du konvertera tal till text med en REST-API. Varje tillgänglig slutpunkt är associerad med en region. Ditt program kräver en prenumerationsnyckel för den slutpunkt som du tänker använda.

Innan du använder REST-API tal till text, Förstå:
* Begäranden som använder REST API får bara innehålla 10 sekunder för ljud.
* Tal till text REST API: et returnerar endast slutliga resultaten. Ofullständiga resultat tillhandahålls inte.

Om du skickar längre ljud är ett krav för ditt program kan du använda den [tal SDK](speech-sdk.md) eller [batch avskrift](batch-transcription.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>Regioner och slutpunkter

Dessa regioner har stöd för tal till text-avskrift med hjälp av REST-API. Kontrollera att du väljer den slutpunkt som matchar din region för prenumerationen.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

## <a name="query-parameters"></a>Frågeparametrar

Dessa parametrar kan ingå i frågesträngen för REST-begäran.

| Parameter | Beskrivning | Obligatoriskt / valfritt |
|-----------|-------------|---------------------|
| `language` | Identifierar talat språk som tolkas. Se [språk som stöds](language-support.md#speech-to-text). | Krävs |
| `format` | Anger resultatformatet. Godkända värden är `simple` och `detailed`. Enkel resultatet inkluderar `RecognitionStatus`, `DisplayText`, `Offset`, och `Duration`. Detaljerad respons omfattar flera resultat med tillförsikt värden och fyra olika representationer. Standardinställningen är `simple`. | Valfri |
| `profanity` | Anger hur du hanterar svordomar i igenkänningsresultat. Godkända värden är `masked`, som ersätter svordomar med asterisker `removed`, vilket tar bort alla svordomar från resultatet, eller `raw`, som innehåller svordomar i resultatet. Standardinställningen är `masked`. | Valfri |

## <a name="request-headers"></a>Begärandehuvud

Den här tabellen innehåller obligatoriska och valfria rubriker för tal till text-begäranden.

|Huvud| Beskrivning | Obligatoriskt / valfritt |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Prenumerationsnyckeln Speech Services. | Antingen den här rubriken eller `Authorization` krävs. |
| `Authorization` | En autentiseringstoken föregås av ordet `Bearer`. Mer information finns i [Autentisering](#authentication). | Antingen den här rubriken eller `Ocp-Apim-Subscription-Key` krävs. |
| `Content-type` | Beskriver format och codec-enheten för den angivna ljuddata. Godkända värden är `audio/wav; codecs=audio/pcm; samplerate=16000` och `audio/ogg; codecs=opus`. | Krävs |
| `Transfer-Encoding` | Anger att segmenterat ljuddata skickas, i stället för en enskild fil. Använd bara den här rubriken om storlekar ljuddata. | Valfri |
| `Expect` | Om du använder med chunked skicka `Expect: 100-continue`. Speech Services är medveten om den första begäran och väntar på dig ytterligare data.| Krävs om du skickar segmenterade ljuddata. |
| `Accept` | Om det måste vara `application/json`. Speech Services omfattar resultat i JSON. Vissa webbramverk för begäran innehåller ett inkompatibelt standardvärdet om du inte anger något, så det är bra att alltid `Accept`. | Valfritt men rekommenderas. |

## <a name="audio-formats"></a>Ljudformat

Ljud skickas i brödtexten i HTTP `POST` begäran. Det måste vara i något av formaten i den här tabellen:

| Format | Codec | Bithastighet | Samplingshastighet |
|--------|-------|---------|-------------|
| WAV | PCM | 16-bitars | 16 kHz, mono |
| OGG | OPUS | 16-bitars | 16 kHz, mono |

>[!NOTE]
>Formaten ovan stöds via REST-API och WebSocket i Speech Services. Den [tal SDK](speech-sdk.md) för närvarande endast stöd för WAV formatera med PCM-codec.

## <a name="sample-request"></a>Exempelbegäran

Det här är en vanliga HTTP-begäran. Exemplet nedan innehåller värdnamn och nödvändiga rubriker. Det är viktigt att notera att tjänsten förväntar sig även ljuddata som inte ingår i det här exemplet. Som nämnts tidigare, rekommenderas storlekar, men krävs inte.

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
| 200 | Ok | Begäran lyckades. svarstexten är ett JSON-objekt. |
| 400 | Felaktig förfrågan | Språkkod inte eller är inte ett språk som stöds. Ogiltig ljudfil. |
| 401 | Behörighet saknas | Prenumerationsnyckel eller auktorisering token är ogiltig i regionen eller ogiltig slutpunkt. |
| 403 | Förbjudna | Prenumerationsnyckel eller auktorisering saknas token. |

## <a name="chunked-transfer"></a>Segmentvis överföring

Segmentvis överföring (`Transfer-Encoding: chunked`) kan hjälpa dig att minska svarstiden för igenkänning av eftersom den tillåter Taltjänster ska börja bearbeta ljudfilen medan den överförs. REST API: et tillhandahåller inte partiell eller mellanliggande resultat. Det här alternativet är avsedd endast för att förbättra svarstiden.

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
|`DisplayText`|Den tolkade texten efter versaler, skiljetecken, inverterade text normalisering (konvertera tal till kortare former, till exempel 200 för ”tvåhundra” eller ”Dr. Smith ”för” läkare smith ”), och svordomar Maskning. Visa endast om åtgärden lyckades.|
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

Den `detailed` format innehåller samma data som den `simple` format tillsammans med `NBest`, en lista över alternativ tolkningar av samma igenkänningsresultatet. De här resultaten returneras rangordnas från det mest sannolikt minst sannolikt den första posten är samma som den huvudsakliga igenkänningsresultatet.  När du använder den `detailed` format, `DisplayText` tillhandahålls som `Display` för varje resultat i den `NBest` lista.

Varje objekt i den `NBest` listan innehåller:

| Parameter | Beskrivning |
|-----------|-------------|
| `Confidence` | Förtroendepoäng för registerposten från 0,0 (inget förtroende) 1.0 (fullt förtroende) |
| `Lexical` | Lexikal form av den tolkade texten: de ord känns igen. |
| `ITN` | (”Canonical”) inverterade-text-normaliserat form av den tolkade texten med phone tal, tal, förkortningar (”läkare smith” till ”dr smith”) och andra omformningen. |
| `MaskedITN` | Formuläret ITN med svordomar maskning tillämpas, om så krävs. |
| `Display` | Visningsformulär för den tolkade texten med skiljetecken och gemener/versaler har lagts till. Den här parametern är samma som `DisplayText` anges när formatet är inställt på `simple`. |

## <a name="sample-responses"></a>Exempel-svar

Detta är ett typiskt svar för `simple` erkännande.

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Detta är ett typiskt svar för `detailed` erkännande.

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
