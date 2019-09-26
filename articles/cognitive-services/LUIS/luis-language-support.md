---
title: Språk stöd – LUIS
titleSuffix: Azure Cognitive Services
description: LUIS har olika funktioner i tjänsten. Alla funktioner är inte på samma språkparitet. Kontrollera att de funktioner som du är intresserad av stöds i kulturen för språk som du arbetar med. En LUIS-app är kultur-specifika och kan inte ändras när den är inställd.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: 94f019205959d63a05ed3d90ede59fece3c05901
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316357"
---
# <a name="language-and-region-support-for-luis"></a>Stöd för språk och din region för LUIS

LUIS har olika funktioner i tjänsten. Alla funktioner är inte på samma språkparitet. Kontrollera att de funktioner som du är intresserad av stöds i kulturen för språk som du arbetar med. En LUIS-app är kultur-specifika och kan inte ändras när den är inställd.

## <a name="multi-language-luis-apps"></a>Flera language Understanding Intelligent Service-appar

Om du behöver ett klientprogram för flera språk LUIS som en chattrobot finns ett par alternativ. Om LUIS har stöd för alla språk, kan du utveckla en LUIS-app för varje språk. Varje LUIS-app har ett unikt app-ID och slutpunkten log. Om du måste ange språkförståelse för ett språk som LUIS inte stöder, du kan använda [Microsoft Translator API](../Translator/translator-info-overview.md) skicka uttryck till LUIS-slutpunkten för att översätta uttryck till ett språk som stöds, och ta emot den resulterande poäng.

## <a name="languages-supported"></a>Språk som stöds

LUIS förstår yttranden på följande språk:

| Språk |Nationell inställning  |  Fördefinierade domän | Fördefinierade entitet | Rekommendationer för fras lista | **[Textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(Känsla och<br>Nyckelord)|
|--|--|:--:|:--:|:--:|:--:|
| Amerikansk engelska |`en-US` | ✔ | ✔  |✔|✔|
| *[Kinesiska](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Nederländska |`nl-NL` |✔|  -   |-|✔|
| Franska (Frankrike) |`fr-FR` |✔| ✔ |✔ |✔|
| Franska (Kanada) |`fr-CA` |-|   -   |-|✔|
| Tyska |`de-DE` |✔| ✔ |✔ |✔|
| Hindi | `hi-IN`|-|-|-|-|
| Italienska |`it-IT` |✔| ✔ |✔|✔|
| *[Japanska](#japanese-support-notes) |`ja-JP` |✔| ✔ |✔|Endast diskussionsämne|
| Koreanska |`ko-KR` |✔|   -   |-|Endast diskussionsämne|
| Portugisiska (Brasilien) |`pt-BR` |✔| ✔ |✔ |inte alla underordnade kulturer|
| Spanska (Spanien) |`es-ES` |✔| ✔ |✔|✔|
| Spanska (Mexiko)|`es-MX` |-|  -   |✔|✔|
| Turkiska | `tr-TR` |✔|-|-|Endast sentiment|


Språkstöd varierar för [förskapade entiteter](luis-reference-prebuilt-entities.md) och [fördefinierade domäner](luis-reference-prebuilt-domains.md).

### <a name="chinese-support-notes"></a>\* Kinesiska stöder anteckningar

 - I den `zh-cn` kulturen, LUIS förväntar sig förenklad kinesiska teckenuppsättningen i stället för den traditionella teckenuppsättningen.
 - Namn på avsikter, entiteter, funktioner och reguljära uttryck kan vara i kinesiska eller latinska tecken.
 - Se den [fördefinierade domän referensen](luis-reference-prebuilt-domains.md) för information om vilka färdiga domäner som stöds i `zh-cn` kulturen.
<!--- When writing regular expressions in Chinese, do not insert whitespace between Chinese characters.-->

### <a name="japanese-support-notes"></a>\* Japanska stöder anteckningar

 - Eftersom LUIS stöder inte syntaktiska analys och kommer inte att förstå skillnaden mellan Keigo och informell japanska, måste du lägga till de olika nivåerna av förfarande utbildning exempel för dina program.
     - でございます är inte samma som です.
     - です är inte samma som だ.

### <a name="text-analytics-support-notes"></a>** Text analytics har stöd för anteckningar
Text analytics innehåller keyPhrase fördefinierade entitets- och attityd-analys. Endast portugisiska stöds för subkulturer: `pt-PT` och `pt-BR`. Andra kulturer som stöds på primära kultur-nivå. Mer information om textanalys [språk som stöds](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages).

### <a name="speech-api-supported-languages"></a>Talspråk för API som stöds
Se tal [språk som stöds](https://docs.microsoft.com/azure/cognitive-services/Speech/api-reference-rest/supportedlanguages##interactive-and-dictation-mode) för talspråk diktering läge.

### <a name="bing-spell-check-supported-languages"></a>Stavningskontroll i Bing stöds språk
Se stavningskontroll i Bing [språk som stöds](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) en lista över språk som stöds och status.

## <a name="rare-or-foreign-words-in-an-application"></a>Sällsynta eller främmande ord i ett program
I den `en-us` kulturen, LUIS lär sig att skilja mest svenska ord, inklusive slang. I den `zh-cn` kulturen, LUIS lär sig att skilja mellan de flesta kinesiska tecken. Om du använder ett ovanligt ord i `en-us` eller tecknet i `zh-cn`, och du ser att LUIS verkar det går inte att skilja den ord eller tecken, du kan lägga till den ord eller tecken till en [funktionen frasen lista](luis-how-to-add-features.md). Till exempel ska utanför programmet – det vill säga främmande ord--kultur läggas till en fras-list-funktion. Den här frasen listan bör markeras ej utbytbara, att indikera att uppsättning sällsynta ord utgör en klass som LUIS bör lär sig att känna igen, men de är inte synonymer eller utbytbara med varandra.

### <a name="hybrid-languages"></a>Hybrid-språk
Hybrid språk kombinera ord från två kulturer, till exempel engelska och kinesiska. De här språken stöds inte i LUIS eftersom en app är baserad på en enda kultur.

## <a name="tokenization"></a>Tokenisering
Om du vill utföra maskininlärning LUIS delar ett uttryck i [token](luis-glossary.md#token) baserat på kultur.

|Språk|  varje blanksteg eller specialtecken | tecknet nivå|sammansatta ord|[principfilerna entitet returnerades](luis-concept-data-extraction.md#tokenized-entity-returned)
|--|:--:|:--:|:--:|:--:|
|Kinesiska||✔||✔|
|Nederländska|||✔|✔|
|Engelska (en-us)|✔ ||||
|Franska (fr-FR)|✔||||
|Franska (fr-CA)|✔||||
|Tyska|||✔|✔|
| Hindi |✔|-|-|-|-|
|Italienska|✔||||
|Japanska||||✔|
|Koreanska||✔||✔|
|Portugisiska (Brasilien)|✔||||
|Spanska (es-ES)|✔||||
|Spanska (es-MX)|✔||||

### <a name="custom-tokenizer-versions"></a>Anpassade tokenizer-versioner

Följande kulturer har anpassade tokenizer-versioner:

|Kultur|Version|Syfte|
|--|--|--|
|Tyska<br>`de-de`|1.0.0|Tokenizes ord genom att dela dem med hjälp av en Machine Learning-baserad tokenizer som försöker dela upp sammansatta ord i sina enskilda komponenter.<br>Om en användare anger `Ich fahre einen krankenwagen` som en uttryck, `Ich fahre einen kranken wagen`aktive ras den. Tillåta markering av `kranken` och `wagen` oberoende som olika entiteter.|
|Tyska<br>`de-de`|1.0.2|Tokenizes ord genom att dela dem på blank steg.<br> om en användare anges `Ich fahre einen krankenwagen` som en uttryck, förblir den en enda token. Markeras därför `krankenwagen` som en enskild entitet. |

### <a name="migrating-between-tokenizer-versions"></a>Migrera mellan tokenizer-versioner
<!--
Your first choice is to change the tokenizer version in the app file, then import the version. This action changes how the utterances are tokenized but allows you to keep the same app ID. 

Tokenizer JSON for 1.0.0. Notice the property value for  `tokenizerVersion`. 

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.0",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.0",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 23,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```

Tokenizer JSON for version 1.0.1. Notice the property value for  `tokenizerVersion`. 

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.1",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.1",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 16,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```
-->

Tokenisering sker på App-nivå. Det finns inget stöd för tokenisering på versions nivå. 

[Importera filen som en ny app](luis-how-to-start-new-app.md#import-an-app-from-file)i stället för en version. Den här åtgärden innebär att den nya appen har ett annat app-ID men använder den tokenizer-version som anges i filen. 
