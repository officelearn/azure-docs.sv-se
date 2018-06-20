---
title: Språk förstå (THOMAS) gränser | Microsoft Docs
titleSuffix: Azure
description: Den här artikeln innehåller kända begränsningar för THOMAS.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 7f46e55e11c4eb68b515a743b0f51392ffc1269e
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266812"
---
# <a name="luis-boundaries"></a>THOMAS gränser
THOMAS har flera gräns områden. Först är den [modellen gräns](#model-boundaries), som styr avsikter, enheter och funktioner i THOMAS. Det andra området är [kvotgränserna](#key-limits) baserat på nyckeltyp. En tredje gränser är den [tangentbord kombination](#keyboard-controls) för att styra THOMAS-webbplatsen. En fjärde området är den [world region mappning](luis-reference-regions.md) mellan THOMAS redigering av webbplatsen och THOMAS [endpoint](luis-glossary.md#endpoint) API: er. 


## <a name="model-boundaries"></a>Modellen gränser

|Område|Gräns|
|--|:--|--|
| [Appnamn][luis-get-started-create-app] | * Standardformat för högsta antal tecken |
| [Testa batch][batch-testing]| 10 datauppsättningar, 1000 utterances per dataset|
| **[Sammansatt](./luis-concept-entity-types.md)|100 med upp till 10 underordnade |
| Explicit lista | 50 per program|
| **[Hierarkisk](./luis-concept-entity-types.md) |100 med upp till 10 underordnade |
| [Avsikter][intents]|500 per program<br>[Dispatch-baserade](https://github.com/Microsoft/botbuilder-tools/tree/master/Dispatch) program har motsvarande 500 dispatch-källor|
| [Lista över enheter](./luis-concept-entity-types.md) | Överordnad: 50, underordnad: 20 000 objekt. Kanoniskt namn är * maximalt antal tecken som standard. Synonymer har ingen begränsning för längd. |
| [Mönster](luis-concept-patterns.md)|500 mönster per program.<br>Maximal längd på mönstret är 400 tecken.<br>3 Pattern.any entiteter per mönster<br>Högst 2 kapslade valfria texter i mönstret|
| [Pattern.any](./luis-concept-entity-types.md)|100 per program, 3 pattern.any entiteter per mönster |
| [Frasen lista][phrase-list]|10 frasen listor, 5 000 poster per|
| [Fördefinierade entiteter](./Pre-builtEntities.md) | Det finns ingen gräns|
| [Reguljärt uttryck entiteter](./luis-concept-entity-types.md)|20 enheter<br>max 500 tecken. per enhet mönstret för reguljära uttryck|
| [roles](luis-concept-roles.md)|300 roller per program. 10 roller per enhet|
| **[Enkel](./luis-concept-entity-types.md)| 100 enheter|
| [Utterance][utterances] | 500 tecken|
| [Utterances][utterances] | 15 000 per program|
| [Versionsnamnet][luis-how-to-manage-versions] | begränsad till alfanumeriska och period 10 tecken (.) |

* Standard tecknet max är 50 tecken. 

** Totalt antal enkla, hierarkisk och sammansatta enheter får inte överskrida 100. Totalt antal hierarkiska entiteter, sammansatta entiteter, enkla enheter och hierarkiska underordnade entiteter får inte överskrida 330. 

## <a name="intent-and-entity-naming"></a>Avsikten och namngivning av entitet
Använd inte följande tecken i namn på avsikten och entitet:

|Tecken|Namn|
|--|--|
|`{`|Vänster klammerparenteser|
|`}`|Höger klammer|
|`[`|Vänster hakparentes|
|`]`|Höger hakparentes|
|`\`|omvänt|

## <a name="key-limits"></a>Viktiga begränsningar
Redigering nyckel har olika gränser för redigering och slutpunkt. Nyckeln för tjänsten prenumeration av THOMAS är endast giltig för slutpunkten frågor.

|Nyckel|Redigering|Slutpunkt|Syfte|
|--|--|--|--|
|Redigera/Starter|1 miljon/månad, 5/sekund|1 tusen/månad, 5/sekund|Redigera THOMAS appen|
|[Prenumerationen] [ pricing] - F0 - kostnadsfri nivå |ogiltig|10 tusen/månad, 5/sekund|Fråga din THOMAS slutpunkt|
|[Prenumerationen] [ pricing] - S0 – grundläggande nivån|ogiltig|50 per sekund|Fråga din THOMAS slutpunkt|
|[Sentiment analysis-integrering](publishapp.md#enable-sentiment-analysis)|ogiltig|Utan kostnad|Lägger till sentiment information, inklusive viktiga frasen data extrahering |
|Dikterings-integrering|ogiltig|$5.50 USD/1 tusen endpoint begäranden|Konvertera talade utterance till utterance text och returnerar THOMAS resultat|

## <a name="keyboard-controls"></a>Tangentbordskontroller

|Tangentbordsinmatning | Beskrivning | 
|--|--|
|Ctrl + E|växlar mellan token och entiteter på utterances lista|

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
