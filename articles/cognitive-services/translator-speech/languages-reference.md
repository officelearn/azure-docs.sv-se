---
title: Translator Speech API språk metod
titleSuffix: Azure Cognitive Services
description: Använd metoden Translator Speech API språk.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: conceptual
ms.date: 05/18/18
ms.author: v-jansko
ms.openlocfilehash: 12f989137c3aea57bdcde0d50315ad157898cd28
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55862759"
---
# <a name="translator-speech-api-languages"></a>API för Talöversättning: Languages

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Translator för Talöversättning utökar kontinuerligt listan över språk som stöds av dess tjänster. Använd detta API för att identifiera uppsättningen språk som är för närvarande tillgängliga för användning med Translator Speech-tjänsten.

Kodexempel som demonstrerar användningen av API för att hämta tillgängliga språk är tillgängliga från den [Microsoft Translator GitHub-webbplatsen](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Implementeringsanteckningar

### <a name="get-languages"></a>Hämta /languages 

Det finns en bred uppsättning språk att transkribera tal, Översätt den transkriberade texten och producera syntetiskt tal översättningens.

En klient använder de `scope` frågeparameter för att definiera vilka uppsättningar med språk som den är intresserad av.

* **Tal till text:** Använd Frågeparametern `scope=speech` att hämta de språk som är tillgängliga att transkribera tal till text.
* **Textöversättning:** Använd Frågeparametern `scope=text` att hämta de språk som är tillgängliga att översätta transkriberade texten.
* **Text till tal:**  Använd Frågeparametern `scope=tts` att hämta uppsättning språk och röster som är tillgängliga för att syntetisera översatt text till tal.

En klient kan hämta flera uppsättningar samtidigt genom att ange en kommaavgränsad lista med alternativ. Till exempel `scope=speech,text,tts`.

Ett lyckat svar är ett JSON-objekt med en egenskap för var och en begärt set.

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

Eftersom en klient kan använda den `scope` frågeparameter för att välja vilka språk som stöds ska returneras, ett faktiska svar får bara innehålla en delmängd av alla egenskaper som anges ovan.

Det värde som tillhandahölls med varje egenskap är som följer.

### <a name="speech-to-text-speech"></a>Tal till text (tal)

Värdet som är associerade med tal till text-egenskapen `speech`, är en dictionary med (nyckel, värde) par. Varje nyckel identifierar ett språk som stöds för tal till text. Nyckeln är identifieraren som klienten skickar till API: et. Värdet kopplat till nyckeln är ett objekt med följande egenskaper:

* `name`: Visningsnamn för språket.
* `language`: Språktaggen för den associerade skrivna språk. Se ”Text-transation” nedan.
Ett exempel är:

```
{
    "speech": {
        "en-US": { name: "English", language: "en" }
    }
}
```

### <a name="text-translation-text"></a>Textöversättning (text)

Värdet som är associerade med den `text` egenskapen är också en ordlista där varje nyckel identifierar ett språk som stöds för textöversättning. Värdet kopplat till nyckeln beskrivs språk:

* `name`: Visningsnamn för språket.
* `dir`: Riktningen som är `rtl` för höger-till-vänster-språk eller `ltr` för vänster till höger språk.

Ett exempel är:

```
{
    "text": {
        "en": { name: "English", dir: "ltr" }
    }
}
```

### <a name="text-to-speech-tts"></a>Tal

Värdet som är associerade med egenskapen text till tal text till tal, är också en ordlista där varje nyckel identifierar en röst som stöds. Attribut för en röst-objekt är:

* `displayName`: Visningsnamn för röst.
* `gender`: Kön röst (manliga eller kvinnliga).
* `locale`: Språktaggen för röst med primära språket undertagg och region undertagg.
* `language`: Språktaggen för den associerade skrivna språk.
* `languageName`: Visningsnamn för språket.
* `regionName`: Visningsnamnet för regionen för det här språket.

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
Tjänsten returnerar alla namn på rubriken Accept-Language för alla språk som stöds i textöversättning språk.

### <a name="response-class-status-200"></a>Svaret klass (Status 200)
Objekt som beskriver uppsättningen språk som stöds.

ModelExample värde: 

Langagues {tal (object, valfritt), text (object, valfritt), text till tal (object, valfritt)}

### <a name="headers"></a>Rubriker

|Huvud|Beskrivning|Type|
:--|:--|:--|
X-RequestId|Värde som genereras av server för att identifiera begäran och används för felsökning.|sträng|

### <a name="parameters"></a>Parametrar

|Parameter|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|
|API-versionen    |Versionen av API: et som begärs av klienten. Tillåtna värden är: `1.0`.|DocumentDB|sträng|
|omfång  |Uppsättningar med språk som stöds eller röster för att återgå till klienten. Den här parametern har angetts som en kommaavgränsad lista över nyckelord. Följande nyckelord är tillgängliga:<ul><li>`speech`: Innehåller en uppsättning språk som stöds för att transkribera tal.</li><li>`tts`: Innehåller antal röster som stöds för text till tal-konvertering.</li><li>`text`: Innehåller en uppsättning språk som stöds för att översätta text.</li></ul>Om ett värde inte anges värdet för `scope` som standard `text`.|DocumentDB|sträng|
|X-ClientTraceId    |En klientgenererade GUID som används för att spåra en begäran. För att underlätta felsökning av problem bör klienter ange ett nytt värde med varje begäran och logga den.|sidhuvud|sträng|
|Accept-Language    |Vissa av fälten i svaret är namnen på språk eller regioner. Använd den här parametern för att ange språket där namnen som returneras. Språket har angetts genom att tillhandahålla en korrekt strukturerad BCP-47 som språktagg. Välj en tagg i listan över språkidentifierare som returneras med den `text` omfång. Språk som stöds inte tillhandahålls namnen på engelska.<br/>Till exempel använda värdet `fr` att begära namnen på franska eller använda värdet `zh-Hant` i begäran namn i traditionell kinesiska.|sidhuvud|sträng|
    
### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400|Felaktig begäran. Kontrollera indataparametrarna för att säkerställa att de är giltiga. Objektet response innehåller en detaljerad beskrivning av felet.|
|429|För många förfrågningar.|
|500|Ett fel uppstod. Om felet kvarstår, rapportera det med klient-ID för spårning (X-ClientTraceId) eller begäranidentifierare (X-RequestId).|
|503|Servern är inte tillgänglig för tillfället. Försök med förfrågan. Om felet kvarstår, rapportera det med klient-ID för spårning (X-ClientTraceId) eller begäranidentifierare (X-RequestId).|
