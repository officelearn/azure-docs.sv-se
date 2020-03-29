---
title: Utbildningspolitik - Personalizer
description: Inlärningsinställningar bestämmer *modellträningens hyperparametrar.* Två modeller av samma data som tränas på olika inlärningsinställningar kommer att sluta annorlunda.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: abe6a2a2ec9b9978230d894c69193469f6e932e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219350"
---
# <a name="learning-policy-and-settings"></a>Utbildningspolitik och inställningar

Inlärningsinställningar bestämmer *modellträningens hyperparametrar.* Två modeller av samma data som tränas på olika inlärningsinställningar kommer att sluta annorlunda.

[Utbildningsprincipen och inställningarna](how-to-settings.md#configure-rewards-for-the-feedback-loop) är inställda på din Personalizer-resurs i Azure-portalen.

## <a name="import-and-export-learning-policies"></a>Principer för import och export av utbildning

Du kan importera och exportera utbildningsprincipfiler från Azure-portalen. Använd den här metoden för att spara befintliga principer, testa dem, ersätta dem och arkivera dem i källkodskontrollen som artefakter för framtida referens och granskning.

Lär dig [hur](how-to-manage-model.md#import-a-new-learning-policy) du importerar och exporterar en utbildningspolitik i Azure-portalen för din Personalizer-resurs.

## <a name="understand-learning-policy-settings"></a>Förstå inställningar för utbildningsprincipen

Inställningarna i utbildningsprincipen är inte avsedda att ändras. Ändra bara inställningar om du förstår hur de påverkar Personalizer. Utan denna kunskap kan du orsaka problem, inklusive invalidisering Personalizer modeller.

Personalizer använder [vowpalwabbit](https://github.com/VowpalWabbit) att träna och poäng händelserna. Se [vokalwabbitdokumentationen](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments) om hur du redigerar inlärningsinställningarna med vokalwabbit. När du har rätt argument på kommandoraden sparar du kommandot i en fil med följande format (ersätt egenskapsvärdet för argument med önskat kommando) och överför filen för att importera utbildningsinställningar i fönstret **Modell- och utbildningsinställningar** i Azure-portalen för din Personalizer-resurs.

Följande `.json` är ett exempel på en utbildningspolitik.

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

## <a name="compare-learning-policies"></a>Jämför utbildningsprinciper

Du kan jämföra hur olika utbildningsprinciper presterar mot tidigare data i Personalizer-loggar genom att göra [offlineutvärderingar](concepts-offline-evaluation.md).

[Ladda upp dina egna utbildningsprinciper](how-to-manage-model.md) för att jämföra dem med den aktuella utbildningsprincipen.

## <a name="optimize-learning-policies"></a>Optimera utbildningsprinciper

Personalizer kan skapa en optimerad inlärningspolicy i en [offlineutvärdering](how-to-offline-evaluation.md). En optimerad inlärningspolicy som har bättre belöningar i en offlineutvärdering ger bättre resultat när den används online i Personalizer.

När du har optimerat en utbildningspolitik kan du använda den direkt på Personalizer så att den omedelbart ersätter den aktuella principen. Du kan också spara den optimerade principen för vidare utvärdering och senare bestämma om du vill ignorera, spara eller tillämpa den.

## <a name="next-steps"></a>Nästa steg

* Lär dig [aktiva och inaktiva händelser](concept-active-inactive-events.md).
