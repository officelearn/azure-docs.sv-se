---
title: Gränser och begränsningar för Språkförståelse (LUIS)
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller kända begränsningar för Azure Cognitive Services Språkförståelse (LUIS). LUIS har flera gräns områden. Modellen gräns styr avsikter, entiteter och funktioner i LUIS. Kvotgränser baserat på nyckeltyp. Tangentkombination styr LUIS-webbplatsen.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/31/2018
ms.author: diberry
ms.openlocfilehash: b867d2c6c2bd586e0844d4ad149ac4a73399ed7b
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2018
ms.locfileid: "44023242"
---
# <a name="luis-boundaries"></a>LUIS gränser
LUIS har flera gräns områden. Först är det [modellen gräns](#model-boundaries), som styr avsikter, entiteter och funktioner i LUIS. Det andra området är [kvotgränser](#key-limits) baserat på nyckeltyp. Ett tredje område av gränser är den [tangentbord kombination](#keyboard-controls) för att styra LUIS-webbplatsen. En fjärde området är den [världen regionsmappning](luis-reference-regions.md) mellan LUIS redigering av webbplatsen och LUIS [endpoint](luis-glossary.md#endpoint) API: er. 


## <a name="model-boundaries"></a>Modellen gränser

|Område|Gräns|
|--|:--|--|
| [Appnamn][luis-get-started-create-app] | * Standardvärdet max |
| [Batch-testning][batch-testing]| 10 datauppsättningar, 1000 yttranden per datauppsättning|
| **[Sammansatta](./luis-concept-entity-types.md)|100 med upp till 10 underordnade |
| Explicit lista | 50 per program|
| **[Hierarkisk](./luis-concept-entity-types.md) |100 med upp till 10 underordnade |
| [Avsikter][intents]|500 per program<br>[Dispatch-baserade](https://github.com/Microsoft/botbuilder-tools/tree/master/Dispatch) programmet har motsvarande 500 dispatch-källor|
| [Lista över entiteter](./luis-concept-entity-types.md) | Överordnad: 50, underordnad: 20 000 objekt. Kanoniskt namn är * maximalt antal tecken som standard. Synonymen värden har ingen begränsning av lösenordslängd. |
| [Mönster](luis-concept-patterns.md)|500 mönster per program.<br>Maximal längd på mönstret är 400 tecken.<br>3 Pattern.any entiteter per mönster<br>Högst 2 kapslade valfritt texter i mönstret|
| [Pattern.any](./luis-concept-entity-types.md)|100 per program, 3 pattern.any entiteter per mönster |
| [Fras lista][phrase-list]|10 fras listor, 5 000 poster per|
| [Fördefinierade entiteter](./luis-prebuilt-entities.md) | ingen gräns|
| [Reguljärt uttryck entiteter](./luis-concept-entity-types.md)|20 entiteter<br>max 500 tecken. per entitet mönster för reguljärt uttryck|
| [roles](luis-concept-roles.md)|300 roller per program. 10 roller per enhet|
| **[Enkel](./luis-concept-entity-types.md)| 100 entiteter|
| [Uttryck][utterances] | 500 tecken|
| [Yttranden][utterances] | 15 000 per program|
| [Versioner](luis-concept-version.md)| ingen gräns |
| [Versionsnamn][luis-how-to-manage-versions] | begränsad till alfanumeriska tecken och period 10 tecken (.) |

* Standard tecknet max är 50 tecken. 

** Det totala antalet enkel-, hierarki- och sammansatta entiteter får inte överskrida 100. Det totala antalet hierarkiska entiteter, sammansatta entiteter, enkla enheter och hierarkisk underordnade entiteter får inte överskrida 330. 

## <a name="intent-and-entity-naming"></a>Avsikt och namngivning av entiteten
Använd inte följande tecken i avsikt och entiteten namn:

|Tecken|Namn|
|--|--|
|`{`|Vänster klammer|
|`}`|Höger klammer|
|`[`|Vänster hakparentes|
|`]`|Högerparentes|
|`\`|Omvänt snedstreck|

## <a name="key-limits"></a>Viktiga begränsningar
Redigering nyckel har olika begränsningar för redigering och slutpunkten. Slutpunktsnyckeln LUIS-tjänsten är endast giltig för slutpunkten frågor.

|Nyckel|Redigering|Slutpunkt|Syfte|
|--|--|--|--|
|Redigera/Starter|1 miljon/månad, 5/sekund|1 tusen/månad, 5/sekund|Redigera LUIS-appen|
|[Prenumeration] [ pricing] - F0 - kostnadsfri nivå |ogiltig|10 tusen/månad, 5/sekund|Fråga din LUIS-slutpunkt|
|[Prenumeration] [ pricing] - S0 - Basic-nivån|ogiltig|50 per sekund|Fråga din LUIS-slutpunkt|
|[Sentiment analysis-integrering](luis-how-to-publish-app.md#enable-sentiment-analysis)|ogiltig|utan kostnad|Att lägga till sentiment information, inklusive extrahering av diskussionsämne data |
|Tal-integrering|ogiltig|5.50 USD/1 tusen endpoint begäranden|Konvertera talat uttryck till text-uttryck och returnerar LUIS resultat|

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
