---
title: API-referens för text till tal (REST) - Taltjänst
titleSuffix: Azure Cognitive Services
description: Läs om hur du använder REST-API:et för text till tal. I den här artikeln får du lära dig mer om auktoriseringsalternativ, frågealternativ, hur du strukturerar en begäran och får ett svar.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: dapine
ms.openlocfilehash: 977c6ec9aa1cd6a8b8a545d123c5308bb8c51651
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409012"
---
# <a name="text-to-speech-rest-api"></a>Text-till-tal (REST API)

Med taltjänsten kan du [konvertera text till syntetiserat tal](#convert-text-to-speech) och få en lista med röster som [stöds](#get-a-list-of-voices) för en region med hjälp av en uppsättning REST-API:er. Varje tillgänglig slutpunkt är associerad med en region. En prenumerationsnyckel för slutpunkten/regionen som du planerar att använda krävs.

REST API för text-till-tal stöder neurala och standardtext-till-tal-röster, som alla stöder ett visst språk och dialekt, som identifieras av språk.

* En fullständig lista med röster finns i [språkstöd](language-support.md#text-to-speech).
* Information om regional tillgänglighet finns i [regioner](regions.md#text-to-speech).

> [!IMPORTANT]
> Kostnaderna varierar för standard, anpassade och neurala röster. Mer information finns i [Prissättning](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Innan du använder det här API:et, förstå:

* REST API för text-till-tal kräver ett auktoriseringshuvud. Det innebär att du måste slutföra ett tokenutbyte för att komma åt tjänsten. Mer information finns i [Autentisering](#authentication).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Få en lista med röster

Slutpunkten `voices/list` kan du få en fullständig lista med röster för en viss region / slutpunkt.

### <a name="regions-and-endpoints"></a>Regioner och slutpunkter

| Region | Slutpunkt |
|--------|----------|
| Australien, östra | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Brasilien, södra | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Kanada, centrala | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA, centrala | `https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Asien, östra | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA, östra | `https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA, östra 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Frankrike, centrala | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Indien, centrala | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Japan, östra | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Sydkorea, centrala | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA, norra centrala | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Europa, norra | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA, södra centrala | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Sydostasien | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Storbritannien, södra | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Europa, västra | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA, västra | `https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA, västra 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

### <a name="request-headers"></a>Begärandehuvuden

I den här tabellen visas obligatoriska och valfria rubriker för text-till-tal-begäranden.

| Huvud | Beskrivning | Obligatoriskt/tillval |
|--------|-------------|---------------------|
| `Authorization` | En auktoriseringstoken som föregås av ordet `Bearer`. Mer information finns i [Autentisering](#authentication). | Krävs |

### <a name="request-body"></a>Begärandetext

En kropp krävs inte `GET` för begäranden till den här slutpunkten.

### <a name="sample-request"></a>Exempel på begäran

Den här begäran kräver bara ett auktoriseringshuvud.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Authorization: Bearer [Base64 access_token]
```

### <a name="sample-response"></a>Exempelsvar

Detta svar har trunkerats för att illustrera strukturen för ett svar.

> [!NOTE]
> Rösttillgängligheten varierar beroende på region/slutpunkt.

```json
[
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
        "ShortName": "ar-EG-Hoda",
        "Gender": "Female",
        "Locale": "ar-EG",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)",
        "ShortName": "ar-SA-Naayf",
        "Gender": "Male",
        "Locale": "ar-SA",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)",
        "ShortName": "bg-BG-Ivan",
        "Gender": "Male",
        "Locale": "bg-BG",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)",
        "ShortName": "ca-ES-HerenaRUS",
        "Gender": "Female",
        "Locale": "ca-ES",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (zh-CN, XiaoxiaoNeural)",
        "ShortName": "zh-CN-XiaoxiaoNeural",
        "Gender": "Female",
        "Locale": "zh-CN",
        "SampleRateHertz": "24000",
        "VoiceType": "Neural"
    },

    ...
]
```

### <a name="http-status-codes"></a>HTTP-statuskoder

HTTP-statuskoden för varje svar anger lyckade eller vanliga fel.

| HTTP-statuskod | Beskrivning | Möjlig orsak |
|------------------|-------------|-----------------|
| 200 | OK | Begäran lyckades. |
| 400 | Felaktig begäran | En obligatorisk parameter saknas, är tom eller null. Eller så är värdet som skickas till en obligatorisk eller valfri parameter ogiltig. Ett vanligt problem är en rubrik som är för lång. |
| 401 | Behörighet saknas | Begäran är inte auktoriserad. Kontrollera att din prenumerationsnyckel eller token är giltig och i rätt region. |
| 429 | För många förfrågningar | Du har överskridit kvoten eller frekvensen av begäranden som tillåts för din prenumeration. |
| 502 | Felaktig gateway    | Problem med nätverk eller server. Kan också indikera ogiltiga rubriker. |


## <a name="convert-text-to-speech"></a>Konvertera text-till-tal

Ändpunkten `v1` kan du konvertera text-till-tal med hjälp av [talsyntes markup språk (SSML)](speech-synthesis-markup.md).

### <a name="regions-and-endpoints"></a>Regioner och slutpunkter

Dessa regioner stöds för text-till-tal med REST API. Se till att du väljer den slutpunkt som matchar din prenumerationsregion.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>Begärandehuvuden

I den här tabellen visas obligatoriska och valfria rubriker för text-till-tal-begäranden.

| Huvud | Beskrivning | Obligatoriskt/tillval |
|--------|-------------|---------------------|
| `Authorization` | En auktoriseringstoken som föregås av ordet `Bearer`. Mer information finns i [Autentisering](#authentication). | Krävs |
| `Content-Type` | Anger innehållstypen för den angivna texten. Godkänt värde: `application/ssml+xml`. | Krävs |
| `X-Microsoft-OutputFormat` | Anger ljudutdataformatet. En fullständig lista över godkända värden finns i [ljudutdata](#audio-outputs). | Krävs |
| `User-Agent` | Programnamnet. Värdet måste vara mindre än 255 tecken. | Krävs |

### <a name="audio-outputs"></a>Ljudutgångar

Det här är en lista över ljudformat som `X-Microsoft-OutputFormat` stöds som skickas i varje begäran som sidhuvud. Varje innehåller en bitrate och kodning typ. Taltjänsten stöder ljudutgångar på 24 kHz, 16 kHz och 8 kHz.

|||
|-|-|
| `raw-16khz-16bit-mono-pcm` | `raw-8khz-8bit-mono-mulaw` |
| `riff-8khz-8bit-mono-alaw` | `riff-8khz-8bit-mono-mulaw` |
| `riff-16khz-16bit-mono-pcm` | `audio-16khz-128kbitrate-mono-mp3` |
| `audio-16khz-64kbitrate-mono-mp3` | `audio-16khz-32kbitrate-mono-mp3` |
| `raw-24khz-16bit-mono-pcm` | `riff-24khz-16bit-mono-pcm` |
| `audio-24khz-160kbitrate-mono-mp3` | `audio-24khz-96kbitrate-mono-mp3` |
| `audio-24khz-48kbitrate-mono-mp3` | |

> [!NOTE]
> Om det valda röst- och utdataformatet har olika bithastigheter samplas ljudet om efter behov. 24 kHz-röster stöder `audio-16khz-16kbps-mono-siren` dock `riff-16khz-16kbps-mono-siren` inte och utdataformat.

### <a name="request-body"></a>Begärandetext

Brödtexten `POST` för varje begäran skickas som [SSML (Speech Synthesis Markup Language).](speech-synthesis-markup.md) Med SSML kan du välja röst och språk för det syntetiserade tal som returneras av text-till-tal-tjänsten. En fullständig lista över röster som stöds finns i [språkstöd](language-support.md#text-to-speech).

> [!NOTE]
> Om du använder en anpassad röst kan brödtexten i en begäran skickas som oformaterad text (ASCII eller UTF-8).

### <a name="sample-request"></a>Exempel på begäran

Den här HTTP-begäran använder SSML för att ange röst och språk. Om kroppslängden är lång, och det resulterande ljudet överstiger 10 minuter - det kortas till 10 minuter. Med andra ord får ljudlängden inte överstiga 10 minuter.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='en-US-AriaRUS'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

Se våra snabbstarter för språkspecifika exempel:

* [.NET-kärna, C #](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=dotnetcore)
* [Python](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-python)
* [Node.js](quickstart-nodejs-text-to-speech.md)

### <a name="http-status-codes"></a>HTTP-statuskoder

HTTP-statuskoden för varje svar anger lyckade eller vanliga fel.

| HTTP-statuskod | Beskrivning | Möjlig orsak |
|------------------|-------------|-----------------|
| 200 | OK | Begäran lyckades. svarstexten är en ljudfil. |
| 400 | Felaktig begäran | En obligatorisk parameter saknas, är tom eller null. Eller så är värdet som skickas till en obligatorisk eller valfri parameter ogiltig. Ett vanligt problem är en rubrik som är för lång. |
| 401 | Behörighet saknas | Begäran är inte auktoriserad. Kontrollera att din prenumerationsnyckel eller token är giltig och i rätt region. |
| 413 | Begär entiteten för stor | SSML-ingången är längre än 1024 tecken. |
| 415 | Medietyp som inte stöds | Det är möjligt att `Content-Type` fel angavs. `Content-Type`bör ställas `application/ssml+xml`in på . |
| 429 | För många förfrågningar | Du har överskridit kvoten eller frekvensen av begäranden som tillåts för din prenumeration. |
| 502 | Felaktig gateway    | Problem med nätverk eller server. Kan också indikera ogiltiga rubriker. |

Om HTTP-statusen är `200 OK`innehåller brödtexten i svaret en ljudfil i det begärda formatet. Den här filen kan spelas upp när den överförs, sparas i en buffert eller sparas i en fil.

## <a name="next-steps"></a>Nästa steg

- [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services)
- [Asynkron syntes för långformigt ljud](quickstarts/text-to-speech/async-synthesis-long-form-audio.md)
- [Komma igång med Custom Voice](how-to-custom-voice.md)
