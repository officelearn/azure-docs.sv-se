---
title: Gränser - LUIS
description: Den här artikeln innehåller de kända gränserna för Azure Cognitive Services Language Understanding (LUIS). LUIS har flera gränsområden. Modellgräns styr avsikter, entiteter och funktioner i LUIS. Kvotgränser baserade på nyckeltyp. Tangentbordskombinationen styr LUIS-webbplatsen.
ms.topic: reference
ms.date: 04/02/2020
ms.openlocfilehash: 4aa69cb0fd36fe5bf4ea2928022aea602b8830d6
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618871"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>Gränser för luis-modellen och -tangenterna
LUIS har flera gränsområden. Den första är [modellgränsen](#model-boundaries), som styr avsikter, entiteter och funktioner i LUIS. Det andra området är [kvotgränser](#key-limits) baserade på nyckeltyp. Ett tredje gränsområde är [tangentbordskombinationen](#keyboard-controls) för att styra LUIS-webbplatsen. Ett fjärde område är [världsregionmappningen](luis-reference-regions.md) mellan LUIS-redigeringswebbplatsen och LUIS-slutpunkts-API:erna. [endpoint](luis-glossary.md#endpoint)


## <a name="model-boundaries"></a>Modellgränser

Om din app överskrider LUIS-modellens gränser och gränser bör du överväga att använda en [LUIS-utskicksapp](luis-concept-enterprise.md#dispatch-tool-and-model) eller använda en [LUIS-behållare](luis-container-howto.md).

|Område|Gräns|
|--|:--|
| [Appnamn][luis-get-started-create-app] | *Max standardtecken |
| Program| 500 program per Azure-redigeringsresurs |
| [Batch-testning][batch-testing]| 10 datauppsättningar, 1000 yttranden per datauppsättning|
| Explicit lista | 50 per ansökan|
| Externa enheter | inga gränser |
| [Avsikter][intents]|500 per program: 499 anpassade avsikter och den _nödvändiga ingen_ avsikt.<br>[Dispatch-baserade](https://aka.ms/dispatch-tool) program har motsvarande 500 leveranskällor.|
| [Listentiteter](./luis-concept-entity-types.md) | Förälder: 50, barn: 20 000 artiklar. Kanoniskt namn är * standardtecken max. Synonymvärden har ingen längdbegränsning. |
| [Maskininlärda entiteter + roller:](./luis-concept-entity-types.md)<br> Sammansatt<br>Enkel<br>entitetsroll|En gräns på antingen 100 överordnade entiteter eller 330 entiteter, beroende på vilken gräns användaren träffar först. En roll räknas som en entitet för den här gränsen. Ett exempel är en sammansatt med en enkel entitet, som har 2 roller är: 1 sammansatt + 1 enkel + 2 roller = 4 av de 330 entiteterna.<br>Delkomponenter kan kapslas upp till 5 nivåer.|
|Modell som funktion| Maximalt antal modeller som kan användas som deskriptor (funktion) till en viss modell som ska vara 10 modeller. Det maximala antalet fraslistor som används som deskriptor (funktion) för att en viss modell ska vara 10-fraslistor.|
| [Förhandsgranska - Dynamiska listentiteter](https://aka.ms/luis-api-v3-doc#dynamic-lists-passed-in-at-prediction-time)|2 listor med ~1k per begäran om frågeförutsägelsförutsägels|
| [Mönster](luis-concept-patterns.md)|500 mönster per applikation.<br>Den maximala längden på mönstret är 400 tecken.<br>3 Pattern.any enheter per mönster<br>Högst 2 kapslade valfria texter i mönster|
| [Pattern.any](./luis-concept-entity-types.md)|100 per applikation, 3 pattern.any enheter per mönster |
| [Fraslista][phrase-list]|500 fraslistor. 10 globala fraslistor på grund av modellen som en funktionsgräns. Den icke utbytbara fraslistan har högst 5 000 fraser. Utbytbar fraslista har max 50 000 fraser. Maximalt antal fraser per applicering av 500 000 fraser.|
| [Fördefinierade enheter](./luis-prebuilt-entities.md) | ingen gräns|
| [Lägga till entiteter för reguljära uttryck](./luis-concept-entity-types.md)|20 enheter<br>500 tecken max. per mönster för ett reguljärt uttryck|
| [Roller](luis-concept-roles.md)|300 roller per program. 10 roller per entitet|
| [Yttrande][utterances] | 500 tecken|
| [Yttranden][utterances] | 15 000 per program - det finns ingen gräns för antalet yttranden per avsikt|
| [Versioner](luis-concept-version.md)| 100 versioner per program |
| [Versionsnamn][luis-how-to-manage-versions] | 10 tecken begränsade till alfanumeriskt och punkt (.) |

*Standardtecken max är 50 tecken.

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>Namn unikhet

Använd följande namngivningsregler för unika egenskaper.

Följande måste vara unikt i en LUIS-app:

* versionsnamn
* Avsikt
* Enhet
* roles

Följande måste vara unika inom det tillämpade scopet:

* fraslista

## <a name="object-naming"></a>Namngivning av objekt

Använd inte följande tecken i följande namn.

|Objekt|Exkludera tecken|
|--|--|
|Avsikts-, entitets- och rollnamn|`:`<br>`$` <br> `&`|
|Versionsnamn|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="resource-usage-and-limits"></a>Resursanvändning och begränsningar

Språk förstå har separata resurser, en typ för redigering, och en typ för att fråga förutsägelse slutpunkten. Mer information om skillnaderna mellan nyckeltyper finns i [Slutpunktsnycklar för redigering och frågeprediktion i LUIS](luis-concept-keys.md).

<a name="key-limits"></a>

### <a name="authoring-resource-limits"></a>Skapa resursbegränsningar

Använd _typen_ `LUIS.Authoring`, , när du filtrerar resurser i Azure-portalen. LUIS begränsar 500 program per Azure-redigeringsresurs.

|Skapa resurs|Författande TPS|
|--|--|
|Starter|1 miljon/månad, 5/sekund|
|F0 - Gratis nivå |1 miljon/månad, 5/sekund|

* TPS = Transaktioner per sekund

[Läs mer om priser.][pricing]

### <a name="query-prediction-resource-limits"></a>Resursbegränsningar för frågeprediktion

Använd _typen_ `LUIS`, , när du filtrerar resurser i Azure-portalen. SLUTpunktsresursen LUIS-frågeprediktion, som används under körningen, är endast giltig för slutpunktsfrågor.

|Frågeprediktionsresurs|Fråga TPS|
|--|--|
|F0 - Gratis nivå |10 tusen/månad, 5/sekund|
|S0 - Standardnivå|50/sekund|

### <a name="sentiment-analysis"></a>Sentimentanalys

[Sentimentanalysintegration](luis-how-to-publish-app.md#enable-sentiment-analysis), som ger sentimentinformation, tillhandahålls utan att kräva en annan Azure-resurs.

### <a name="speech-integration"></a>Integrering av tal

[Talintegrering](../speech-service/how-to-recognize-intents-from-speech-csharp.md) ger 1 tusen slutpunktsbegäranden per styckkostnad.

[Läs mer om priser.][pricing]

## <a name="keyboard-controls"></a>Tangentbordskontroller

|Tangentbordsinmatning | Beskrivning |
|--|--|
|Kontroll+E|växlar mellan token och entiteter i listan över yttranden|

## <a name="website-sign-in-time-period"></a>Inloggningsperiod för webbplats

Din inloggningsåtkomst är i **60 minuter.** Efter denna tidsperiod får du det här felet. Du måste logga in igen.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
