---
title: 'Exempel på visuella gränssnitt #3: regression till priser och jämför algoritmer'
titleSuffix: Azure Machine Learning
description: Den här artikeln visar hur du skapar ett komplext dator inlärnings experiment utan att skriva en enda rad kod med hjälp av det visuella gränssnittet. Lär dig att träna och jämföra flera Regressions modeller för att förutsäga bilars priser baserat på tekniska funktioner
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 2c45ccf55b1adde7150dee17fd562b24ee4777ba
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997107"
---
# <a name="sample-2---regression-predict-price-and-compare-algorithms"></a>Exempel 2 – regression: Förutsäg priser och jämför algoritmer

Lär dig hur du skapar ett komplext dator inlärnings experiment utan att skriva en enda rad kod med hjälp av det visuella gränssnittet. Detta exempel tåg och jämför flera Regressions modeller för att förutsäga bil priset baserat på dess tekniska funktioner. Vi ger dig en motivering för de val som gjorts i det här experimentet så att du kan ta itu med dina egna maskin inlärnings problem.

Om du precis har kommit igång med Machine Learning kan du ta en titt på den [grundläggande versionen](ui-sample-regression-predict-automobile-price-basic.md) av det här experimentet och se ett grundläggande Regressions experiment.

Här är det färdiga diagrammet för det här experimentet:

[![Diagram över experimentet](media/ui-sample-regression-predict-automobile-price-compare-algorithms/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Välj knappen **Öppna** för exempel 2 experiment:

    ![Öppna experimentet](media/ui-sample-regression-predict-automobile-price-compare-algorithms/open-sample2.png)

## <a name="experiment-summary"></a>Experiment Sammanfattning

Vi använder de här stegen för att bygga experimentet:

1. Hämta data.
1. För bearbetning av data.
1. Träna modellen.
1. Testa, utvärdera och jämför modeller.

## <a name="get-the-data"></a>Hämta data

I det här experimentet använder vi **data uppsättningen för Automobil pris data (RAW)** , som kommer från den Machine Learning lagrings platsen. Den här data uppsättningen innehåller 26 kolumner som innehåller information om bilar, inklusive märke, modell, pris, fordons funktioner (t. ex. antalet cylindrar), MPG och en försäkrings risk poäng. Målet med det här experimentet är att förutsäga priset på en bil.

## <a name="pre-process-the-data"></a>För behandling av data

Uppgifterna för förberedelse av data omfattar data rengöring, integrering, omvandling, reduktion och diskretiseringsmetoden stämmer eller kvantifieringsfel. I Visual Interface kan du hitta moduler för att utföra dessa åtgärder och andra uppgifter för för bearbetning i **data omvandlings** gruppen i den vänstra panelen.

I det här experimentet använder vi modulen **Välj kolumner i data uppsättning** för att undanta normaliserade förluster som har många värden som saknas. Vi använder sedan **rensa saknade data** för att ta bort rader som saknar värden. På så sätt kan du skapa en ren uppsättning tränings data.

![För bearbetning av data](media/ui-sample-regression-predict-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>Träna modellen

Problem med maskin inlärning varierar. Vanliga Machine Learning-uppgifter är klassificerings-, kluster-, Regressions-och rekommenderade system, vilket kan kräva en annan algoritm. Valet av algoritm beror ofta på kraven i användnings fallet. När du har valt en algoritm måste du justera dess parametrar för att träna en mer exakt modell. Du måste sedan utvärdera alla modeller utifrån mått som precision, intelligibility och effektivitet.

Eftersom syftet med det här experimentet är att förutsäga bil priser, och eftersom etikett kolumnen (priset) innehåller reella tal, är en Regressions modell ett bra val. Med tanke på att antalet funktioner är relativt litet (mindre än 100) och dessa funktioner inte är glesa, är besluts gränser troligt vis inte linjärt.

Om du vill jämföra prestanda för olika algoritmer använder vi två andra typer av algoritmer som inte är linjära, **stärker besluts träd regression** och **regression för besluts skogar**, för att bygga modeller. Båda algoritmerna har parametrar som du kan ändra, men vi använder standardvärdena för det här experimentet.

Vi använder modulen **dela data** för att slumpmässigt dela in indata så att inlärnings data uppsättningen innehåller 70% av original data och test data uppsättningen innehåller 30% av de ursprungliga data.

## <a name="test-evaluate-and-compare-the-models"></a>Testa, utvärdera och jämför modeller

Vi använder två olika uppsättningar av slumpmässigt utvalda data att träna och testa modellen, enligt beskrivningen i föregående avsnitt. Vi delar data uppsättningen och använder olika data uppsättningar för att träna och testa modellen för att göra utvärderingen av modellen mer mål.

När modellen har tränats använder vi **Poäng modellen** och **utvärderar modell** moduler för att generera förutsägande resultat och utvärdera modellerna. **Poäng modellen** genererar förutsägelser för test data uppsättningen med hjälp av den tränade modellen. Därefter skickar vi poängen för att **utvärdera modellen** för att generera utvärderings mått.

I det här experimentet använder vi två instanser av **utvärdera modell** för att jämföra två par modeller.

Vi jämför först två algoritmer för data uppsättningen träning.
För det andra jämför vi två algoritmer på test data uppsättningen.
Här är resultaten:

![Jämför resultaten](media/ui-sample-regression-predict-automobile-price-compare-algorithms/result.png)

De här resultaten visar att modellen som har skapats med **Regressions träd regression** har ett fel i ett lägre rot genomsnitt än den modell som bygger på **regressionen av besluts skogen**.

Båda algoritmerna har ett lägre fel på inlärnings data uppsättningen än på osett test data uppsättning.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Utforska de andra exempel som är tillgängliga för det visuella gränssnittet:

- [Exempel 1 – regression: Förutsäga ett bils pris](ui-sample-regression-predict-automobile-price-basic.md)
- [Exempel 3 – klassificering: Förutsägelse kredit risk](ui-sample-classification-predict-credit-risk-basic.md)
- [Exempel 4 – klassificering: Förutsägelse kredit risk (kostnads känsligt)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exempel 5 – klassificering: Förutsäg omsättning](ui-sample-classification-predict-churn.md)
- [Exempel 6 – klassificering: Förutsäg fördröjningar i flygning](ui-sample-classification-predict-flight-delay.md)
