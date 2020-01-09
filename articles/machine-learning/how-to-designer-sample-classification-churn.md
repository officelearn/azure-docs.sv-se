---
title: 'Designer: förutse omsättnings exempel'
titleSuffix: Azure Machine Learning
description: Följ det här klassificerings exemplet för att förutse omsättning med Azure Machine Learning designer & utökat besluts träd.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.reviewer: sgilley
ms.date: 12/25/2019
ms.openlocfilehash: 9e0bae722430662ddbe252e82c5108eb820ce352
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660154"
---
# <a name="use-boosted-decision-tree-to-predict-churn-with-azure-machine-learning-designer"></a>Använd utökat besluts träd för att förutse omsättning med Azure Machine Learning designer

**Designer (för hands version), exempel 5**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Lär dig hur du skapar en komplex pipeline för maskin inlärning utan att skriva en enda rad kod med hjälp av designern (för hands version).

Detta pipeline-tåg 2 **stärker besluts trädens** klassificerare för att förutsäga vanliga uppgifter för kund Relations hanterings system (CRM)-system – kund omsättning. Data värden och etiketter delas mellan flera data källor och är fördelade på maskera kund information, men vi kan fortfarande använda designern för att kombinera data uppsättningar och träna en modell med de dolda värdena.

Eftersom du försöker besvara frågan "vilket är en?" Detta kallas klassificerings problem, men du kan använda samma logik som visas i det här exemplet för att ta itu med alla typer av maskin inlärnings problem, oavsett om det är regression, klassificering, klustring och så vidare.

Här är det färdiga diagrammet för den här pipelinen:

![Pipeline-diagram](./media/how-to-designer-sample-classification-churn/pipeline-graph.png)

## <a name="prerequisites"></a>Krav

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Klicka på exempel 5 för att öppna det. 

## <a name="data"></a>Data

Data för den här pipelinen är från KDD-bägaren 2009. Den har 50 000 rader och 230 funktions kolumner. Uppgiften är att förutse omsättning, begär och försäljning för kunder som använder dessa funktioner. Mer information om data och aktiviteter finns på [KDD-webbplatsen](https://www.kdd.org/kdd-cup/view/kdd-cup-2009).

## <a name="pipeline-summary"></a>Sammanfattning av pipeline

Den här exempel pipelinen i designern visar binär klassificerare förutsägelse av omsättning, begär och försäljning, en vanlig uppgift för kund Relations hantering (CRM).

För det första är en enkel data bearbetning.

- RAW-datauppsättningen har många värden som saknas. Använd modulen **Rensa data som saknas** för att ersätta de saknade värdena med 0.

    ![Rensa data uppsättningen](media/how-to-designer-sample-classification-churn/sample5-dataset-1225.png)

- Funktionerna och motsvarande omsättning är i olika data uppsättningar. Använd modulen **Lägg till kolumner** för att lägga till etikett kolumner i funktions kolumnerna. Den första kolumnen, **Col1**, är kolumnen etikett. Från visualiserings resultatet kan vi se att data uppsättningen är obalanserad. Det finns mer negativa (-1) exempel än positiva exempel (+ 1). Vi kommer att använda **SMOTE** -modulen för att öka undervisade ärenden senare.

    ![Lägg till kolumn data uppsättningen](./media/how-to-designer-sample-classification-churn/sample5-addcol-1225.png)



- Använd modulen **dela data** för att dela upp data uppsättningen i träna-och test uppsättningar.

- Använd sedan den binära klassificeraren för besluts trädet med standard parametrarna för att bygga förutsägelse modeller. Skapa en modell per uppgift, det vill säga en modell för att förutse försäljning, begär och omsättning.

- I den högra delen av pipelinen använder vi **SMOTE** -modulen för att öka procent andelen positiva exempel. SMOTE procent anges till 100 för att dubblera positiva exempel. Läs mer om hur SMOTE-modulen fungerar med [SMOTE module reference0](algorithm-module-reference/smote.md).

## <a name="results"></a>Resultat

Visualisera utdata från modulen **utvärdera modell** för att se modellens prestanda i test uppsättningen. 

![Utvärdera resultatet](./media/how-to-designer-sample-classification-churn/sample5-evaluate-1225.png)

 Du kan flytta skjutreglaget **tröskelvärde** och se måtten för den binära klassificerings aktiviteten. 

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Utforska de andra exempel som är tillgängliga för designern:

- [Exempel 1 – regression: förutsäga ett bils pris](how-to-designer-sample-regression-automobile-price-basic.md)
- [Exempel 2-regression: jämför algoritmer för bil förutsägelse av bilar](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Exempel 3 – klassificering med funktions val: inkomst förutsägelse](how-to-designer-sample-classification-predict-income.md)
- [Exempel 4 – klassificering: förutsägelse kredit risk (kostnads känsligt)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Exempel 6 – klassificering: förutsäga flyg fördröjningar](how-to-designer-sample-classification-flight-delay.md)
- [Exempel 7 – text klassificering: Wikipedia SP 500-datauppsättning](how-to-designer-sample-text-classification.md)
