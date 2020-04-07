---
title: Språkstöd - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS har en mängd olika funktioner i tjänsten. Alla funktioner är inte på samma språkparitet. Kontrollera att de funktioner du är intresserad av stöds i den språkkultur du riktar dig till. En LUIS-app är kulturspecifik och kan inte ändras när den har ställts in.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 82efa70b30e829cfedd0b1fa7a21fd06949aa6d5
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80744140"
---
# <a name="language-and-region-support-for-luis"></a>Språk- och regionstöd för LUIS

LUIS har en mängd olika funktioner i tjänsten. Alla funktioner är inte på samma språkparitet. Kontrollera att de funktioner du är intresserad av stöds i den språkkultur du riktar dig till. En LUIS-app är kulturspecifik och kan inte ändras när den har ställts in.

## <a name="multi-language-luis-apps"></a>LUIS-appar på flera språk

Om du behöver ett LUIS-klientprogram på flera språk, till exempel en chatbot, har du några alternativ. Om LUIS stöder alla språk utvecklar du en LUIS-app för varje språk. Varje LUIS-app har ett unikt app-ID och slutpunktslogg. Om du behöver ge språkförståelse för ett språk som LUIS inte stöder kan du använda [Microsoft Translator API](../Translator/translator-info-overview.md) för att översätta uttrycket till ett språk som stöds, skicka uttryck till LUIS-slutpunkten och få de resulterande poängen.

## <a name="languages-supported"></a>Språk som stöds

LUIS förstår yttranden på följande språk:

| Språk |Nationell inställning  |  Fördefinierad domän | Fördefinierad enhet | Rekommendationer för fraslista | **[Textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(Sentiment och<br>Nyckelord)|
|--|--|:--:|:--:|:--:|:--:|
| Amerikansk engelska |`en-US` | ✔ | ✔  |✔|✔|
| Arabiska (förhandsvisning - modern standard arabiska) |`ar-AR`|-|-|-|-|
| *[Kinesiska](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Nederländska |`nl-NL` |✔|-|-|✔|
| Franska (Frankrike) |`fr-FR` |✔| ✔ |✔ |✔|
| Franska (Kanada) |`fr-CA` |-|-|-|✔|
| Tyska |`de-DE` |✔| ✔ |✔ |✔|
| Gujarati | `gu-IN`|-|-|-|-|
| Hindi | `hi-IN`|-|✔|-|-|
| Italienska |`it-IT` |✔| ✔ |✔|✔|
| *[Japanska](#japanese-support-notes) |`ja-JP` |✔| ✔ |✔|Endast nyckelfras|
| Koreansk |`ko-KR` |✔|-|-|Endast nyckelfras|
| Marathi | `mr-IN`|-|-|-|-|
| Portugisiska (Brasilien) |`pt-BR` |✔| ✔ |✔ |inte alla underkulturer|
| Spanska (Spanien) |`es-ES` |✔| ✔ |✔|✔|
| Spanska (Mexiko)|`es-MX` |-|-|✔|✔|
| Tamilska | `ta-IN`|-|-|-|-|
| Telugu | `te-IN`|-|-|-|-|
| Turkiska | `tr-TR` |✔|✔|-|Endast känsla|




Språkstöd varierar för [fördefinierade entiteter](luis-reference-prebuilt-entities.md) och [fördefinierade domäner](luis-reference-prebuilt-domains.md).

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

### <a name="japanese-support-notes"></a>*Japanska supportanteckningar

 - Eftersom LUIS inte ger syntaktisk analys och inte kommer att förstå skillnaden mellan Keigo och informella japanska, måste du införliva de olika nivåerna av formalitet som utbildning exempel för dina program.
     - Det är inte samma sak som.
     - Det är inte samma sak som.

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]

### <a name="speech-api-supported-languages"></a>Språk som stöds av tal-API
Se [Språk som stöds](../speech-service/speech-to-text.md) av talstöd för språk i taldiktationsläge.

### <a name="bing-spell-check-supported-languages"></a>Språk som stöds av Bing-stavningskontroll
Se Språk för stavningskontroll [som stöds](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) av Bing för en lista över språk och status som stöds.

## <a name="rare-or-foreign-words-in-an-application"></a>Sällsynta eller främmande ord i en ansökan
I `en-us` kulturen lär sig LUIS att skilja de flesta engelska ord, inklusive slang. I `zh-cn` kulturen lär sig LUIS att skilja de flesta kinesiska tecken. Om du använder ett `en-us` sällsynt `zh-cn`ord i eller tecken i , och du ser att LUIS inte verkar skilja det ordet eller tecknet, kan du lägga till det ordet eller tecknet i en [fraslistefunktion](luis-how-to-add-features.md). Ord utanför programmets kultur - det vill säga utländska ord – bör till exempel läggas till i en fraslistefunktion.

<!--This phrase list should be marked non-interchangeable, to indicate that the set of rare words forms a class that LUIS should learn to recognize, but they are not synonyms or interchangeable with each other.-->

### <a name="hybrid-languages"></a>Hybridspråk
Hybridspråk kombinerar ord från två kulturer som engelska och kinesiska. Dessa språk stöds inte i LUIS eftersom en app baseras på en enda kultur.

## <a name="tokenization"></a>Tokenisering
För att utföra maskininlärning bryter LUIS ett uttryck i [token baserat](luis-glossary.md#token) på kultur.

|Språk|  varje utrymme eller specialtecken | teckennivå|sammansatta ord
|--|:--:|:--:|:--:|
|Arabiska|✔|||
|Kinesiska||✔||
|Nederländska|✔||✔|
|Engelska (en-us)|✔ |||
|Franska (fr-FR)|✔|||
|Franska (fr-CA)|✔|||
|Tyska|✔||✔|
|Gujarati|✔|||
|Hindi|✔|||
|Italienska|✔|||
|Japanska|||✔
|Koreansk||✔||
|Marathi|✔|||
|Portugisiska (Brasilien)|✔|||
|Spanska (es-ES)|✔|||
|Spanska (es-MX)|✔|||
|Tamilska|✔|||
|Telugu|✔|||
|Turkiska|✔|||


### <a name="custom-tokenizer-versions"></a>Anpassade tokenizerversioner

Följande kulturer har anpassade tokenizerversioner:

|Kultur|Version|Syfte|
|--|--|--|
|Tyska<br>`de-de`|1.0.0|Tokenizes ord genom att dela upp dem med hjälp av en maskininlärningsbaserad tokenizer som försöker dela upp sammansatta ord i sina enskilda komponenter.<br>Om en användare `Ich fahre einen krankenwagen` anger som ett uttryck, `Ich fahre einen kranken wagen`vrids den till . Tillåta märkning `kranken` av `wagen` och oberoende som olika enheter.|
|Tyska<br>`de-de`|1.0.2|Tokenizes ord genom att dela dem på mellanslag.<br> Om en användare `Ich fahre einen krankenwagen` anger som ett uttryck förblir det en enda token. Således `krankenwagen` är markerad som en enda enhet. |
|Nederländska<br>`de-de`|1.0.0|Tokenizes ord genom att dela upp dem med hjälp av en maskininlärningsbaserad tokenizer som försöker dela upp sammansatta ord i sina enskilda komponenter.<br>Om en användare `Ik ga naar de kleuterschool` anger som ett uttryck, `Ik ga naar de kleuter school`vrids den till . Tillåta märkning `kleuter` av `school` och oberoende som olika enheter.|
|Nederländska<br>`de-de`|1.0.1|Tokenizes ord genom att dela dem på mellanslag.<br> Om en användare `Ik ga naar de kleuterschool` anger som ett uttryck förblir det en enda token. Således `kleuterschool` är markerad som en enda enhet. |


### <a name="migrating-between-tokenizer-versions"></a>Migrera mellan tokenizerversioner
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

Tokenisering sker på appnivå. Det finns inget stöd för tokenisering på versionsnivå.

[Importera filen som en ny app](luis-how-to-start-new-app.md)i stället för en version. Den här åtgärden innebär att den nya appen har ett annat app-ID men använder tokenizerversionen som anges i filen.
