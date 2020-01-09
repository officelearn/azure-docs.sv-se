---
title: 'Design: klassificera, förutse inkomst exempel'
titleSuffix: Azure Machine Learning
description: Följ det här exemplet för att bygga en icke-kodad klassificerare för att förutsäga intäkter med Azure Machine Learning designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: f65c93ac06da6512255a3380d5484c6285798098
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660007"
---
# <a name="build-a-classifier--use-feature-selection-to-predict-income-with-azure-machine-learning-designer"></a>Bygg en klassificerare & använda funktions val för att förutse intäkter med Azure Machine Learning designer

**Designer (för hands version), exempel 3**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Lär dig hur du skapar en dator inlärnings klassificerare utan att skriva en enda rad kod med hjälp av designern (för hands version). Det här exemplet tränar ett **besluts träd i två klasser** för att förutsäga den vuxen skatte inkomsten (> = 50 000 eller < = 50 000).

Eftersom frågan besvarar "vilket är en?" Detta kallas för ett klassificerings problem. Du kan dock använda samma grundläggande process för att ta itu med alla typer av Machine Learning-problem – regression, klassificering, klustring och så vidare.

Här är den slutliga pipeline-grafen för det här exemplet:

![Diagram över pipelinen](./media/how-to-designer-sample-classification-predict-income/overall-graph.png)

## <a name="prerequisites"></a>Krav

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Öppna det genom att klicka på exemplet 3.



## <a name="data"></a>Data

Data uppsättningen innehåller 14 funktioner och en etikett kolumn. Det finns flera typer av funktioner, inklusive numeriska och kategoriska. Följande diagram visar ett utdrag från data uppsättningen: ![data](media/how-to-designer-sample-classification-predict-income/sample3-dataset-1225.png)



## <a name="pipeline-summary"></a>Sammanfattning av pipeline

Följ de här stegen för att skapa pipelinen:

1. Dra den binära indatamängds-modulen för total mängds inkomst till pipeline-arbetsytan.
1. Lägg till en modul för **delade data** för att skapa inlärnings-och test uppsättningar. Ange bråk delen av raderna i den första data uppsättningen för utdata till 0,7. Den här inställningen anger att 70% av data kommer att matas ut till den vänstra porten i modulen och resten till rätt port. Vi använder den vänstra data uppsättningen för utbildning och det som är bäst för testning.
1. Lägg till **funktionen filtrera baserat funktions val** för att välja 5 funktioner efter PearsonCorreclation. 
1. Lägg till en modul för **besluts träd med två klasser** för att initiera en utökat besluts träds klassificerare.
1. Lägg till en modul för **träna modell** . Anslut klassificeraren från föregående steg till den vänstra Indataporten för **träna-modellen**. Anslut den filtrerade data uppsättningen från filter baserat funktions val modul som utbildnings data uppsättning.  **Träna-modellen** kommer att träna klassificeraren.
1. Lägg till omvandling av urvals kolumner och Använd omvandling för att tillämpa samma omvandling (filtrerad beroende funktions val) för att testa data uppsättningen.
![Apply-Transformation](./media/how-to-designer-sample-classification-predict-income/transformation.png)
1. Lägg till modulen för **Poäng modell** och Anslut modulen **träna modell** till den. Lägg sedan till test uppsättningen (utdata från modulen Använd omvandling som använder funktions urvalet som test uppsättning för) i **Poäng modellen**. **Poäng modellen** kommer att göra förutsägelserna. Du kan välja dess utdataport för att se förutsägelserna och den positiva klassen sannolikhet.


    Den här pipelinen har två score-moduler, den som finns i kolumnen till höger har uteslutit etikett-kolumnen innan den gör förutsägelsen. Detta är för berett för att distribuera en slut punkt i real tid, eftersom webb tjänstens indatatyper endast förväntar sig att funktioner inte är märkta. 

1. Lägg till en **utvärdera modell** -modul och Anslut den returnerade data uppsättningen till den vänstra Indataporten. Om du vill se utvärderings resultaten väljer du utdataporten för modulen **utvärdera modell** och väljer **visualisera**.

## <a name="results"></a>Resultat

![Utvärdera resultatet](media/how-to-designer-sample-classification-predict-income/sample3-evaluate-1225.png)

I utvärderings resultatet kan du se att kurvor som ROC, precisions åter kallelse och förvirring. 

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Utforska de andra exempel som är tillgängliga för designern:

- [Exempel 1 – regression: förutsäga ett bils pris](how-to-designer-sample-regression-automobile-price-basic.md)
- [Exempel 2-regression: jämför algoritmer för bil förutsägelse av bilar](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Exempel 4 – klassificering: förutsägelse kredit risk (kostnads känsligt)](service/how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Exempel 5 – klassificering: förutsägelse omsättning](service/how-to-designer-sample-classification-churn.md)
- [Exempel 6 – klassificering: förutsäga flyg fördröjningar](service/how-to-designer-sample-classification-flight-delay.md)
- [Exempel 7 – text klassificering: Wikipedia SP 500-datauppsättning](how-to-designer-sample-text-classification.md)
