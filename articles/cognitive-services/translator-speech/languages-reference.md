---
title: Microsoft Translator tal API språk metoden | Microsoft Docs
titleSuffix: Cognitive Services
description: Använd metoden Microsoft översättare tal API språk.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 05/18/18
ms.author: v-jansko
ms.openlocfilehash: 5396e3be17345c3c36197a9b6cbace86e1f574c1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355701"
---
# <a name="speech-api-languages"></a>Dikterings-API: språk

Microsoft Translator utökar kontinuerligt en lista med språk som stöds av dess tjänster. Använd detta API för att upptäcka antal språk som är tillgängliga för användning med tjänsten tal Translation.

Kodexempel som visar användningen av API för att hämta tillgängliga språk som är tillgängliga från den [Microsoft översättare Github plats](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Implementeringsanteckningar

Hämta /languages 

Det finns ett stort antal språk som transkribera tal, att översätta transcribed text och för att skapa syntetiskt tal översättningens.

En klient använder de `scope` Frågeparametern för att definiera vilka uppsättningar av språk som den är intresserad av.

* **Tal till text:** Använd Frågeparametern `scope=speech` att hämta de språk som är tillgängliga för transkribera tal till text.
* **Textöversättning:** Använd Frågeparametern `scope=text` att hämta de språk som är tillgängliga att översätta sätt text.
* **Text till tal:** Använd Frågeparametern `scope=tts` att hämta en uppsättning språk och röster som är tillgängliga för att syntetisera översatt text till tal.

En klient kan hämta flera uppsättningar samtidigt genom att ange en kommaavgränsad lista med alternativ. Till exempel `scope=speech,text,tts`.

Ett lyckat svar är en JSON-objekt med en egenskap för varje begärt uppsättningen.

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

Eftersom en klient kan använda den `scope` frågeparameter att välja vilka språk som stöds som ska returneras, ett faktiska svar får endast innehålla en delmängd av alla egenskaper som visas ovan.

Värdet som angetts med varje egenskap är som följer.

### <a name="speech-to-text-speech"></a>Dikterings-till-text (tal)

Värdet som associeras med tal till text-egenskap `speech`, är en dictionary (nyckel, värde) par. Varje nyckel identifierar ett språk som stöds för tal till text. Nyckeln är identifieraren som klienten skickar till API: et. Värdet som associeras med nyckeln är ett objekt med följande egenskaper:

* `name`: Visningsnamn för språket.
* `language`: Språk taggen för den associerade skrivs språk. Se ”Text transation” nedan.
Ett exempel är:

```
{
    "speech": {
        "en-US": { name: "English", language: "en" }
    }
}
```

### <a name="text-translation-text"></a>Textöversättning (text)

Värdet som associeras med den `text` egenskapen är också en ordlista där varje nyckel identifierar ett språk som stöds för textöversättning. Värdet som associeras med nyckeln beskrivs språk:

* `name`: Visningsnamn för språket.
* `dir`: Riktningen som är `rtl` för höger-till-vänster-språk eller `ltr` för vänster till höger-språk.

Ett exempel är:

```
{
    "text": {
        "en": { name: "English", dir: "ltr" }
    }
}
```

### <a name="text-to-speech-tts"></a>Tal

Värdet som är associerade med egenskapen text till tal-text till tal, är också en ordlista där varje nyckel identifierar en stöds röst. Attribut för en röst-objekt är:

* `displayName`: Visningsnamn för röst.
* `gender`: Kön röst (han- eller honkontakt).
* `locale`: Språk taggen för röst med primära språket undertagg och region undertagg.
* `language`: Språk taggen för den associerade skrivs språk.
* `languageName`: Visningsnamn för språket.
* `regionName`: Visningsnamn för regionen för det här språket.

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
Tjänsten returnerar alla namn på rubriken acceptera-språk för alla språk som stöds i textöversättning språk.

### <a name="response-class-status-200"></a>Svaret klass (Status 200)
Objektet som beskriver uppsättningen språk som stöds.

ModelExample värde: 

Langagues {tal (objekt, valfritt), texten (objekt, valfritt), text till tal (objekt, valfritt)}

### <a name="headers"></a>Sidhuvuden

|Sidhuvud|Beskrivning|Typ|
:--|:--|:--|
X-RequestId|Värde som genereras av servern för att identifiera begäran och används för felsökning.|sträng|

### <a name="parameters"></a>Parametrar

|Parameter|Beskrivning|Parametertypen|Datatyp|
|:--|:--|:--|:--|
|API-version    |Versionen av API: et som begärs av klienten. Tillåtna värden är: `1.0`.|DocumentDB|sträng|
|omfång  |Anger de språk som stöds eller röster att återgå till klienten. Den här parametern har angetts som en kommaavgränsad lista över nyckelord. Följande nyckelord är tillgängliga:<ul><li>`speech`: Innehåller en uppsättning språk som stöds för att transkribera tal.</li><li>`tts`: Innehåller en uppsättning röster som stöds för konvertering av text till tal.</li><li>`text`: Innehåller en uppsättning språk som stöds för att översätta text.</li></ul>Om ett värde anges värdet för `scope` som standard `text`.|DocumentDB|sträng|
|X-ClientTraceId    |En genererad klient-GUID som används för att spåra en begäran. För att underlätta felsökning av problem ska klienter ange ett nytt värde för varje begäran och logga.|sidhuvud|sträng|
|Acceptera språk    |Vissa fält i svaret är namnen på språk eller regioner. Använd den här parametern om du vill definiera språket namn returneras. Språket som har angetts genom att ange en giltig BCP 47 språk tagg. Välj en tagg i listan över språkidentifierare som returneras med den `text` omfång. För språk som inte stöds tillhandahålls namnen på engelska.<br/>Till exempel använda värdet `fr` att begära namn på franska eller använda värdet `zh-Hant` i begäran namn i traditionell kinesiska.|sidhuvud|sträng|
    
### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400|Felaktig begäran. Kontrollera indataparametrarna för att säkerställa att de är giltiga. Objektet svaret innehåller en mer detaljerad beskrivning av felet.|
|429|För många begäranden.|
|500|Ett fel uppstod. Om felet kvarstår rapport med klient-ID för spårning (X-ClientTraceId) eller begäranidentifierare (X-RequestId).|
|503|Servern är inte tillgänglig för tillfället. Försök att utföra begäran. Om felet kvarstår rapport med klient-ID för spårning (X-ClientTraceId) eller begäranidentifierare (X-RequestId).|
