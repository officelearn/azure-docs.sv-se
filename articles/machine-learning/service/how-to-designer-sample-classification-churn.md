---
title: 'Design exempel #5: klassificering för att förutsäga omsättning + begär + upp-försäljning'
titleSuffix: Azure Machine Learning
description: Den här designerns (förhands granskning) exempel pipeline visar binär klassificerare för omsättning, en vanlig uppgift för kund Relations hantering (CRM).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 11/04/2019
ms.openlocfilehash: 52fcebb201fbdeebd7b75d9735fd81c3a647c337
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516245"
---
# <a name="sample-5---classification-predict-churn"></a>Exempel 5 – klassificering: förutsägelse omsättning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Lär dig hur du skapar en komplex pipeline för maskin inlärning utan att skriva en enda rad kod med hjälp av designern (för hands version).

Detta pipeline-tåg 2 **stärker besluts trädens** klassificerare för att förutsäga vanliga uppgifter för kund Relations hanterings system (CRM)-system – kund omsättning. Data värden och etiketter delas mellan flera data källor och är fördelade på maskera kund information, men vi kan fortfarande använda designern för att kombinera data uppsättningar och träna en modell med de dolda värdena.

Eftersom du försöker besvara frågan "vilket är en?" Detta kallas klassificerings problem, men du kan använda samma logik som visas i det här exemplet för att ta itu med alla typer av maskin inlärnings problem, oavsett om det är regression, klassificering, klustring och så vidare.

Här är det färdiga diagrammet för den här pipelinen:

![Pipeline-diagram](./media/how-to-ui-sample-classification-predict-churn/pipeline-graph.png)

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Klicka på exempel 5 för att öppna det. 

## <a name="data"></a>Data

Data för den här pipelinen är från KDD-bägaren 2009. Den har 50 000 rader och 230 funktions kolumner. Uppgiften är att förutse omsättning, begär och försäljning för kunder som använder dessa funktioner. Mer information om data och aktiviteter finns på [KDD-webbplatsen](https://www.kdd.org/kdd-cup/view/kdd-cup-2009).

## <a name="pipeline-summary"></a>Sammanfattning av pipeline

Den här exempel pipelinen i designern visar binär klassificerare förutsägelse av omsättning, begär och försäljning, en vanlig uppgift för kund Relations hantering (CRM).

För det första är en enkel data bearbetning.

- RAW-datauppsättningen har många värden som saknas. Använd modulen **Rensa data som saknas** för att ersätta de saknade värdena med 0.

    ![Rensa data uppsättningen](./media/how-to-ui-sample-classification-predict-churn/cleaned-dataset.png)

- Funktionerna och motsvarande omsättning är i olika data uppsättningar. Använd modulen **Lägg till kolumner** för att lägga till etikett kolumner i funktions kolumnerna. Den första kolumnen, **Col1**, är kolumnen etikett. Från visualiserings resultatet kan vi se att data uppsättningen är obalanserad. Det finns mer negativa (-1) exempel än positiva exempel (+ 1). Vi kommer att använda **SMOTE** -modulen för att öka undervisade ärenden senare.

    ![Lägg till kolumn data uppsättningen](./media/how-to-ui-sample-classification-predict-churn/added-column1.png)



- Använd modulen **dela data** för att dela upp data uppsättningen i träna-och test uppsättningar.

- Använd sedan den binära klassificeraren för besluts trädet med standard parametrarna för att bygga förutsägelse modeller. Skapa en modell per uppgift, det vill säga en modell för att förutse försäljning, begär och omsättning.

- I den högra delen av pipelinen använder vi **SMOTE** -modulen för att öka procent andelen positiva exempel. SMOTE procent anges till 100 för att dubblera positiva exempel. Läs mer om hur SMOTE-modulen fungerar med [SMOTE module reference0](../././algorithm-module-reference/SMOTE.md).

## <a name="results"></a>Resultat

Visualisera utdata från modulen **utvärdera modell** för att se modellens prestanda i test uppsättningen. 

![Utvärdera resultaten](./media/how-to-ui-sample-classification-predict-churn/evaluate-result.png)

 Du kan flytta skjutreglaget **tröskelvärde** och se måtten för den binära klassificerings aktiviteten. 

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Utforska de andra exempel som är tillgängliga för designern:

- [Exempel 1 – regression: förutsäga ett bils pris](how-to-designer-sample-regression-automobile-price-basic.md)
- [Exempel 2-regression: jämför algoritmer för bil förutsägelse av bilar](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Exempel 3 – klassificering med funktions val: inkomst förutsägelse](how-to-designer-sample-classification-predict-income.md)
- [Exempel 4 – klassificering: förutsägelse kredit risk (kostnads känsligt)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Exempel 6 – klassificering: förutsäga flyg fördröjningar](how-to-designer-sample-classification-flight-delay.md)
- [Exempel 7 – text klassificering: Wikipedia SP 500-datauppsättning](how-to-designer-sample-text-classification.md)
