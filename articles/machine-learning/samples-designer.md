---
title: Pipelines i exempel designern
titleSuffix: Azure Machine Learning
description: Använd exempel i Azure Machine Learning designer för att komma igång med dina pipelines i Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 03/29/2020
ms.openlocfilehash: f9a8b0a4c51024d91e517db2f6ae10a4dba62384
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80389349"
---
# <a name="designer-sample-pipelines"></a>Exempelpipelines i Designer

Använd de inbyggda exemplen i Azure Machine Learning designer för att snabbt komma igång med att skapa dina egna maskin inlärnings pipeliner. Azure Machine Learning designer [GitHub-lagringsplatsen](https://github.com/Azure/MachineLearningDesigner) innehåller detaljerad dokumentation som hjälper dig att förstå några vanliga scenarier för maskin inlärning.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://aka.ms/AMLFree).
* En Azure Machine Learning-arbetsyta med Enterprise SKU.


## <a name="how-to-use-sample-pipelines"></a>Använda exempel pipelines

Designern sparar en kopia av exempel pipelines till din Studio-arbetsyta. Du kan redigera pipelinen för att anpassa den efter dina behov och spara den som din egen. Använd dem som start punkt för att rivstart med dina projekt.

### <a name="open-a-sample-pipeline"></a>Öppna en exempel pipeline

1. Logga in på <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.Azure.com</a>och välj den arbets yta som du vill arbeta med.

1. Välj **Designer**.

1. Välj en exempel-pipeline under det **nya pipeline** -avsnittet.

    Välj **Visa fler exempel** för en fullständig lista över exempel.

### <a name="submit-a-pipeline-run"></a>Skicka en pipeline-körning

Om du vill köra en pipeline måste du först ställa in ett standard beräknings mål för att köra pipelinen på.

1. I fönstret **Inställningar** till höger om arbets ytan väljer du **Välj Compute Target (Välj Compute Target**).

1. I dialog rutan som visas väljer du ett befintligt beräknings mål eller skapar ett nytt. Välj **Spara**.

1. Välj **Skicka** överst på arbets ytan för att skicka en pipeline-körning.

Beroende på pipeline-och beräknings inställningarna i exemplet kan det ta en stund att slutföra körningen. Standard beräknings inställningarna har en minsta Node-storlek på 0, vilket innebär att Designer måste allokera resurser efter inaktivitet. Upprepade pipelines körningar tar mindre tid eftersom beräknings resurserna redan har allokerats. Dessutom använder designern cachelagrade resultat för varje modul för att ytterligare förbättra effektiviteten.


### <a name="review-the-results"></a>Granska resultaten

När pipelinen har körts klart kan du granska pipelinen och visa utdata för varje modul för att lära dig mer.

Använd följande steg för att visa utdata från modulen:

1. Välj en modul på arbets ytan.

1. I informations fönstret för moduler till höger om arbets ytan väljer du **utdata + loggar**. Välj ikonen ![](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) för diagrammets visuella ikon för att se resultatet av varje modul. 

Använd exemplen som start punkter för några av de vanligaste scenarierna för maskin inlärning.

## <a name="regression-samples"></a>Regressions exempel

Lär dig mer om de inbyggda Regressions exemplen.

| Exempelrubrik | Beskrivning | 
| --- | --- |
| [Exempel 1: regression – pris förutsägelse för bilar (grundläggande)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-basic.md) | Förutsäga bil priser med linjär regression. |
| [Exempel 2: regression – pris förutsägelse för bilar (avancerat)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-compare-algorithms.md) | Förutsäga bil priser med hjälp av besluts skog och utökat besluts träd regressorer. Jämför modeller för att hitta den bästa algoritmen.

## <a name="classification-samples"></a>Klassificerings exempel

Lär dig mer om de inbyggda klassificerings exemplen. Du kan lära dig mer om exemplen utan dokumentations länkar genom att öppna exemplen och Visa kommentarer i modulen i stället.

| Exempelrubrik | Beskrivning | 
| --- | --- |
| [Exempel 3: binära klassificering med funktions val – inkomst förutsägelse](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-predict-income.md) | Förutsäga inkomst som hög eller låg genom att använda ett besluts träd med två klasser. Använd Pearson-korrelation för att välja funktioner.
| [Exempel 4: binära klassificering med anpassat Python-skript – kredit risk förutsägelse](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-credit-risk-cost-sensitive.md) | Klassificera kredit program som hög eller låg risk. Använd modulen kör Python-skript för att vikta dina data.
| [Exempel 5: binära klassificering – kund Relations förutsägelse](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-churn.md) | Förutsäga kund omsättningen med hjälp av två klass förstärknings besluts träd. Använd SMOTE för att sampla förprioriterade data.
| [Exempel 7: text klassificering-Wikipedia SP 500-datauppsättning](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-text-classification.md) | Klassificera företags typer från Wikipedia-artiklar med multiklass logistik regression. |
| Exempel 12: klassificerings igenkänning av multiklasser – brev igenkänning | Skapa en ensemble med binära klassificerare för att klassificera skrivna bokstäver. |

## <a name="recommender-samples"></a>Rekommenderade exempel

Läs mer om de inbyggda rekommendations exemplen. Du kan lära dig mer om exemplen utan dokumentations länkar genom att öppna exemplen och Visa kommentarer i modulen i stället.

| Exempelrubrik | Beskrivning | 
| --- | --- |
| Exempel 10: rekommendation-film klassificering tweets | Bygg en Movie rekommenderar-motor från film titlar och klassificering. |

## <a name="utility-samples"></a>Verktygs exempel

Lär dig mer om de exempel som demonstrerar verktyg och funktioner för maskin inlärning. Du kan lära dig mer om exemplen utan dokumentations länkar genom att öppna exemplen och Visa kommentarer i modulen i stället.

| Exempelrubrik | Beskrivning | 
| --- | --- |
| [Exempel 6: Använd anpassat R-skript – flyg fördröjnings förutsägelse](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-flight-delay.md) |
| Exempel 8: kors validering för binär klassificering – vuxen inkomst förutsägelse | Använd kors validering för att bygga en binär klassificerare för vuxen inkomster.
| Exempel 9: permutations funktions prioritet | Använd permutation-funktionen för att beräkna prioritets resultat för test data uppsättningen. 
| Exempel 11: finjustera parametrar för binär klassificering – naturinkomst förutsägelse | Använd finjustera modellens egenskaper för att hitta optimala disponeringsparametrarna för att bygga en binär klassificerare. |

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Lär dig hur du skapar och distribuerar maskin inlärnings modeller med [Självstudier: förutsäga bil pris med designern](tutorial-designer-automobile-price-train-score.md)
