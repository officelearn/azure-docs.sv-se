---
title: 'Exempel på visuella gränssnitt #3: Klassificering för att förutsäga kredit risk'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du skapar en klassificeraren för maskin inlärning utan att skriva en enda rad kod med hjälp av det visuella gränssnittet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 7565f94910d0e926682a72af42b02059fe7295ea
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990057"
---
# <a name="sample-3---classification-predict-credit-risk"></a>Exempel 3 – klassificering: Förutsäga kreditrisk

Lär dig hur du skapar en klassificeraren för maskin inlärning utan att skriva en enda rad kod med hjälp av det visuella gränssnittet. Det här exemplet använder ett **besluts träd i två klasser** för att förutsäga kredit risk (hög eller låg) baserat på kredit program information, till exempel kredit historik, ålder och antal kredit kort.

Eftersom vi försöker besvara frågan "vilket är en?" Detta kallas för ett klassificerings problem. Du kan dock använda samma grundläggande process för att ta itu med alla typer av maskin inlärnings problem, oavsett om det är regression, klassificering, klustring och så vidare.

Här är det färdiga diagrammet för det här experimentet:

![Diagram över experimentet](media/ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Välj knappen **Öppna** för exempel 3 experiment:

    ![Öppna experimentet](media/ui-sample-classification-predict-credit-risk-basic/open-sample3.png)

## <a name="related-sample"></a>Relaterat exempel

[Exempel 4 – klassificering: Kredit risk förutsägelse (kostnads känslig)](ui-sample-classification-predict-credit-risk-cost-sensitive.md) ger ett avancerat experiment som löser samma problem som det här experimentet. Det visar hur du utför en _kostnads känslig_ klassificering genom att använda en **köra python-skriptfil** och jämföra prestanda för två binära klassificerings algoritmer. Referera till den om du vill lära dig mer om hur du skapar klassificerings experiment.

## <a name="data"></a>Data

Vi använder det tyska kredit kortets data uppsättning från UC Irvine-lagringsplatsen.
Data uppsättningen innehåller 1 000-exempel med 20 funktioner och 1 etikett. Varje exempel representerar en person. Funktionerna omfattar numeriska och kategoriska-funktioner. På sidan [](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) för den här funktionen finns information om betydelsen av kategoriska-funktionerna. Den sista kolumnen är etiketten, som anger kredit risken och bara har två möjliga värden: hög kredit risk = 2 och låg kredit risk = 1.

## <a name="experiment-summary"></a>Experiment Sammanfattning

Vi följer de här stegen för att skapa experimentet:

1. Dra den tyska datauppsättnings modulen för data uppsättningen för tyska kredit kort till experimentets arbets yta.
1. Lägg till en **Edit metadata** -modul så att vi kan lägga till meningsfulla namn för varje kolumn.
1. Lägg till en modul för **delade data** för att skapa inlärnings-och test uppsättningar. Ange bråk delen av raderna i den första data uppsättningen för utdata till 0,7. Den här inställningen anger att 70% av data kommer att matas ut till den vänstra porten i modulen och resten till rätt port. Vi använder den vänstra data uppsättningen för utbildning och det som är bäst för testning.
1. Lägg till en modul för **besluts träd med två klasser** för att initiera en utökat besluts träds klassificerare.
1. Lägg till en modul för **träna modell** . Anslut klassificeraren från föregående steg till den vänstra Indataporten för **träna-modellen**. Lägg till inlärnings uppsättningen (den vänstra utdataporten för **delnings data**) till den högra Indataporten för **träna-modellen**. **Träna-modellen** kommer att träna klassificeraren.
1. Lägg till en **modell för Poäng modell** och Anslut modulen **träna modell** till den. Lägg sedan till test uppsättningen (den rätta porten för **delnings data**) i **Poäng modellen**. **Poäng modellen** kommer att göra förutsägelserna. Du kan välja dess utdataport för att se förutsägelserna och den positiva klassen sannolikhet.
1. Lägg till en **utvärdera modell** -modul och Anslut den returnerade data uppsättningen till den vänstra Indataporten. Om du vill se utvärderings resultaten väljer du utdataporten för modulen **utvärdera modell** och väljer **visualisera**.

Här är det fullständiga experiment diagrammet:

![Diagram över experimentet](media/ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="results"></a>Resultat

![Utvärdera resultaten](media/ui-sample-classification-predict-credit-risk-basic/evaluate-result.png)

I utvärderings resultatet kan du se att AUC för modellen är 0,776. Vid tröskelvärdet är 0,5 precisionen 0,621, återkallning är 0,456 och F1-poängen är 0,526.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Utforska de andra exempel som är tillgängliga för det visuella gränssnittet:

- [Exempel 1 – regression: Förutsäga ett bils pris](ui-sample-regression-predict-automobile-price-basic.md)
- [Exempel 2 – regression: Jämför algoritmer för bilpris förutsägelser för bilar](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exempel 4 – klassificering: Förutsägelse kredit risk (kostnads känsligt)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exempel 5 – klassificering: Förutsäg omsättning](ui-sample-classification-predict-churn.md)
- [Exempel 6 – klassificering: Förutsäg fördröjningar i flygning](ui-sample-classification-predict-flight-delay.md)
