---
title: 'Regression: Förutsäga pris'
titleSuffix: Azure Machine Learning service
description: Detta visuella gränssnittet exempelexperiment visar hur du skapar en regressionsmodell för att förutsäga priset på en bil. Processen omfattar utbildning, testning och utvärdering av modellen på Automobile price data (Raw) datauppsättningen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/02/2019
ms.openlocfilehash: fa9b9179cda767d69d08dcd357a03123bde901cb
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028897"
---
# <a name="sample-1---regression-predict-price"></a>Exempel 1 – Regression: Förutsäga pris

Detta visuella gränssnittet exempelexperiment visar hur du skapar en regressionsmodell för att förutsäga priset på en bil. Processen omfattar utbildning, testning och utvärdering av modellen med hjälp av den **Automobile price data (Raw)** datauppsättning.

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Välj den **öppna** knappen för experimentet exempel 1:

    ![Öppna experimentet](media/ui-sample-regression-predict-automobile-price-basic/open-sample1.png)

## <a name="related-sample"></a>Relaterade exemplet

[Exempel 2 – Regression: Bil pris förutsägelse (jämför algoritmer)](ui-sample-regression-predict-automobile-price-compare-algorithms.md) ger en mer komplicerad exempelexperimentet som löser samma problem som det här experimentet genom att använda två olika regressionsmodeller. Den visar hur du snabbt jämföra olika algoritmer. Kolla in om du letar efter ett mer avancerat exempel.

## <a name="experiment-summary"></a>Sammanfattning för experiment

Vi kan använda de här stegen för att skapa experimentet:

1. Hämta data.
1. Förbearbeta data.
1. Träna modellen.
1. Testa, utvärdera och jämför modeller.

Här är det fullständiga diagrammet över experimentet:

![Diagram över experimentet](media/ui-sample-regression-predict-automobile-price-basic/overall-graph.png)

## <a name="get-the-data"></a>Hämta data

I det här experimentet använder vi den **Automobile price data (Raw)** datauppsättningen, vilket är från UCI Machine Learning-databasen. Datauppsättningen innehåller 26 kolumner som innehåller information om bilar, inklusive märke, modell, pris, vehicle funktioner (t.ex. antalet cylindrar), MPG och en försäkring riskpoäng. Målet med det här experimentet är att förutsäga priset för bilen.

## <a name="pre-process-the-data"></a>Förbearbeta data

Förberedelseuppgifter viktigaste data inkluderar datarensning, integration, transformation, minskning, och discretization eller kvantifieringsfel. I det visuella gränssnittet hittar du moduler för att utföra dessa åtgärder och andra data förbearbetning uppgifter i den **Dataomvandling** i den vänstra panelen.

Vi använder den **Välj kolumner i datauppsättning** modul för att exkludera normalized-losses som har många värden som saknas. Sedan använder vi **Rensa Data som saknas** att ta bort de rader som innehåller värden som saknas. Detta hjälper dig för att skapa en ren uppsättning träningsdata.

![Förbearbetning av data](./media/ui-sample-regression-predict-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>Träna modellen
Machine learning problem variera. Vanliga inkluderar klassificering och klustring, regression och rekommenderare system, som kan kräva en annan algoritm. Ditt val av algoritmen beror ofta på kraven för användningsfallet. När du väljer en algoritm, måste du justera dess parametrar för att träna en modell för exaktare. Sedan måste du utvärdera alla modeller som är baserat på mått som noggrannhet, uppfattbarheten avsevärt och effektivitet.

Eftersom målet med det här experimentet är att förutsäga bilpriser och eftersom etikettkolumnen (pris) innehåller reella tal, en regressionsmodell är ett bra val. Överväger att antalet funktioner som är relativt liten (mindre än 100) och dessa funktioner inte är null-optimerade troligen beslut gränsen kommer att vara icke-linjära. Så vi använder **beslut skog Regression** för det här experimentet.

Vi använder den **dela Data** modul för att dela upp indata slumpmässigt så att innehåller 70% av den ursprungliga informationen för datauppsättning för träning och testning datauppsättningen innehåller 30% av den ursprungliga informationen.

## <a name="test-evaluate-and-compare"></a>Testa, utvärdera och jämföra

 Vi dela upp datauppsättningen och använda olika datauppsättningar för att träna och testa modellen för att göra flera mål för utvärderingen av modellen.

När modellen tränas, använder vi den **Poängmodell** och **utvärdera modell** moduler för att generera förutsagt resultat och utvärdera modellerna.

**Bedömningsmodell** genererar förutsägelser för test-datauppsättning med hjälp av den tränade modellen. Om du vill kontrollera resultatet, väljer du utdataporten för **Poängmodell** och välj sedan **visualisera**.

![Poäng resultat](./media/ui-sample-regression-predict-automobile-price-basic/score-result.png)

Vi skickar sedan poängen till den **utvärdera modell** modul för att generera utvärderingsmått. Om du vill kontrollera resultatet, väljer du utdataporten för den **utvärdera modell** och välj sedan **visualisera**.

![Utvärdera resultatet](./media/ui-sample-regression-predict-automobile-price-basic/evaluate-result.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Utforska de andra exempel som är tillgängliga för det visuella gränssnittet:

- [Exempel 2 – Regression: Jämför algoritmer för bil pris förutsägelse](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exempel 3 – klassificering: Förutsäga kreditrisk](ui-sample-classification-predict-credit-risk-basic.md)
- [Exempel 4 - klassificering: Förutsäga kreditrisken (kostnad känsliga)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exempel 5 - klassificering: Förutsäga kundomsättning](ui-sample-classification-predict-churn.md)
