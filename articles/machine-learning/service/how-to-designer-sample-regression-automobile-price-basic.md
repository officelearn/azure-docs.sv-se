---
title: 'Design exempel #1: regression till förutsägelse pris'
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar en maskin inlärnings modell för att förutsäga ett bils pris utan att behöva skriva en enda rad kod.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: 14b9f5fd1459ebd3341218a4ada14b16ddb4853f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515478"
---
# <a name="sample-1---regression-predict-price"></a>Exempel 1 – regression: Förutsäg pris
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Lär dig hur du skapar en Regressions modell för Machine Learning utan att skriva en enda rad kod med hjälp av designern (för hands version).

Den här pipelinen tågen en **besluts skog modellerings regressor** att förutsäga bils pris baserat på tekniska funktioner som märke, modell, häst krafter och storlek. Eftersom du försöker besvara frågan "hur mycket?" Detta kallas för ett Regressions problem. Du kan dock använda samma grundläggande steg i det här exemplet för att ta itu med alla typer av maskin inlärnings problem, oavsett om det är regression, klassificering, klustring och så vidare.

De grundläggande stegen för en utbildnings maskin inlärnings modell är:

1. Hämta data
1. För behandling av data
1. Träna modellen
1. Utvärdera modellen

Här är det slutliga, färdiga diagrammet i pipelinen. Den här artikeln innehåller en motivering för alla moduler så att du kan fatta liknande beslut på egen hand.

![Diagram över pipelinen](media/how-to-ui-sample-regression-predict-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Klicka på exempel 1 för att öppna IT-från en omfattande


## <a name="get-the-data"></a>Hämta data

I det här exemplet används RAW-datauppsättningen **(Automobile Price data)** som är från den Machine Learning lagrings platsen. Data uppsättningen innehåller 26 kolumner som innehåller information om bilenheter, inklusive märke, modell, pris, fordons funktioner (t. ex. antalet cylindrar), MPG och en försäkrings risk poäng. Syftet med det här exemplet är att förutsäga priset på bilen.

## <a name="pre-process-the-data"></a>För behandling av data

Uppgifterna för förberedelse av data omfattar data rengöring, integrering, omvandling, reduktion och diskretiseringsmetoden stämmer eller kvantifieringsfel. I designern hittar du moduler för att utföra dessa åtgärder och andra uppgifter för för bearbetning i **data omvandlings** gruppen i den vänstra panelen.

Använd modulen **Välj kolumner i data uppsättning** för att undanta normaliserade förluster som har många värden som saknas. Använd sedan **rensa saknade data** för att ta bort rader som saknar värden. På så sätt kan du skapa en ren uppsättning tränings data.

![För bearbetning av data](./media/how-to-ui-sample-regression-predict-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>Träna modellen

Problem med maskin inlärning varierar. Vanliga Machine Learning-uppgifter är klassificerings-, kluster-, Regressions-och rekommenderade system, vilket kan kräva en annan algoritm. Valet av algoritm beror ofta på kraven i användnings fallet. När du har valt en algoritm måste du justera dess parametrar för att träna en mer exakt modell. Du måste sedan utvärdera alla modeller utifrån mått som precision, intelligibility och effektivitet.

Eftersom syftet med det här exemplet är att förutsäga bil priser och att kolumnen etikett (pris) innehåller reella tal är en Regressions modell ett bra val. Med tanke på att antalet funktioner är relativt litet (mindre än 100) och dessa funktioner inte är glesa, är besluts gränser troligt vis inte linjärt. Vi använder därför **besluts skogs regression** för denna pipeline.

Använd modulen **dela data** för att slumpmässigt dela in indata så att inlärnings data uppsättningen innehåller 70% av original data och test data uppsättningen innehåller 30% av de ursprungliga data.

## <a name="test-evaluate-and-compare"></a>Testa, utvärdera och jämför

Dela data uppsättningen och Använd olika data uppsättningar för att träna och testa modellen för att göra utvärderingen av modellen mer mål.

När modellen har tränats kan du använda **Poäng modellen** och **utvärdera modell** moduler för att generera förutsägande resultat och utvärdera modellerna.

**Poäng modellen** genererar förutsägelser för test data uppsättningen med hjälp av den tränade modellen. Kontrol lera resultatet genom att välja utdataporten för **Poäng modellen** och sedan välja **visualisera**.

![Resultat resultat](./media/how-to-ui-sample-regression-predict-automobile-price-basic/score-result.png)

Skicka poängen till modulen **utvärdera modell** för att generera utvärderings mått. Kontrol lera resultatet genom att välja utdataporten för **utvärdera modellen** och sedan välja **visualisera**.

![Utvärdera resultat](./media/how-to-ui-sample-regression-predict-automobile-price-basic/evaluate-result.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Utforska de andra exempel som är tillgängliga för designern:

- [Exempel 2-regression: jämför algoritmer för bil förutsägelse av bilar](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Exempel 3 – klassificering med funktions val: inkomst förutsägelse](how-to-designer-sample-classification-predict-income.md)
- [Exempel 4 – klassificering: förutsägelse kredit risk (kostnads känsligt)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Exempel 5 – klassificering: förutsägelse omsättning](how-to-designer-sample-classification-churn.md)
- [Exempel 6 – klassificering: förutsäga flyg fördröjningar](how-to-designer-sample-classification-flight-delay.md)
- [Exempel 7 – text klassificering: Wikipedia SP 500-datauppsättning](how-to-designer-sample-text-classification.md)