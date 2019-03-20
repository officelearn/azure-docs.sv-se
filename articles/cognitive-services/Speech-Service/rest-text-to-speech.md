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
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: d67d8462c177d19dfa3cebbd0b4b000fbe3f41b8
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2019
ms.locfileid: "57894983"
---
# <a name="text-to-speech-rest-api"></a>Text till tal REST-API

Speech Services kan du omvandla text till tal med en REST-API. Varje tillgänglig slutpunkt är associerad med en region. Ditt program kräver en prenumerationsnyckel för den slutpunkt som du tänker använda. Text till tal REST API har stöd för neurala och standard text till tal röster, som stöder ett visst språk och dialekt som identifieras av nationella inställningar.

* En fullständig lista över röster Se [språkstöd](language-support.md#text-to-speech).
* Information om regional tillgänglighet finns i [regioner](regions.md#text-to-speech).

> [!IMPORTANT]
> Kostnader för olika för standard, anpassade och neural röster. Mer information finns i [priser](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Innan du använder detta API kan förstå:

* Text till tal REST-API kräver ingen auktoriseringsrubrik. Det innebär att du måste utföra en token exchange för att komma åt tjänsten. Mer information finns i [Autentisering](#authentication).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="request-headers"></a>Begärandehuvud

Den här tabellen innehåller obligatoriska och valfria rubriker för tal till text-begäranden.

| Huvud | Beskrivning | Obligatoriskt / valfritt |
|--------|-------------|---------------------|
| `Authorization` | En autentiseringstoken föregås av ordet `Bearer`. Mer information finns i [Autentisering](#authentication). | Krävs |
| `Content-Type` | Anger innehållstypen för den angivna texten. Godkänt värde: `application/ssml+xml`. | Krävs |
| `X-Microsoft-OutputFormat` | Anger formatet för ljuduppspelning. En fullständig lista över godkända värden, se [ljud utdata](#audio-outputs). | Krävs |
| `User-Agent` | Programnamnet. Det angivna värdet måste vara mindre än 255 tecken. | Krävs |

## <a name="audio-outputs"></a>Ljud utdata

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

## <a name="request-body"></a>Begärandetext

Brödtexten i var och en `POST` begäran skickas som [tal syntes Markup Language (SSML)](speech-synthesis-markup.md). SSML kan du välja rösten och språket för syntetiskt tal returneras av text till tal-tjänsten. En fullständig lista över stöds röster Se [språkstöd](language-support.md#text-to-speech).

> [!NOTE]
> Om du använder en anpassad röst kan brödtexten i en begäran skickas som oformaterad text (ASCII- eller UTF-8).

## <a name="sample-request"></a>Exempelbegäran

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

## <a name="http-status-codes"></a>HTTP-statuskoder

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
