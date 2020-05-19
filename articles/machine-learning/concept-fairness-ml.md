---
title: Utvärdera och minimera skälighet i Machine Learning-modeller
titleSuffix: Azure Machine Learning
description: Lär dig mer om skälighet i Machine Learning-modeller och hur Fairlearn python-paketet kan hjälpa dig att bygga bättre modeller.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 05/02/2020
ms.openlocfilehash: c21ec0329a7b5716a00262b7422296df3afe208b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598607"
---
# <a name="fairness-in-machine-learning-models"></a>Skälighet i Machine Learning-modeller

Lär dig mer om skälighet i maskin inlärning och hur python-paketet Fairlearn med öppen källkod kan hjälpa dig att skapa modeller som är mer rättvist.

## <a name="what-is-fairness-in-machine-learning-systems"></a>Vad är skälighet i Machine Learning-system?

Artificiell intelligens och Machine Learning-system kan visa illojalt beteende. Ett sätt att definiera oskäligt beteende är att skada, eller påverkan på människor. Det finns många typer av skador som AI-system kan ge upphov till. Två vanliga typer av AI-orsakade skador är:

- Skada på tilldelningen: ett AI-system utökar eller drar av affärs möjligheter, resurser eller information. Exempel är anställning, skol mottagningar och utlåning där en modell kan vara mycket bättre vid bästa val av en speciell grupp av personer än bland andra grupper.

- Skada för tjänst kvalitet: ett AI-system fungerar inte även för en grupp med personer som det gör för en annan. Ett tal igenkännings system kan exempelvis inte fungera både för kvinnor som det gör för män.

För att minska illojalt beteende i AI-system måste du utvärdera och minimera dessa skador.

>[!NOTE]
> Skälighet är en socioekonomisk teknisk utmaning. Många aspekter av skälighet, till exempel rättvisa och förfallna processer, samlas inte in i kvantitativa skälighet-mått. Dessutom kan många kvantitativa skälighet-mått inte vara uppfyllda samtidigt. Målet är att göra det möjligt för människor att utvärdera olika lösningar för minskning och sedan göra kompromisser som är lämpliga för deras situation.

## <a name="fairness-assessment-and-mitigation-with-fairlearn"></a>Skälighet-utvärdering och-minskning med Fairlearn

Fairlearn är ett python-paket med öppen källkod som gör det möjligt för Machine Learning Systems-utvecklare att utvärdera sina system skälighet och minimera de observerade skälighet-problemen.

Fairlearn har två komponenter:

- Utvärderings instrument panel: en Jupyter Notebook-widget för att bedöma hur en modells förutsägelser påverkar olika grupper. Du kan också jämföra flera modeller med skälighet och prestanda mått.
- Algoritmer för minskning: en uppsättning algoritmer som minimerar den binära klassificeringen och regressionen.

Tillsammans gör dessa komponenter det möjligt för data experter och affärs ledare att navigera i alla kompromisser mellan skälighet och prestanda och för att välja den strategi för minskning som bäst passar deras behov.

## <a name="fairness-assessment"></a>Skälighet-utvärdering

I Fairlearn är skälighet konceptuellt även om en metod som kallas **Group skälighet**, som frågar: vilka grupper av individer riskerar att drabbas av skador?

Relevanta grupper, även kallade del populationer, definieras genom **känsliga funktioner** eller känsliga attribut. Känsliga funktioner skickas till en Fairlearn-uppskattning som en Vector eller en matris som kallas `sensitive_features` . Termen rekommenderar att system designern bör vara känslig för dessa funktioner när du bedömer grupp skälighet. Något att vara mindful är om dessa funktioner innehåller sekretess effekter på grund av personligt identifierbar information. Men ordet "känslig" innebär inte att dessa funktioner inte ska användas för att göra förutsägelser.

Under utvärderings fasen är skälighet kvantifierat genom olikheter i mått. **Olikhets mått** kan utvärdera och jämföra modellens beteende i olika grupper, antingen som förhållanden eller som skillnader. Fairlearn stöder två klasser av olikhets mått:


- Olikheter i modell prestanda: dessa uppsättningar av mått beräknar olikheten (skillnad) i värdena för det valda prestanda måttet för olika under grupper. Några exempel är:

  - skillnader i noggrannhets grad
  - skillnader i fel frekvens
  - skillnader i precision
  - skillnader i återkallande
  - skillnader i MAE
  - många andra

- Skillnader i urvals frekvens: det här måttet innehåller skillnaden i markerings frekvens mellan olika under grupper. Ett exempel på detta är olikheter i godkännande frekvensen för lånet. Markerings frekvensen innebär en bråkdel av Datapoints i varje klass som klassificeras som 1 (i binär klassificering) eller distribution av förutsägelse värden (i regression).

## <a name="unfairness-mitigation"></a>Minskning av rättvishet

### <a name="parity-constraints"></a>Paritets begränsningar

Fairlearn innehåller en mängd olika algoritmer för minskning av god variation. Dessa algoritmer har stöd för en uppsättning begränsningar i förförutsägelsens beteende som kallas **paritets begränsningar** eller villkor. Paritets begränsningar kräver att vissa delar av förväntare beteendet är jämförbara i de grupper som är känsliga funktioner definierar (t. ex. olika races). Fairlearn: s dämpade algoritmer använder sådana paritets begränsningar för att minimera de observerade skälighet-problemen.

Fairlearn stöder följande typer av paritets begränsningar:

|Paritets begränsning  | Syfte  |Machine Learning-uppgift  |
|---------|---------|---------|
|Demografisk paritet     |  Minimera tilldelnings skador | Binära klassificering, regression |
|Lika med strider  | Diagnostisera tilldelnings-och kvalitets-och tjänst skador | Binära klassificering        |
|Förlust av begränsad grupp     |  Minimera kvaliteten på tjänstens skada | Regression |

### <a name="mitigation-algorithms"></a>Dämpa algoritmer

Fairlearn tillhandahåller postprocessing och minskade algoritmer för minskning av god godhet:

- Minskning: dessa algoritmer tar en standard uppskattning (t. ex. en LightGBM modell) i svart Box (t. ex. en modell) och genererar en uppsättning rekonstruktions modeller med hjälp av en sekvens med omviktade inlärnings data uppsättningar. Till exempel kan sökande till en viss kön vara viktad eller nedviktad för att omträna modeller och minska olikheter i olika jämställdhets grupper. Användarna kan sedan välja en modell som ger bästa möjliga kompromisser mellan precisionen (eller andra prestanda mått) och olikheter, som vanligt vis skulle behöva baseras på affärs regler och kostnads beräkningar.  
- Efter bearbetning: dessa algoritmer tar en befintlig klassificerare och den känsliga funktionen som indata. Sedan härleder de en transformering av klassificerarens förutsägelse för att genomdriva de angivna skälighet-begränsningarna. Den största fördelen med tröskelvärdes optimering är dess enkelhet och flexibilitet eftersom den inte behöver träna om modellen. 

| Integritetsalgoritm | Description | Machine Learning-uppgift | Känsliga funktioner | Paritets begränsningar som stöds | Typ av algoritm |
| --- | --- | --- | --- | --- | --- |
| `ExponentiatedGradient` | En svart ruta till god klassificering som beskrivs i [en minsknings metod för god klassificering](https://arxiv.org/abs/1803.02453) | Binära klassificering | Kategoriska | [Demografisk paritet](#parity-constraints), [desamma strider](#parity-constraints) | Minskning |
| `GridSearch` | Svart ruta som beskrivs i [en minsknings metod för god klassificering](https://arxiv.org/abs/1803.02453)| Binära klassificering | Binär | [Demografisk paritet](#parity-constraints), [desamma strider](#parity-constraints) | Minskning |
| `GridSearch` | Svart Box-metod som implementerar ett rutnät – search-variant av rättvis regression med algoritmen för begränsat grupp bortfall som beskrivs i [rättvis regression: kvantitativa definitioner och reduktionbaserade algoritmer](https://arxiv.org/abs/1905.12843) | Regression | Binär | [Förlust av begränsad grupp](#parity-constraints) | Minskning |
| `ThresholdOptimizer` | Postprocessing-algoritmen är baserad på pappers [likheten för affärs möjligheter i övervakad utbildning](https://arxiv.org/abs/1610.02413). Den här tekniken tar sig in i en befintlig klassificerare och den känsliga funktionen, och härleder en enkel färgs omvandling av klassificerarens förutsägelse för att genomdriva de angivna paritets begränsningarna. | Binära klassificering | Kategoriska | [Demografisk paritet](#parity-constraints), [desamma strider](#parity-constraints) | Efter bearbetning |

## <a name="next-steps"></a>Nästa steg

- Om du vill lära dig hur du använder olika komponenter kan du titta på [Fairlearn GitHub-lagringsplatsen](https://github.com/fairlearn/fairlearn/) och [exempel antecknings böcker](https://github.com/fairlearn/fairlearn/tree/master/notebooks).
- Lär dig mer om att bevara data integriteten med hjälp av [differentiell sekretess och WhisperNoise-paketet](concept-differential-privacy.md).