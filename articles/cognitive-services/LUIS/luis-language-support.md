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
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 4b6d954d06f09bef5240bddc4860ddbc83513d69
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361182"
---
# <a name="language-and-region-support-for-luis"></a>Stöd för språk och din region för LUIS

LUIS har olika funktioner i tjänsten. Alla funktioner är inte på samma språkparitet. Kontrollera att de funktioner som du är intresserad av stöds i kulturen för språk som du arbetar med. En LUIS-app är kultur-specifika och kan inte ändras när den är inställd.

## <a name="multi-language-luis-apps"></a>Flera language Understanding Intelligent Service-appar

Om du behöver ett klientprogram för flera språk LUIS som en chattrobot finns ett par alternativ. Om LUIS har stöd för alla språk, kan du utveckla en LUIS-app för varje språk. Varje LUIS-app har ett unikt app-ID och slutpunkten log. Om du behöver ange språk förståelse för ett språk LUIS inte stöder kan du använda [Microsoft Translator API](../Translator/translator-info-overview.md) för att översätta uttryck till ett språk som stöds, skicka uttryck till Luis-slutpunkten och ta emot de resulterande poängen.

## <a name="languages-supported"></a>Språk som stöds

LUIS förstår yttranden på följande språk:

| Språk |Nationell inställning  |  Fördefinierade domän | Fördefinierade entitet | Rekommendationer för fras lista | **[text analys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(Känsla och<br>Nyckelord)|
|--|--|:--:|:--:|:--:|:--:|
| Amerikansk engelska |`en-US` | ✔ | ✔  |✔|✔|
| Arabiska (förhands granskning-modern standard arabisk) |`ar-AR`|-|-|-|-|
| *[kinesiska](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Nederländska |`nl-NL` |✔|  -   |-|✔|
| Franska (Frankrike) |`fr-FR` |✔| ✔ |✔ |✔|
| Franska (Kanada) |`fr-CA` |-|   -   |-|✔|
| Tyska |`de-DE` |✔| ✔ |✔ |✔|
| Hindi | `hi-IN`|-|-|-|-|
| Italienska |`it-IT` |✔| ✔ |✔|✔|
| *[japanska](#japanese-support-notes) |`ja-JP` |✔| ✔ |✔|Endast diskussionsämne|
| Koreanska |`ko-KR` |✔|   -   |-|Endast diskussionsämne|
| Portugisiska (Brasilien) |`pt-BR` |✔| ✔ |✔ |inte alla underordnade kulturer|
| Spanska (Spanien) |`es-ES` |✔| ✔ |✔|✔|
| Spanska (Mexiko)|`es-MX` |-|  -   |✔|✔|
| Turkiska | `tr-TR` |✔|-|-|Endast sentiment|

Språk stöd varierar för [färdiga entiteter](luis-reference-prebuilt-entities.md) och [fördefinierade domäner](luis-reference-prebuilt-domains.md).

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

### <a name="japanese-support-notes"></a>\* Japanska stöder anteckningar

 - Eftersom LUIS stöder inte syntaktiska analys och kommer inte att förstå skillnaden mellan Keigo och informell japanska, måste du lägga till de olika nivåerna av förfarande utbildning exempel för dina program.
     - でございます är inte samma som です.
     - です är inte samma som だ.

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]

### <a name="speech-api-supported-languages"></a>Talspråk för API som stöds
Se språk som [stöds](../speech-service/speech-to-text.md) av tal i tal för tallägen.

### <a name="bing-spell-check-supported-languages"></a>Stavningskontroll i Bing stöds språk
Se Stavningskontroll i Bing [språk som stöds](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) för en lista över språk och status som stöds.

## <a name="rare-or-foreign-words-in-an-application"></a>Sällsynta eller främmande ord i ett program
I `en-us` kulturen lär sig LUIS att skilja de flesta engelska ord, inklusive slang. I `zh-cn` kulturen lär sig LUIS att skilja på de flesta kinesiska tecknen. Om du använder ett sällsynt ord i `en-us` eller ett specialtecken i `zh-cn`och du ser att LUIS inte verkar särskilja ordet eller specialtecknet kan du lägga till ordet eller symbolen i en [fras List funktion](luis-how-to-add-features.md). Till exempel ska utanför programmet – det vill säga främmande ord--kultur läggas till en fras-list-funktion.

<!--This phrase list should be marked non-interchangeable, to indicate that the set of rare words forms a class that LUIS should learn to recognize, but they are not synonyms or interchangeable with each other.-->

### <a name="hybrid-languages"></a>Hybrid-språk
Hybrid språk kombinera ord från två kulturer, till exempel engelska och kinesiska. De här språken stöds inte i LUIS eftersom en app är baserad på en enda kultur.

## <a name="tokenization"></a>Tokenisering
För att utföra maskin inlärning bryter LUIS en uttryck till [token](luis-glossary.md#token) baserat på kulturen.

|Språk|  varje blanksteg eller specialtecken | tecknet nivå|sammansatta ord|[en token enhet returnerades](luis-concept-data-extraction.md#tokenized-entity-returned)
|--|:--:|:--:|:--:|:--:|
|Arabiska|||||
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

|Culture (Kultur)|Version|Syfte|
|--|--|--|
|Tyska<br>`de-de`|1.0.0|Tokenizes ord genom att dela dem med hjälp av en Machine Learning-baserad tokenizer som försöker dela upp sammansatta ord i sina enskilda komponenter.<br>Om en användare går in `Ich fahre einen krankenwagen` som en uttryck, aktive ras den `Ich fahre einen kranken wagen`. Tillåta markering av `kranken` och `wagen` oberoende av olika entiteter.|
|Tyska<br>`de-de`|1.0.2|Tokenizes ord genom att dela dem på blank steg.<br> om en användare går in `Ich fahre einen krankenwagen` som en uttryck, förblir den en enda token. Därför markeras `krankenwagen` som en enskild enhet. |

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

[Importera filen som en ny app](luis-how-to-start-new-app.md)i stället för en version. Den här åtgärden innebär att den nya appen har ett annat app-ID men använder den tokenizer-version som anges i filen.
