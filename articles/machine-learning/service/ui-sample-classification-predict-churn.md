---
title: 'Klassificering: Förutsäga kundomsättning, begär och ökad försäljning '
titleSuffix: Azure Machine Learning service
description: Detta visuella gränssnittet exempelexperiment visar binär klassificerare förutsägelser av omsättningen, vanliga åtgärder för hantering av kundrelationer (CRM).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/02/2019
ms.openlocfilehash: 1cb533348236905b7c4e9b58968041745af0e71b
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028447"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>Exempel 5 - klassificering: Förutsäga kundomsättning, begär och ökad försäljning 

Detta visuella gränssnittet exempelexperiment visar binär klassificerare förutsägelse av omsättning, begär och ökad försäljning, vanliga åtgärder för hantering av kundrelationer (CRM).

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Välj den **öppna** knappen exempel 5-experimentet.

    ![Öppna experimentet](media/ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>Data

Informationen som vi använder det här experimentet är från KDD Cup 2009. Datauppsättningen har 50 000 rader och kolumner för 230 funktionen. Uppgiften är att förutsäga kundomsättning, begär och ökad försäljning för kunder som använder dessa funktioner. Se den [KDD webbplats](https://www.kdd.org/kdd-cup/view/kdd-cup-2009) för mer information om data och uppgiften.

## <a name="experiment-summary"></a>Sammanfattning för experiment

Här är den klar experimentdiagram:

![Experimentdiagram](./media/ui-sample-classification-predict-churn/experiment-graph.png)

Först måste göra vi några enkla databearbetning.

- Rå datauppsättningen innehåller många värden som saknas. Vi använder den **Rensa Data som saknas** modulen att ersätta det saknade värden med 0.

    ![Rensa datauppsättningen](./media/ui-sample-classification-predict-churn/cleaned-dataset.png)

- Funktionerna och motsvarande omsättning, begär, och ökad försäljning etiketter finns i olika datauppsättningar. Vi använder den **Lägg till kolumner** modulen att lägga till etikettkolumner till kolumner för funktionen. Den första kolumnen **Kol1**, är etikettkolumnen. Resten av kolumn, **Var1**, **Var2**och så vidare är funktionen kolumner.
 
    ![Lägg till kolumn-datauppsättning](./media/ui-sample-classification-predict-churn/added-column1.png)

- Vi använder den **dela Data** modul för att dela upp datauppsättningen i träna och testa uppsättningar.


    Vi använder sedan den binär klassificeraren för beslutsträd med standardparametrarna för att skapa förutsägande modeller. Vi bygger en modell per aktivitet, det vill säga en modell varje att förutsäga ökad försäljning, begär och omsättning.

## <a name="results"></a>Resultat

Visualisera utdata från den **utvärdera modell** modul för att se modellen i test-grupp. För ökad försäljning-uppgiften visar ROC-kurvan att modellen har bättre än en slumpmässig modell. Området under kurvan (AUC) är 0.857. Precisionen är 0,7 med tröskelvärde 0,5 återkallelsen är 0.463 och F1-poängen är 0.545.

![Utvärdera resultaten](./media/ui-sample-classification-predict-churn/evaluate-result.png)

 Du kan flytta den **tröskelvärdet** skjutreglaget och se mått som ändrar för aktiviteten binär klassificering.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Utforska de andra exempel som är tillgängliga för det visuella gränssnittet:

- [Exempel 1 – Regression: Förutsäga priset på en bil](ui-sample-regression-predict-automobile-price-basic.md)
- [Exempel 2 – Regression: Jämför algoritmer för bil pris förutsägelse](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exempel 3 – klassificering: Förutsäga kreditrisk](ui-sample-classification-predict-credit-risk-basic.md)
- [Exempel 4 - klassificering: Förutsäga kreditrisken (kostnad känsliga)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
