---
title: 'Exempel på visuella gränssnitt #5: klassificering för att förutsäga omsättning + begär och försäljning'
titleSuffix: Azure Machine Learning
description: Den här exempel pipeline för visuella gränssnitt visar binär klassificerare för omsättning, en vanlig uppgift för kund Relations hantering (CRM).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 09/23/2019
ms.openlocfilehash: 82639779dde08bb1f71fb75dba62038dbf34d1b6
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693551"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>Exempel 5 – klassificering: förutsägelse omsättning, begär och försäljning 

Lär dig hur du skapar en komplex pipeline för maskin inlärning utan att skriva en enda rad kod med hjälp av det visuella gränssnittet.

Den här pipelinen tågen tre, bättre klassificerare för **besluts träd i besluts träd** för att förutsäga vanliga uppgifter för kund Relations hanterings system (CRM): omsättning, begär och upp-försäljning. Data värden och etiketter delas mellan flera data källor och är fördelade på maskera kund information, men vi kan fortfarande använda det visuella gränssnittet för att kombinera data uppsättningar och träna en modell med de dolda värdena.

Eftersom du försöker besvara frågan "vilket är en?" Detta kallas klassificerings problem, men du kan använda samma logik som visas i det här exemplet för att ta itu med alla typer av maskin inlärnings problem, oavsett om det är regression, klassificering, klustring och så vidare.

Här är det färdiga diagrammet för den här pipelinen:

![Pipeline-diagram](./media/how-to-ui-sample-classification-predict-churn/pipeline-graph.png)

## <a name="prerequisites"></a>Krav

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Välj knappen **Öppna** för exempel 5 pipelinen.

    ![Öppna pipelinen](media/how-to-ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>Data

Data för den här pipelinen är från KDD-bägaren 2009. Den har 50 000 rader och 230 funktions kolumner. Uppgiften är att förutse omsättning, begär och försäljning för kunder som använder dessa funktioner. Mer information om data och aktiviteter finns på [KDD-webbplatsen](https://www.kdd.org/kdd-cup/view/kdd-cup-2009).

## <a name="pipeline-summary"></a>Sammanfattning av pipeline

Den här exempel pipeline för visuella gränssnitt visar binära klassificeraren för omsättning, begär och försäljning, en vanlig uppgift för kund Relations hantering (CRM).

För det första är en enkel data bearbetning.

- RAW-datauppsättningen har många värden som saknas. Använd modulen **Rensa data som saknas** för att ersätta de saknade värdena med 0.

    ![Rensa data uppsättningen](./media/how-to-ui-sample-classification-predict-churn/cleaned-dataset.png)

- Funktionerna och motsvarande omsättnings-, begär-och försäljnings etiketter finns i olika data uppsättningar. Använd modulen **Lägg till kolumner** för att lägga till etikett kolumner i funktions kolumnerna. Den första kolumnen, **Col1**, är kolumnen etikett. Resten av kolumnerna, **Var1**, **Var2**och så vidare, är funktions kolumnerna.

    ![Lägg till kolumn data uppsättningen](./media/how-to-ui-sample-classification-predict-churn/added-column1.png)

- Använd modulen **dela data** för att dela upp data uppsättningen i träna-och test uppsättningar.

- Använd sedan den binära klassificeraren för besluts trädet med standard parametrarna för att bygga förutsägelse modeller. Skapa en modell per uppgift, det vill säga en modell för att förutse försäljning, begär och omsättning.

## <a name="results"></a>Resultat

Visualisera utdata från modulen **utvärdera modell** för att se modellens prestanda i test uppsättningen. ROC kurvan visar att modellen fungerar bättre än en slumpmässig modell för aktiviteten upp till Sälj Ande. Arean under kurvan (AUC) är 0,857. Vid tröskelvärdet är 0,5 precisionen 0,7, återkallning är 0,463 och F1-poängen är 0,545.

![Utvärdera resultaten](./media/how-to-ui-sample-classification-predict-churn/evaluate-result.png)

 Du kan flytta skjutreglaget **tröskelvärde** och se måtten för den binära klassificerings aktiviteten.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Utforska de andra exempel som är tillgängliga för det visuella gränssnittet:

- [Exempel 1 – regression: förutsäga ett bils pris](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [Exempel 2-regression: jämför algoritmer för bil förutsägelse av bilar](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exempel 3 – klassificering: förutsägelse kredit risk](how-to-ui-sample-classification-predict-credit-risk-basic.md)
- [Exempel 4 – klassificering: förutsägelse kredit risk (kostnads känsligt)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exempel 6 – klassificering: förutsäga flyg fördröjningar](how-to-ui-sample-classification-predict-flight-delay.md)
- [Exempel 7 – text klassificering: bok granskningar](how-to-ui-sample-text-classification.md)
