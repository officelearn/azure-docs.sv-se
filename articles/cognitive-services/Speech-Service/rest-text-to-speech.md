---
title: Text till tal-API-referens (REST) – Speech Services
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder text till tal REST-API. I den här artikeln får du lära dig om auktorisering, alternativ frågan, hur du strukturerar en begäran och får ett svar.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 71710cd940aad3a56dae6c19d4d52a5b141b3d80
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60480547"
---
# <a name="text-to-speech-rest-api"></a>Text till tal REST-API

Speech Services kan du [Omvandla text till tal syntetiserade](#convert-text-to-speech) och [hämta en lista över stöds röster](#get-a-list-of-voices) för en region med en uppsättning REST API: er. Varje tillgänglig slutpunkt är associerad med en region. Det krävs en prenumerationsnyckel för slutpunkten/region som du tänker använda.

Text till tal REST API har stöd för neurala och standard text till tal röster, som stöder ett visst språk och dialekt som identifieras av nationella inställningar.

* En fullständig lista över röster Se [språkstöd](language-support.md#text-to-speech).
* Information om regional tillgänglighet finns i [regioner](regions.md#text-to-speech).

> [!IMPORTANT]
> Kostnader för olika för standard, anpassade och neural röster. Mer information finns i [priser](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Innan du använder detta API kan förstå:

* Text till tal REST-API kräver ingen auktoriseringsrubrik. Det innebär att du måste utföra en token exchange för att komma åt tjänsten. Mer information finns i [Autentisering](#authentication).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Hämta en lista över röster

Den `voices/list` slutpunkt kan du få en fullständig lista över röster för en viss region/slutpunkt.

### <a name="regions-and-endpoints"></a>Regioner och slutpunkter

| Region | Slutpunkt |
|--------|----------|
| Östra Australien | https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Södra Brasilien | https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Centrala Kanada | https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Centrala USA | https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Östasien | https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Östra USA | https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list |
| USA, östra 2 | https://eastus2.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Frankrike, centrala | https://francecentral.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Centrala Indien | https://centralindia.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Östra Japan | https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Sydkorea, centrala | https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Norra centrala USA | https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Norra Europa | https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Södra centrala USA | https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Sydostasien | https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Storbritannien, södra | https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Västra Europa | https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Västra USA | https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Västra USA 2 | https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list |

### <a name="request-headers"></a>Begärandehuvud

Den här tabellen innehåller obligatoriska och valfria rubriker för text till tal-begäranden.

| Huvud | Beskrivning | Obligatoriskt / valfritt |
|--------|-------------|---------------------|
| `Authorization` | En autentiseringstoken föregås av ordet `Bearer`. Mer information finns i [Autentisering](#authentication). | Obligatoriskt |

### <a name="request-body"></a>Begärandetext

En brödtext krävs inte för `GET` begäranden till den här slutpunkten.

### <a name="sample-request"></a>Exempelbegäran

Den här begäran kräver bara en auktoriseringsrubrik.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Authorization: Bearer [Base64 access_token]
```

### <a name="sample-response"></a>Exempelsvar

Det här svaret har trunkerats för att illustrera en struktur för ett svar.

> [!NOTE]
> Röst tillgänglighet varierar beroende på region/slutpunkt.

```json
[
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
        "ShortName": "ar-EG-Hoda",
        "Gender": "Female",
        "Locale": "ar-EG"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)",
        "ShortName": "ar-SA-Naayf",
        "Gender": "Male",
        "Locale": "ar-SA"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)",
        "ShortName": "bg-BG-Ivan",
        "Gender": "Male",
        "Locale": "bg-BG"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)",
        "ShortName": "ca-ES-HerenaRUS",
        "Gender": "Female",
        "Locale": "ca-ES"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (cs-CZ, Jakub)",
        "ShortName": "cs-CZ-Jakub",
        "Gender": "Male",
        "Locale": "cs-CZ"
    },

    ...

]
```

### <a name="http-status-codes"></a>HTTP-statuskoder

HTTP-statuskod för varje svar anger lyckad eller vanliga fel.

| HTTP-statuskod | Beskrivning | Möjlig orsak |
|------------------|-------------|-----------------|
| 200 | Ok | Begäran lyckades. |
| 400 | Felaktig begäran | En obligatorisk parameter är tom, null eller saknas. Eller värdet som skickas till antingen en obligatorisk eller valfri parameter är ogiltig. Ett vanligt problem är en rubrik som är för lång. |
| 401 | Behörighet saknas | Begäran har inte behörighet. Kontrollera att din prenumerationsnyckel eller token är giltig och i rätt region. |
| 429 | För många begäranden | Du har överskridit kvoten eller antalet begäranden som tillåts för din prenumeration. |
| 502 | Felaktig gateway | Problem med nätverket eller servern. Kan också vara ogiltiga sidhuvuden. |


## <a name="convert-text-to-speech"></a>Konvertera text-till-tal

Den `v1` slutpunkt kan du konvertera text till tal med [tal syntes Markup Language (SSML)](speech-synthesis-markup.md).

### <a name="regions-and-endpoints"></a>Regioner och slutpunkter

Dessa regioner har stöd för text till tal med hjälp av REST-API. Kontrollera att du väljer den slutpunkt som matchar din region för prenumerationen.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>Begärandehuvud

Den här tabellen innehåller obligatoriska och valfria rubriker för text till tal-begäranden.

| Huvud | Beskrivning | Obligatoriskt / valfritt |
|--------|-------------|---------------------|
| `Authorization` | En autentiseringstoken föregås av ordet `Bearer`. Mer information finns i [Autentisering](#authentication). | Obligatoriskt |
| `Content-Type` | Anger innehållstypen för den angivna texten. Godkänt värde: `application/ssml+xml`. | Krävs |
| `X-Microsoft-OutputFormat` | Anger formatet för ljuduppspelning. En fullständig lista över godkända värden, se [ljud utdata](#audio-outputs). | Krävs |
| `User-Agent` | Programnamnet. Det angivna värdet måste vara mindre än 255 tecken. | Obligatoriskt |

### <a name="audio-outputs"></a>Ljud utdata

Detta är en lista över de format som ljud som skickas i varje begäran som den `X-Microsoft-OutputFormat` rubrik. Var och en innehåller en bithastigheten och kodningstyp. Speech Services stöder 24 KHz, 16 KHz och 8 KHz ljud matar ut.

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
> Om din valda röst- och utdataformat har olika bithastigheter, samplas ljudet efter behov. Dock stöder inte 24khz röster `audio-16khz-16kbps-mono-siren` och `riff-16khz-16kbps-mono-siren` utdataformat.

### <a name="request-body"></a>Begärandetext

Brödtexten i var och en `POST` begäran skickas som [tal syntes Markup Language (SSML)](speech-synthesis-markup.md). SSML kan du välja rösten och språket för syntetiskt tal returneras av text till tal-tjänsten. En fullständig lista över stöds röster Se [språkstöd](language-support.md#text-to-speech).

> [!NOTE]
> Om du använder en anpassad röst kan brödtexten i en begäran skickas som oformaterad text (ASCII- eller UTF-8).

### <a name="sample-request"></a>Exempelbegäran

Den här HTTP-begäran använder SSML för att ange röst- och språk. Brödtexten får inte överskrida 1 000 tecken.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

Se våra snabbstarter för språkspecifika exempel:

* [.NET Core, C#](quickstart-dotnet-text-to-speech.md)
* [Python](quickstart-python-text-to-speech.md)
* [Node.js](quickstart-nodejs-text-to-speech.md)

### <a name="http-status-codes"></a>HTTP-statuskoder

HTTP-statuskod för varje svar anger lyckad eller vanliga fel.

| HTTP-statuskod | Beskrivning | Möjlig orsak |
|------------------|-------------|-----------------|
| 200 | Ok | Begäran lyckades. svarstexten är en ljudfil. |
| 400 | Felaktig begäran | En obligatorisk parameter är tom, null eller saknas. Eller värdet som skickas till antingen en obligatorisk eller valfri parameter är ogiltig. Ett vanligt problem är en rubrik som är för lång. |
| 401 | Behörighet saknas | Begäran har inte behörighet. Kontrollera att din prenumerationsnyckel eller token är giltig och i rätt region. |
| 413 | Begäran om entiteten är för stor | SSML-indata är längre än 1024 tecken. |
| 429 | För många begäranden | Du har överskridit kvoten eller antalet begäranden som tillåts för din prenumeration. |
| 502 | Felaktig gateway | Problem med nätverket eller servern. Kan också vara ogiltiga sidhuvuden. |

Om HTTP-status är `200 OK`, brödtexten i svaret innehåller en ljudfil i det begärda formatet. Den här filen kan spelas upp när den har överförts, sparas i en buffert eller sparas som en fil.

## <a name="next-steps"></a>Nästa steg

- [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Anpassa akustiska modeller](how-to-customize-acoustic-models.md)
- [Anpassa språkmodeller](how-to-customize-language-model.md)
