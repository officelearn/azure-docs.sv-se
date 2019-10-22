---
title: 'Exempel på visuella gränssnitt #3: klassificering till förutsägelse av kredit risk'
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar en klassificeraren för maskin inlärning utan att skriva en enda rad kod med hjälp av det visuella gränssnittet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 09/23/2019
ms.openlocfilehash: 4649303b8ee643130b8e254f01bfffbe8ad9eb2b
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693508"
---
# <a name="sample-3---classification-predict-credit-risk"></a>Exempel 3 – klassificering: förutsägelse kredit risk

Lär dig hur du skapar en klassificeraren för maskin inlärning utan att skriva en enda rad kod med hjälp av det visuella gränssnittet. Det här exemplet **utökar ett besluts träd i två klasser** för att förutsäga kredit risken (hög eller låg) baserat på kredit information om kredit, till exempel kredit historik, ålder och antal kredit kort.

Eftersom frågan besvarar "vilket är en?" Detta kallas för ett klassificerings problem. Du kan dock använda samma grundläggande process för att ta itu med alla typer av maskin inlärnings problem, oavsett om det är regression, klassificering, klustring och så vidare.

Här är den slutliga pipeline-grafen för det här exemplet:

![Diagram över pipelinen](media/how-to-ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="prerequisites"></a>Krav

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Välj knappen **Öppna** för exempel 3 pipelinen:

    ![Öppna pipelinen](media/how-to-ui-sample-classification-predict-credit-risk-basic/open-sample3.png)

## <a name="related-sample"></a>Relaterat exempel

[Exempel 4 – klassificering: kredit risk förutsägelse (kostnads känslig)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md) tillhandahåller en avancerad pipeline som löser samma problem som det här exemplet. Det visar hur du utför en *kostnads känslig* klassificering genom att använda en **köra python-skriptfil** och jämföra prestanda för två binära klassificerings algoritmer. Referera till den om du vill lära dig mer om hur du skapar klassificerings pipeliner.


## <a name="data"></a>Data

Exemplet använder det tyska kredit kortets data uppsättning från UC Irvine-lagringsplatsen. Den innehåller 1 000 exempel med 20 funktioner och 1 etikett. Varje exempel representerar en person. Funktionerna omfattar numeriska och kategoriska-funktioner. På sidan [för](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) den här funktionen finns information om betydelsen av kategoriska-funktionerna. Den sista kolumnen är etiketten, som anger kredit risken och bara har två möjliga värden: hög kredit risk = 2 och låg kredit risk = 1.

## <a name="pipeline-summary"></a>Sammanfattning av pipeline

Följ de här stegen för att skapa pipelinen:

1. Dra den tyska datauppsättnings modulen för den tyska kredit kortet till pipeline-arbetsytan.
1. Lägg till en **Edit metadata** -modul så att vi kan lägga till meningsfulla namn för varje kolumn.
1. Lägg till en modul för **delade data** för att skapa inlärnings-och test uppsättningar. Ange bråk delen av raderna i den första data uppsättningen för utdata till 0,7. Den här inställningen anger att 70% av data kommer att matas ut till den vänstra porten i modulen och resten till rätt port. Vi använder den vänstra data uppsättningen för utbildning och det som är bäst för testning.
1. Lägg till en modul för **besluts träd med två klasser** för att initiera en utökat besluts träds klassificerare.
1. Lägg till en modul för **träna modell** . Anslut klassificeraren från föregående steg till den vänstra Indataporten för **träna-modellen**. Lägg till inlärnings uppsättningen (den vänstra utdataporten för **delnings data**) till den högra Indataporten för **träna-modellen**. **Träna-modellen** kommer att träna klassificeraren.
1. Lägg till en **modell för Poäng modell** och Anslut modulen **träna modell** till den. Lägg sedan till test uppsättningen (den rätta porten för **delnings data**) i **Poäng modellen**. **Poäng modellen** kommer att göra förutsägelserna. Du kan välja dess utdataport för att se förutsägelserna och den positiva klassen sannolikhet.
1. Lägg till en **utvärdera modell** -modul och Anslut den returnerade data uppsättningen till den vänstra Indataporten. Om du vill se utvärderings resultaten väljer du utdataporten för modulen **utvärdera modell** och väljer **visualisera**.

## <a name="results"></a>Resultat

![Utvärdera resultaten](media/how-to-ui-sample-classification-predict-credit-risk-basic/evaluate-result.png)

I utvärderings resultatet kan du se att AUC för modellen är 0,776. Vid tröskelvärdet är 0,5 precisionen 0,621, återkallning är 0,456 och F1-poängen är 0,526.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Utforska de andra exempel som är tillgängliga för det visuella gränssnittet:

- [Exempel 1 – regression: förutsäga ett bils pris](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [Exempel 2-regression: jämför algoritmer för bil förutsägelse av bilar](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exempel 4 – klassificering: förutsägelse kredit risk (kostnads känsligt)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exempel 5 – klassificering: förutsägelse omsättning](how-to-ui-sample-classification-predict-churn.md)
- [Exempel 6 – klassificering: förutsäga flyg fördröjningar](how-to-ui-sample-classification-predict-flight-delay.md)
- [Exempel 7 – text klassificering: bok granskningar](how-to-ui-sample-text-classification.md)