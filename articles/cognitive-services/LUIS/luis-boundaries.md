---
title: Gränser – LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller kända begränsningar för Azure Cognitive Services Språkförståelse (LUIS). LUIS har flera gräns områden. Modellen gräns styr avsikter, entiteter och funktioner i LUIS. Kvotgränser baserat på nyckeltyp. Tangentkombination styr LUIS-webbplatsen.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/07/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: d584b00caef628eb9dfd085b1fdce2bb7b353988
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220087"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>Gränser för din LUIS-modell och nycklar
LUIS har flera gräns områden. Den första är [modellens gränser](#model-boundaries), som styr avsikter, entiteter och funktioner i Luis. Det andra avsnittet är [kvot gränser](#key-limits) baserat på nyckel typ. En tredje del av gränserna är [tangentkombinationen](#keyboard-controls) för att kontrol lera Luis-webbplatsen. Ett fjärde område är [Mappning av världs regionen](luis-reference-regions.md) mellan Luis Authoring-webbplatsen och API: erna för Luis- [slutpunkt](luis-glossary.md#endpoint) .


## <a name="model-boundaries"></a>Modellen gränser

Om din app överskrider LUIS-modellens gränser och gränser bör du överväga att använda en [Luis sändnings](luis-concept-enterprise.md#dispatch-tool-and-model) app eller en [Luis-behållare](luis-container-howto.md).

|Område|Gräns|
|--|:--|
| [Appens namn][luis-get-started-create-app] | \* Standardvärdet max |
| Program| 500 program per Azure Authoring-resurs |
| [Batch-testning][batch-testing]| 10 datauppsättningar, 1000 yttranden per datauppsättning|
| Explicit lista | 50 per program|
| Externa entiteter | inga gränser |
| [Avsikter][intents]|500 per program: 499 anpassade avsikter och det krävs _ingen_ avsikt.<br>[Skickat](https://aka.ms/dispatch-tool) program har motsvarande 500-sändnings källor.|
| [Lista entiteter](./luis-concept-entity-types.md) | Överordnad: 50, underordnad: 20 000 objekt. Kanoniskt namn är * maximalt antal tecken som standard. Synonymen värden har ingen begränsning av lösenordslängd. |
| [Kundlärt entiteter + roller](./luis-concept-entity-types.md):<br> sammansättning<br>gång<br>enhets roll|En gräns på antingen 100 överordnade entiteter eller 330 entiteter, beroende på vilken gräns användaren träffar först. En roll räknas som en entitet för den här gränserens syfte. Ett exempel är ett sammansatt objekt med en enkel entitet, som har två roller: 1 sammansatt + 1 enkla + 2 roller = 4 av 330-enheterna.<br>Del komponenter kan kapslas upp till 5 nivåer.|
|Modell som en funktion| Det maximala antalet modeller som kan användas som en beskrivning (funktion) till en speciell modell som är 10 modeller. Det maximala antalet fras listor som används som en beskrivning (funktion) för en speciell modell som är 10 fras listor.|
| [Förhands granskning-dynamiska List enheter](https://aka.ms/luis-api-v3-doc#dynamic-lists-passed-in-at-prediction-time)|2 listor med ~ 1 kB per förfrågan för förutsägelse slut punkt|
| [Skuggning](luis-concept-patterns.md)|500 mönster per program.<br>Maximal längd på mönstret är 400 tecken.<br>3 Pattern.any entiteter per mönster<br>Högst 2 kapslade valfritt texter i mönstret|
| [Mönster. alla](./luis-concept-entity-types.md)|100 per program, 3 pattern.any entiteter per mönster |
| [Fras lista][phrase-list]|500 fras listor. Icke-utbytbara phraselist har högst 5 000 fraser. Utbytbara Phraselist har högst 50 000 fraser. Maximalt antal fraser per program 500 000-fraser.|
| [Fördefinierade entiteter](./luis-prebuilt-entities.md) | ingen gräns|
| [Entiteter med reguljära uttryck](./luis-concept-entity-types.md)|20 entiteter<br>max 500 tecken. per entitet mönster för reguljärt uttryck|
| [roles](luis-concept-roles.md)|300 roller per program. 10 roller per enhet|
| [Uttryck][utterances] | 500 tecken|
| [Yttranden][utterances] | 15 000 per program-det finns ingen gräns för antalet yttranden per avsikt|
| [5](luis-concept-version.md)| 100-versioner per program |
| [Versions namn][luis-how-to-manage-versions] | begränsad till alfanumeriska tecken och period 10 tecken (.) |

\* Standard tecknet max är 50 tecken.

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>Namn unikhet

Använd följande unika namngivnings regler.

Följande måste vara unikt inom en LUIS-app:

* Versions namn
* avsikt
* entitet
* roles

Följande måste vara unikt inom omfånget som används:

* fras lista

## <a name="object-naming"></a>Objekt namn

Använd inte följande tecken i följande namn.

|Objekt|Uteslut tecken|
|--|--|
|Namn på avsikt, entitet och roll|`:`<br>`$` <br> `&`|
|Versions namn|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="key-usage"></a>Nyckelanvändning

Språk förståelsen har separata nycklar, en typ för redigering och en typ för att skicka frågor till förutsägelse slut punkten. Mer information om skillnaderna mellan nyckel typer finns i [Redigera och köra slut punkts nycklar för förutsägelse i Luis](luis-concept-keys.md).

<a name="key-limits"></a>

## <a name="resource-key-limits"></a>Begränsningar för resurs nyckel

Resurs nycklarna har olika gränser för redigering och slut punkt. Slut punkts nyckeln för LUIS förutsägelse fråga är endast giltig för slut punkts frågor.

* 500 program per Azure Authoring-resurs

|Nyckel|Redigering|Slutpunkt|Syfte|
|--|--|--|--|
|Starter|1 miljon/månad, 5/sekund|1 tusen/månad, 5/sekund|Redigera LUIS-appen|
|F0 – kostnads fri nivå |1 miljon/månad, 5/sekund|10 tusen/månad, 5/sekund|Fråga din LUIS-slutpunkt|
|S0 – Basic-nivå|-|50 per sekund|Fråga din LUIS-slutpunkt|
|S0 – standard nivå|-|50 per sekund|Fråga din LUIS-slutpunkt|
|[Integrering av sentiment-analys](luis-how-to-publish-app.md#enable-sentiment-analysis)|-|-|Att lägga till sentiment-information, inklusive data extrahering för nyckel fraser, tillhandahålls utan att det krävs någon annan Azure-resurs. |
|[Tal integrering](../speech-service/how-to-recognize-intents-from-speech-csharp.md)|-|1000 slut punkts begär Anden per enhets kostnad|Konvertera talat uttryck till text-uttryck och returnerar LUIS resultat|

[Läs mer om priser.][pricing]

## <a name="keyboard-controls"></a>Keyboard kontroller

|Tangentbordsinmatning | Beskrivning |
|--|--|
|Ctrl + E|växlar mellan token och entiteter på yttranden lista|

## <a name="website-sign-in-time-period"></a>Inloggnings tids period för webbplats

Din inloggnings åtkomst är i **60 minuter**. Efter denna tidsperiod får du det här felet. Du måste logga in igen.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
