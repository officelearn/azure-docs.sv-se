---
title: 'Exempel på visuella gränssnitt #5: Klassificering för att förutsäga omsättning + begär och försäljning'
titleSuffix: Azure Machine Learning
description: Det här visuella gränssnittet exempel experiment visar binär klassificerar förutsägelse för omsättning, en vanlig uppgift för kund Relations hantering (CRM).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 054ac3ea5224a4ffea2e7bae9da329ba7bc81233
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997140"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>Exempel 5 – klassificering: Förutsäga omsättning, begär och försäljning 

Lär dig hur du skapar ett komplext dator inlärnings experiment utan att skriva en enda rad kod med hjälp av det visuella gränssnittet.

Det här experimentet tränar tre, **dubbelriktade besluts träd** med tre klasser för att förutsäga vanliga uppgifter för kund Relations hanterings system (CRM): omsättning, begär och försäljning. Data värden och etiketter delas upp i flera data källor och går att avkoda till maskera kund information, men vi kan fortfarande använda det visuella gränssnittet för att kombinera data uppsättningar och träna en modell med hjälp av de kodade värdena.

Eftersom vi försöker besvara frågan "vilket är en?" Detta kallas för ett klassificerings problem. Du kan dock använda samma steg i det här experimentet för att ta itu med alla typer av maskin inlärnings problem, oavsett om det är regression, klassificering, klustring och så vidare.

Här är det färdiga diagrammet för det här experimentet:

![Experiment diagram](./media/ui-sample-classification-predict-churn/experiment-graph.png)

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Välj knappen **Öppna** för exempel 5-experimentet.

    ![Öppna experimentet](media/ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>Data

De data vi använder för det här experimentet är från KDD-bägare 2009. Data uppsättningen har 50 000 rader och 230 funktions kolumner. Uppgiften är att förutse omsättning, begär och försäljning för kunder som använder dessa funktioner. Mer information om data och aktiviteter finns på [KDD-webbplatsen](https://www.kdd.org/kdd-cup/view/kdd-cup-2009).

## <a name="experiment-summary"></a>Experiment Sammanfattning

Det här visuella gränssnittets exempel experiment visar binära klassificeraren för omsättning, begär och försäljning, en vanlig uppgift för kund Relations hantering (CRM).

Först utför vi lite enkel data bearbetning.

- RAW-datauppsättningen innehåller massor av saknade värden. Vi använder modulen **Rensa data som saknas** för att ersätta de värden som saknas med 0.

    ![Rensa data uppsättningen](./media/ui-sample-classification-predict-churn/cleaned-dataset.png)

- Funktionerna och motsvarande omsättnings-, begär-och försäljnings etiketter finns i olika data uppsättningar. Vi använder modulen **Lägg till kolumner** för att lägga till etikett kolumner i funktions kolumnerna. Den första kolumnen, **Col1**, är kolumnen etikett. Resten av kolumnerna, **Var1**, **Var2**och så vidare, är funktions kolumnerna.

    ![Lägg till kolumn data uppsättningen](./media/ui-sample-classification-predict-churn/added-column1.png)

- Vi använder modulen **dela data** för att dela upp data uppsättningen i träna-och test uppsättningar.

    Vi använder sedan den binära klassificeraren för besluts trädet med standard parametrarna för att bygga förutsägelse modeller. Vi skapar en modell per uppgift, det vill säga en modell för att förutse försäljning, begär och omsättning.

## <a name="results"></a>Resultat

Visualisera utdata från modulen **utvärdera modell** för att se modellens prestanda i test uppsättningen. ROC kurvan visar att modellen fungerar bättre än en slumpmässig modell för aktiviteten upp till Sälj Ande. Arean under kurvan (AUC) är 0,857. Vid tröskelvärdet är 0,5 precisionen 0,7, återkallning är 0,463 och F1-poängen är 0,545.

![Utvärdera resultaten](./media/ui-sample-classification-predict-churn/evaluate-result.png)

 Du kan flytta skjutreglaget **tröskelvärde** och se måtten för den binära klassificerings aktiviteten.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Utforska de andra exempel som är tillgängliga för det visuella gränssnittet:

- [Exempel 1 – regression: Förutsäga ett bils pris](ui-sample-regression-predict-automobile-price-basic.md)
- [Exempel 2 – regression: Jämför algoritmer för bilpris förutsägelser för bilar](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exempel 3 – klassificering: Förutsägelse kredit risk](ui-sample-classification-predict-credit-risk-basic.md)
- [Exempel 4 – klassificering: Förutsägelse kredit risk (kostnads känsligt)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exempel 6 – klassificering: Förutsäg fördröjningar i flygning](ui-sample-classification-predict-flight-delay.md)
