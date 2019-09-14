---
title: Metod för Translator Speech API språk
titleSuffix: Azure Cognitive Services
description: Använd metoden Translator Speech API språk.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 46ac3928238382f56db5a799226bd3d9243b7ca2
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966401"
---
# <a name="translator-speech-api-languages"></a>Translator Speech API: Languages

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Translator Speech utvidgar kontinuerligt listan över språk som stöds av tjänsterna. Använd det här API: et för att identifiera de språk som för närvarande är tillgängliga för användning med tjänsten Translator Speech.

Kod exempel som demonstrerar användningen av API: et för att hämta tillgängliga språk är tillgängliga på [webbplatsen Microsoft Translator GitHub](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Implementerings anteckningar

### <a name="get-languages"></a>Hämta/languages

Ett stort antal språk är tillgängligt för att skriva tal, för att översätta den översatta texten och för att skapa syntetiskt tal för översättningen.

En klient använder `scope` Frågeparametern för att definiera vilka uppsättningar språk som den är intresse rad av.

* **Tal till text:** Använd Frågeparametern `scope=speech` för att hämta den uppsättning språk som är tillgängliga för att skriva om tal till text.
* **Text översättning:** Använd Frågeparametern `scope=text` för att hämta den uppsättning språk som är tillgängliga för översättning av text.
* **Text till tal:**  Använd Frågeparametern `scope=tts` för att hämta den uppsättning språk och röster som är tillgängliga för att syntetisera översatt text tillbaka till tal.

En klient kan hämta flera mängder samtidigt genom att ange en kommaavgränsad lista med alternativ. Till exempel `scope=speech,text,tts`.

Ett lyckat svar är ett JSON-objekt med en egenskap för varje begärd uppsättning.

```
{
    "speech": {
        //... Set of languages supported for speech-to-text
    },
    "text": {
        //... Set of languages supported for text translation
    },
    "tts": {
        //... Set of languages supported for text-to-speech
    }
}
```

Eftersom en klient kan använda `scope` Frågeparametern för att välja vilka uppsättningar av språk som stöds ska returneras kan ett faktiskt svar bara innehålla en delmängd av alla egenskaper som visas ovan.

Värdet som anges med varje egenskap är följande.

### <a name="speech-to-text-speech"></a>Tal till text (tal)

Värdet som är kopplat till egenskapen tal-till-text, `speech`är en ord lista med (nyckel, värde) par. Varje nyckel identifierar ett språk som stöds för tal till text. Nyckeln är den identifierare som klienten skickar till API: et. Värdet som är kopplat till nyckeln är ett objekt med följande egenskaper:

* `name`: Språkets visnings namn.
* `language`: Språk tag gen för det kopplade, skrivna språket. Se "text transaktion" nedan.
Ett exempel är:

```
{
    "speech": {
        "en-US": { name: "English", language: "en" }
    }
}
```

### <a name="text-translation-text"></a>Text översättning (text)

Värdet som är associerat `text` med egenskapen är också en ord lista där varje nyckel identifierar ett språk som stöds för text översättning. Värdet som är kopplat till nyckeln beskriver språket:

* `name`: Språkets visnings namn.
* `dir`: Riktning som är `rtl` för höger-till-vänster-språk eller `ltr` för vänster-till-höger-språk.

Ett exempel är:

```
{
    "text": {
        "en": { name: "English", dir: "ltr" }
    }
}
```

### <a name="text-to-speech-tts"></a>Text till tal (TTS)

Värdet som är kopplat till text till tal-egenskapen, TTS, är också en ord lista där varje nyckel identifierar en röst som stöds. Attribut för ett röst objekt är:

* `displayName`: Visnings namn för rösten.
* `gender`: Röstens kön (hane eller hona).
* `locale`: Språk tag gen för rösten med primärt språk Subtag och region Subtag.
* `language`: Språk tag gen för det kopplade, skrivna språket.
* `languageName`: Språkets visnings namn.
* `regionName`: Visnings namn för regionen för det här språket.

Ett exempel är:

```
{
    "tts": {
        "en-US-Zira": {
            "displayName": "Zira",
            "gender": "female",
            "locale": "en-US",
            "languageName": "English",
            "regionName": "United States",
            "language": "en"
        }
}
```

### <a name="localization"></a>Lokalisering
Tjänsten returnerar alla namn på språket för rubriken accept-language, för alla språk som stöds i text översättning.

### <a name="response-class-status-200"></a>Svars klass (status 200)
Objekt som beskriver de språk som stöds.

ModelExample värde:

Langagues {Speech (objekt, valfritt), text (objekt, valfritt), TTS (objekt, valfritt)}

### <a name="headers"></a>Huvuden

|Huvud|Beskrivning|type|
:--|:--|:--|
X-RequestId|Värde som genereras av servern för att identifiera begäran och användas i fel söknings syfte.|sträng|

### <a name="parameters"></a>Parametrar

|Parameter|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|
|API-versionen    |Den version av API: t som klienten begär. Tillåtna värden är: `1.0`.|query|sträng|
|scope  |Uppsättningar av språk eller röster som stöds för att återgå till klienten. Den här parametern anges som en kommaavgränsad lista med nyckelord. Följande nyckelord är tillgängliga:<ul><li>`speech`: Tillhandahåller en uppsättning språk som stöds för att skriva om tal.</li><li>`tts`: Tillhandahåller en uppsättning röster som stöds för text-tal omvandling.</li><li>`text`: Tillhandahåller den uppsättning språk som stöds för översättning av text.</li></ul>Om inget värde anges används värdet `scope` som `text`standard.|query|sträng|
|X-ClientTraceId    |Ett GUID som skapats av klienten som används för att spåra en begäran. För att under lätta fel sökning av problem bör klienterna ange ett nytt värde för varje begäran och logga det.|sidhuvud|sträng|
|Acceptera-språk    |Några av fälten i svaret är namn på språk eller regioner. Använd den här parametern för att definiera språket som namnen returneras i. Språket anges genom att tillhandahålla en välformulerad BCP 47-språktagg. Välj en tagg i listan över språk identifierare som returneras med `text` omfånget. För språk som inte stöds finns namnen på det engelska språket.<br/>Använd exempelvis värdet `fr` för att begära namn på franska eller Använd värdet `zh-Hant` för att begära namn på traditionell kinesiska.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Reason|
|:--|:--|
|400|Felaktig begäran. Kontrol lera indataparametrarna för att säkerställa att de är giltiga. Objektet Response innehåller en mer detaljerad beskrivning av felet.|
|429|För många begär Anden.|
|500|Ett fel uppstod. Om felet kvarstår kan du rapportera det med klient spårnings-ID: n (X-ClientTraceId) eller begärande-ID (X-RequestId).|
|503|Servern är inte tillgänglig för tillfället. Försök att utföra begäran igen. Om felet kvarstår kan du rapportera det med klient spårnings-ID: n (X-ClientTraceId) eller begärande-ID (X-RequestId).|
