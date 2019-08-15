---
title: 'Exempel på visuella gränssnitt #1: regression till förutsägelse pris'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du skapar en maskin inlärnings modell för att förutsäga ett bils pris utan att behöva skriva en enda rad kod.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 0c86955e0b10111bf9b6db0d884b73867a4467c5
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990412"
---
# <a name="sample-1---regression-predict-price"></a>Exempel 1 – regression: Förutsäga pris

Lär dig hur du skapar en Regressions modell för Machine Learning utan att skriva en enda rad kod med hjälp av det visuella gränssnittet.

Det här experimentet tränar en **besluts skog modellerings regressor** att förutsäga en bils pris baserat på tekniska funktioner som märke, modell, häst krafter och storlek. Eftersom vi försöker besvara frågan "hur mycket?" Detta kallas för ett Regressions problem. Du kan dock använda samma grundläggande steg i det här experimentet för att ta itu med alla typer av maskin inlärnings problem, oavsett om det är regression, klassificering, klustring och så vidare.

De grundläggande stegen för en utbildnings maskin inlärnings modell är:

1. Hämta data
1. För behandling av data
1. Träna modellen
1. Utvärdera modellen

Här är det sista, färdiga grafen i experimentet som du kommer att arbeta med. Vi ger dig en motivering för alla moduler så att du kan fatta liknande beslut på egen hand.

![Diagram över experimentet](media/ui-sample-regression-predict-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Välj knappen **Öppna** för exempel 1-experimentet:

    ![Öppna experimentet](media/ui-sample-regression-predict-automobile-price-basic/open-sample1.png)

## <a name="get-the-data"></a>Hämta data

I det här experimentet använder vi **data uppsättningen för Automobil pris data (RAW)** , som kommer från den Machine Learning lagrings platsen. Data uppsättningen innehåller 26 kolumner som innehåller information om bilenheter, inklusive märke, modell, pris, fordons funktioner (t. ex. antalet cylindrar), MPG och en försäkrings risk poäng. Målet med det här experimentet är att förutsäga priset på bilen.

## <a name="pre-process-the-data"></a>För behandling av data

Uppgifterna för förberedelse av data omfattar data rengöring, integrering, omvandling, reduktion och diskretiseringsmetoden stämmer eller kvantifieringsfel. I det visuella gränssnittet kan du hitta moduler för att utföra dessa åtgärder och andra uppgifter för för bearbetning i **data omvandlings** gruppen i den vänstra panelen.

Vi använder modulen **Välj kolumner i data uppsättning** för att undanta normaliserade förluster som har många värden som saknas. Vi använder sedan **rensa saknade data** för att ta bort rader som saknar värden. På så sätt kan du skapa en ren uppsättning tränings data.

![För bearbetning av data](./media/ui-sample-regression-predict-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>Träna modellen

Problem med maskin inlärning varierar. Vanliga Machine Learning-uppgifter är klassificerings-, kluster-, Regressions-och rekommenderade system, vilket kan kräva en annan algoritm. Valet av algoritm beror ofta på kraven i användnings fallet. När du har valt en algoritm måste du justera dess parametrar för att träna en mer exakt modell. Du måste sedan utvärdera alla modeller utifrån mått som precision, intelligibility och effektivitet.

Eftersom syftet med det här experimentet är att förutsäga bil priser, och eftersom etikett kolumnen (priset) innehåller reella tal, är en Regressions modell ett bra val. Med tanke på att antalet funktioner är relativt litet (mindre än 100) och dessa funktioner inte är glesa, är besluts gränser troligt vis inte linjärt. Vi använder därför **besluts skogs regression** för det här experimentet.

Vi använder modulen **dela data** för att slumpmässigt dela in indata så att inlärnings data uppsättningen innehåller 70% av original data och test data uppsättningen innehåller 30% av de ursprungliga data.

## <a name="test-evaluate-and-compare"></a>Testa, utvärdera och jämför

 Vi delar data uppsättningen och använder olika data uppsättningar för att träna och testa modellen för att göra utvärderingen av modellen mer mål.

När modellen har tränats använder vi **Poäng modellen** och utvärderar **modell** moduler för att generera förutsägande resultat och utvärdera modellerna.

**Poäng modellen** genererar förutsägelser för test data uppsättningen med hjälp av den tränade modellen. Kontrol lera resultatet genom att välja utdataporten för **Poäng modellen** och sedan välja **visualisera**.

![Resultat resultat](./media/ui-sample-regression-predict-automobile-price-basic/score-result.png)

Vi skickar sedan poängen till modulen **utvärdera modell** för att generera utvärderings mått. Kontrol lera resultatet genom att välja utdataporten för **utvärdera modellen** och sedan välja **visualisera**.

![Utvärdera resultat](./media/ui-sample-regression-predict-automobile-price-basic/evaluate-result.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Utforska de andra exempel som är tillgängliga för det visuella gränssnittet:

- [Exempel 2 – regression: Jämför algoritmer för bilpris förutsägelser för bilar](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exempel 3 – klassificering: Förutsägelse kredit risk](ui-sample-classification-predict-credit-risk-basic.md)
- [Exempel 4 – klassificering: Förutsägelse kredit risk (kostnads känsligt)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exempel 5 – klassificering: Förutsäg omsättning](ui-sample-classification-predict-churn.md)
- [Exempel 6 – klassificering: Förutsäg fördröjningar i flygning](ui-sample-classification-predict-flight-delay.md)
