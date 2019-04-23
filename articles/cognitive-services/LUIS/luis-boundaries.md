---
title: Begränsningar
titleSuffix: Language Understanding - Azure Cognitive Services
description: Den här artikeln innehåller kända begränsningar för Azure Cognitive Services Språkförståelse (LUIS). LUIS har flera gräns områden. Modellen gräns styr avsikter, entiteter och funktioner i LUIS. Kvotgränser baserat på nyckeltyp. Tangentkombination styr LUIS-webbplatsen.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/18/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 58f6d6cf8bf16f7c35bab35a69cfcdf8759f66ae
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60006836"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>Gränser för LUIS-modell och nycklar
LUIS har flera gräns områden. Först är det [modellen gräns](#model-boundaries), som styr avsikter, entiteter och funktioner i LUIS. Det andra området är [kvotgränser](#key-limits) baserat på nyckeltyp. Ett tredje område av gränser är den [tangentbord kombination](#keyboard-controls) för att styra LUIS-webbplatsen. En fjärde området är den [världen regionsmappning](luis-reference-regions.md) mellan LUIS redigering av webbplatsen och LUIS [endpoint](luis-glossary.md#endpoint) API: er. 


## <a name="model-boundaries"></a>Modellen gränser

Om din app överskrider LUIS-modellen begränsningar och gränser, bör du använda en [LUIS dispatch](luis-concept-enterprise.md#dispatch-tool-and-model) app eller med hjälp av en [LUIS behållare](luis-container-howto.md). 

|Område|Gräns|
|--|:--|
| [Appnamn][luis-get-started-create-app] | * Standardvärdet max |
| [Batch-testning][batch-testing]| 10 datauppsättningar, 1000 yttranden per datauppsättning|
| Explicit lista | 50 per program|
| [Avsikter][intents]|500 per program: 499 anpassade avsikter och de nödvändiga _ingen_ avsikt.<br>[Dispatch-baserade](https://aka.ms/dispatch-tool) programmet har motsvarande 500 dispatch-källor.|
| [Lista över entiteter](./luis-concept-entity-types.md) | Överordnad: 50, underordnade: 20 000 objekt. Kanoniskt namn är * maximalt antal tecken som standard. Synonymen värden har ingen begränsning av lösenordslängd. |
| [Datorn lärt dig entiteter](./luis-concept-entity-types.md):<br> Sammansatta,<br>  Hierarkisk<br> Enkel|En gräns på 100 överordnade entiteter (inkluderar inte hierarkiska underordnade) eller 330 entiteter (inklusive hierarkisk underordnade), beroende på vilket som begränsar träffar för användaren först.<br><br>Ett exempel på hierarki är 30 hierarkier varje med 10 underordnade.  Underordnade förbrukar 300 totala och element för hierarkin kommer att använda de återstående 30. |
| [Mönster](luis-concept-patterns.md)|500 mönster per program.<br>Maximal längd på mönstret är 400 tecken.<br>3 Pattern.any entiteter per mönster<br>Högst 2 kapslade valfritt texter i mönstret|
| [Pattern.any](./luis-concept-entity-types.md)|100 per program, 3 pattern.any entiteter per mönster |
| [Fras lista][phrase-list]|10 fras listor, 5 000 poster per|
| [Fördefinierade entiteter](./luis-prebuilt-entities.md) | ingen gräns|
| [Reguljärt uttryck entiteter](./luis-concept-entity-types.md)|20 entiteter<br>max 500 tecken. per entitet mönster för reguljärt uttryck|
| [roles](luis-concept-roles.md)|300 roller per program. 10 roller per enhet|
| [Uttryck][utterances] | 500 tecken|
| [Yttranden][utterances] | 15 000 per program – det finns ingen gräns för antalet yttranden per avsikt|
| [Versioner](luis-concept-version.md)| ingen gräns |
| [Versionsnamn][luis-how-to-manage-versions] | begränsad till alfanumeriska tecken och period 10 tecken (.) |

* Standard tecknet max är 50 tecken. 

<a name="intent-and-entity-naming"></a>

## <a name="object-naming"></a>Objekt-namngivning

Använd inte följande tecken i följande namn.

|Object|Utesluta tecken|
|--|--|
|Avsikten-, entitets-och roll|`:`<br>`$`|
|Versionsnamn|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="key-usage"></a>Nyckelanvändning

Förstå språket har separata nycklar, en typ för redigering och en typ för att fråga förutsägelse-slutpunkten. Mer information om skillnaderna mellan nyckeltyper finns [redigering och fråga förutsägelse endpoint nycklar i LUIS](luis-concept-keys.md).

## <a name="key-limits"></a>Viktiga begränsningar

Redigering nyckel har olika begränsningar för redigering och slutpunkten. Slutpunktsnyckeln LUIS-tjänsten är endast giltig för slutpunkten frågor.


|Nyckel|Redigering|Slutpunkt|Syfte|
|--|--|--|--|
|Language Understanding redigering/Starter|1 miljon/månad, 5/sekund|1 tusen/månad, 5/sekund|Redigera LUIS-appen|
|Språkförståelse [prenumeration] [ pricing] - F0 - kostnadsfri nivå |ogiltig|10 tusen/månad, 5/sekund|Fråga din LUIS-slutpunkt|
|Språkförståelse [prenumeration] [ pricing] - S0 - Basic-nivån|ogiltig|50 per sekund|Fråga din LUIS-slutpunkt|
|Cognitive Service [prenumeration] [ pricing] - S0 - Standard-nivån|ogiltig|50 per sekund|Fråga din LUIS-slutpunkt|
|[Sentiment analysis-integrering](luis-how-to-publish-app.md#enable-sentiment-analysis)|Ogiltig|utan kostnad|Att lägga till sentiment information, inklusive extrahering av diskussionsämne data |
|Tal-integrering|Ogiltig|5.50 USD/1 tusen endpoint begäranden|Konvertera talat uttryck till text-uttryck och returnerar LUIS resultat|

## <a name="keyboard-controls"></a>Keyboard kontroller

|Tangentbordsinmatning | Beskrivning | 
|--|--|
|Ctrl + E|växlar mellan token och entiteter på yttranden lista|

## <a name="website-sign-in-time-period"></a>Webbplatsen logga under en tidsperiod

Din inloggning åtkomst är för **60 minuter**. Efter denna tidsperiod får du det här felet. Du måste logga in igen.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
