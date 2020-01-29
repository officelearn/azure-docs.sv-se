---
title: Text till tal-API-referens (REST) – tal service
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder text-till-tal-REST API. I den här artikeln får du lära dig om auktorisering, alternativ frågan, hur du strukturerar en begäran och får ett svar.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: ab0891653f449b13f50dc43b196cf16a2f71370e
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975830"
---
# <a name="text-to-speech-rest-api"></a>Text-till-tal (REST API)

Med röst tjänsten kan du [konvertera text till syntetiskt tal](#convert-text-to-speech) och [Hämta en lista över röster som stöds](#get-a-list-of-voices) för en region med en uppsättning REST API: er. Varje tillgänglig slut punkt är associerad med en region. En prenumerations nyckel för den slut punkt/region som du planerar att använda måste anges.

Text till tal-REST API stöder neurala och standard text till tal-röster, som var och en har stöd för ett särskilt språk och dialekt, som identifieras av locale.

* En fullständig lista över röster finns i [språk stöd](language-support.md#text-to-speech).
* Information om regional tillgänglighet finns i [regioner](regions.md#text-to-speech).

> [!IMPORTANT]
> Kostnaderna varierar för standard-, anpassad-och neurala-röster. Mer information finns i [prissättning](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Innan du använder det här API: et, förstå:

* Text till tal REST-API kräver ingen auktoriseringsrubrik. Det innebär att du måste utföra en token exchange för att komma åt tjänsten. Mer information finns i [Autentisering](#authentication).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Hämta en lista över röster

Med `voices/list` slut punkten kan du få en fullständig lista över röster för en bestämd region eller slut punkt.

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

### <a name="request-headers"></a>Begärandehuvud

I den här tabellen listas obligatoriska och valfria rubriker för text till tal-begäranden.

| Huvud | Beskrivning | Obligatoriskt / valfritt |
|--------|-------------|---------------------|
| `Authorization` | En autentiseringstoken föregås av ordet `Bearer`. Mer information finns i [Autentisering](#authentication). | Krävs |

### <a name="request-body"></a>Begärandetext

Det krävs ingen brödtext för att `GET` begär anden till den här slut punkten.

### <a name="sample-request"></a>Exempelbegäran

Den här begäran kräver bara ett Authorization-huvud.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Authorization: Bearer [Base64 access_token]
```

### <a name="sample-response"></a>Exempelsvar

Svaret har trunkerats för att illustrera ett svars struktur.

> [!NOTE]
> Röst tillgänglighet varierar beroende på region/slut punkt.

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
| 200 | OK | Begäran lyckades. |
| 400 | Felaktig förfrågan | En obligatorisk parameter är tom, null eller saknas. Eller värdet som skickas till antingen en obligatorisk eller valfri parameter är ogiltig. Ett vanligt problem är en rubrik som är för lång. |
| 401 | Behörighet saknas | Begäran har inte behörighet. Kontrollera att din prenumerationsnyckel eller token är giltig och i rätt region. |
| 429 | För många begäranden | Du har överskridit kvoten eller antalet begäranden som tillåts för din prenumeration. |
| 502 | Felaktig gateway | Problem med nätverket eller servern. Kan också vara ogiltiga sidhuvuden. |


## <a name="convert-text-to-speech"></a>Konvertera text-till-tal

Med `v1`-slutpunkten kan du konvertera text till tal med [SSML (Speech syntes Markup Language)](speech-synthesis-markup.md).

### <a name="regions-and-endpoints"></a>Regioner och slutpunkter

Dessa regioner har stöd för text till tal med hjälp av REST-API. Kontrollera att du väljer den slutpunkt som matchar din region för prenumerationen.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>Begärandehuvud

I den här tabellen listas obligatoriska och valfria rubriker för text till tal-begäranden.

| Huvud | Beskrivning | Obligatoriskt / valfritt |
|--------|-------------|---------------------|
| `Authorization` | En autentiseringstoken föregås av ordet `Bearer`. Mer information finns i [Autentisering](#authentication). | Krävs |
| `Content-Type` | Anger innehållstypen för den angivna texten. Godkänt värde: `application/ssml+xml`. | Krävs |
| `X-Microsoft-OutputFormat` | Anger formatet för ljuduppspelning. En fullständig lista över godkända värden, se [ljud utdata](#audio-outputs). | Krävs |
| `User-Agent` | Programnamnet. Det tillhandahållna värdet måste vara mindre än 255 tecken. | Krävs |

### <a name="audio-outputs"></a>Ljud utdata

Detta är en lista över de format som ljud som skickas i varje begäran som den `X-Microsoft-OutputFormat` rubrik. Var och en innehåller en bithastigheten och kodningstyp. Tal tjänsten har stöd för 24 kHz-, 16 kHz-och 8 kHz-ljudutdata.

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
> Om din valda röst- och utdataformat har olika bithastigheter, samplas ljudet efter behov. 24 kHz-röster stöder dock inte `audio-16khz-16kbps-mono-siren` och `riff-16khz-16kbps-mono-siren` utdataformat.

### <a name="request-body"></a>Begärandetext

Bröd texten i varje `POST`-begäran skickas som [SSML (Speech syntes Markup Language)](speech-synthesis-markup.md). Med SSML kan du välja röst och språk för det syntetiskt tal som returneras av text till tal-tjänsten. En fullständig lista över vilka röster som stöds finns i [språk stöd](language-support.md#text-to-speech).

> [!NOTE]
> Om du använder en anpassad röst kan bröd texten i en begäran skickas som oformaterad text (ASCII eller UTF-8).

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
    name='en-US-JessaRUS'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

Se våra snabb starter för språkspecifika exempel:

* [.NET Core,C#](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=dotnetcore)
* [Python](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-python)
* [Node.js](quickstart-nodejs-text-to-speech.md)

### <a name="http-status-codes"></a>HTTP-statuskoder

HTTP-statuskod för varje svar anger lyckad eller vanliga fel.

| HTTP-statuskod | Beskrivning | Möjlig orsak |
|------------------|-------------|-----------------|
| 200 | OK | Begäran lyckades. svarstexten är en ljudfil. |
| 400 | Felaktig förfrågan | En obligatorisk parameter är tom, null eller saknas. Eller värdet som skickas till antingen en obligatorisk eller valfri parameter är ogiltig. Ett vanligt problem är en rubrik som är för lång. |
| 401 | Behörighet saknas | Begäran har inte behörighet. Kontrollera att din prenumerationsnyckel eller token är giltig och i rätt region. |
| 413 | Begäran om entiteten är för stor | SSML-indata är längre än 1024 tecken. |
| 415 | Medie typen stöds inte | Det är möjligt att fel `Content-Type` tillhandahölls. `Content-Type` ska anges till `application/ssml+xml`. |
| 429 | För många begäranden | Du har överskridit kvoten eller antalet begäranden som tillåts för din prenumeration. |
| 502 | Felaktig gateway | Problem med nätverket eller servern. Kan också vara ogiltiga sidhuvuden. |

Om HTTP-status är `200 OK`, brödtexten i svaret innehåller en ljudfil i det begärda formatet. Den här filen kan spelas upp när den har överförts, sparas i en buffert eller sparas som en fil.

## <a name="next-steps"></a>Nästa steg

- [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Anpassa akustiska modeller](how-to-customize-acoustic-models.md)
- [Anpassa språkmodeller](how-to-customize-language-model.md)
