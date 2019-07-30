---
title: Gränser – LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller kända begränsningar för Azure Cognitive Services Språkförståelse (LUIS). LUIS har flera gräns områden. Modellen gräns styr avsikter, entiteter och funktioner i LUIS. Kvotgränser baserat på nyckeltyp. Tangentkombination styr LUIS-webbplatsen.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/29/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 6d4991a0a05bbdd7143987bfa227cc40732cda35
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639243"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>Gränser för din LUIS-modell och nycklar
LUIS har flera gräns områden. Först är det [modellen gräns](#model-boundaries), som styr avsikter, entiteter och funktioner i LUIS. Det andra området är [kvotgränser](#key-limits) baserat på nyckeltyp. Ett tredje område av gränser är den [tangentbord kombination](#keyboard-controls) för att styra LUIS-webbplatsen. En fjärde området är den [världen regionsmappning](luis-reference-regions.md) mellan LUIS redigering av webbplatsen och LUIS [endpoint](luis-glossary.md#endpoint) API: er. 


## <a name="model-boundaries"></a>Modellen gränser

Om din app överskrider LUIS-modellens gränser och gränser bör du överväga att använda en [Luis sändnings](luis-concept-enterprise.md#dispatch-tool-and-model) app eller en [Luis-behållare](luis-container-howto.md). 

|Område|Gräns|
|--|:--|
| [Appens namn][luis-get-started-create-app] | \* Standardvärdet max |
| [Batch-testning][batch-testing]| 10 datauppsättningar, 1000 yttranden per datauppsättning|
| Explicit lista | 50 per program|
| Externa entiteter | inga gränser |
| [Avsikter][intents]|500 per program: 499 anpassade avsikter och det krävs _ingen_ avsikt.<br>[Skickat](https://aka.ms/dispatch-tool) program har motsvarande 500-sändnings källor.|
| [Lista över entiteter](./luis-concept-entity-types.md) | Klassificering 50, underordnad: 20 000 objekt. Kanoniskt namn är * maximalt antal tecken som standard. Synonymen värden har ingen begränsning av lösenordslängd. |
| [Kundlärt entiteter + roller](./luis-concept-entity-types.md):<br> sammansättning<br>gång<br>enhets roll|En gräns på antingen 100 överordnade entiteter eller 330 entiteter, beroende på vilken gräns användaren träffar först. En roll räknas som en entitet för den här gränserens syfte. Ett exempel är ett sammansatt objekt med en enkel entitet som har två roller: 1 sammansatta + 1 enkla + 2 roller = 4 av 330-enheterna.|
| [Förhands granskning-dynamiska List enheter](https://aka.ms/luis-api-v3-doc#dynamic-lists-passed-in-at-prediction-time)|2 listor med ~ 1 kB per förfrågan för förutsägelse slut punkt|
| [Mönster](luis-concept-patterns.md)|500 mönster per program.<br>Maximal längd på mönstret är 400 tecken.<br>3 Pattern.any entiteter per mönster<br>Högst 2 kapslade valfritt texter i mönstret|
| [Pattern.any](./luis-concept-entity-types.md)|100 per program, 3 pattern.any entiteter per mönster |
| [Fras lista][phrase-list]|10 fras listor, 5 000 poster per|
| [Fördefinierade entiteter](./luis-prebuilt-entities.md) | ingen gräns|
| [Reguljärt uttryck entiteter](./luis-concept-entity-types.md)|20 entiteter<br>max 500 tecken. per entitet mönster för reguljärt uttryck|
| [roles](luis-concept-roles.md)|300 roller per program. 10 roller per enhet|
| [Uttryck][utterances] | 500 tecken|
| [Yttranden][utterances] | 15 000 per program-det finns ingen gräns för antalet yttranden per avsikt|
| [Versioner](luis-concept-version.md)| ingen gräns |
| [Versions namn][luis-how-to-manage-versions] | begränsad till alfanumeriska tecken och period 10 tecken (.) |

\* Standard tecknet max är 50 tecken. 

<a name="intent-and-entity-naming"></a>

## <a name="object-naming"></a>Objekt namn

Använd inte följande tecken i följande namn.

|Object|Uteslut tecken|
|--|--|
|Namn på avsikt, entitet och roll|`:`<br>`$`|
|Versions namn|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="key-usage"></a>Nyckelanvändning

Språk förståelsen har separata nycklar, en typ för redigering och en typ för att skicka frågor till förutsägelse slut punkten. Mer information om skillnaderna mellan nyckel typer finns i [Redigera och köra slut punkts nycklar för förutsägelse i Luis](luis-concept-keys.md).

## <a name="key-limits"></a>Viktiga begränsningar

Redigering nyckel har olika begränsningar för redigering och slutpunkten. Slutpunktsnyckeln LUIS-tjänsten är endast giltig för slutpunkten frågor.


|Nyckel|Redigering|Slutpunkt|Syfte|
|--|--|--|--|
|Language Understanding redigering/starter|1 miljon/månad, 5/sekund|1 tusen/månad, 5/sekund|Redigera LUIS-appen|
|Language Understanding [prenumeration][pricing] – F0-kostnads fri nivå |Ogiltig|10 tusen/månad, 5/sekund|Fråga din LUIS-slutpunkt|
|Language Understanding [prenumeration][pricing] – S0 – Basic-nivå|Ogiltig|50 per sekund|Fråga din LUIS-slutpunkt|
|Kognitiv tjänst [prenumeration][pricing] – S0 – standard nivå|Ogiltig|50 per sekund|Fråga din LUIS-slutpunkt|
|[Sentiment analysis-integrering](luis-how-to-publish-app.md#enable-sentiment-analysis)|Ogiltig|utan kostnad|Att lägga till sentiment information, inklusive extrahering av diskussionsämne data |
|[Tal integrering](../speech-service/how-to-recognize-intents-from-speech-csharp.md)|Ogiltig|5\.50 USD/1 tusen endpoint begäranden|Konvertera talat uttryck till text-uttryck och returnerar LUIS resultat|

## <a name="keyboard-controls"></a>Keyboard kontroller

|Tangentbordsinmatning | Beskrivning | 
|--|--|
|Ctrl + E|växlar mellan token och entiteter på yttranden lista|

## <a name="website-sign-in-time-period"></a>Inloggnings tids period för webbplats

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
