---
title: Exempel på designerpipeldrar
titleSuffix: Azure Machine Learning
description: Använd exempel i Azure Machine Learning-designern för att få fart på dina pipelines för maskininlärning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 03/29/2020
ms.openlocfilehash: f9a8b0a4c51024d91e517db2f6ae10a4dba62384
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389349"
---
# <a name="designer-sample-pipelines"></a>Provrörledningar för designer

Använd de inbyggda exemplen i Azure Machine Learning designer för att snabbt komma igång med att bygga egna pipelines för maskininlärning. Azure Machine Learning designer [GitHub-databasen](https://github.com/Azure/MachineLearningDesigner) innehåller detaljerad dokumentation som hjälper dig att förstå några vanliga machine learning-scenarier.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://aka.ms/AMLFree).
* En Azure Machine Learning-arbetsyta med Enterprise SKU.


## <a name="how-to-use-sample-pipelines"></a>Så här använder du exempelpipelar

Designern sparar en kopia av exempelpipelorna på studioarbetsytan. Du kan redigera pipelinen för att anpassa den till dina behov och spara den som din egen. Använd dem som utgångspunkt för att få fart på projekten.

### <a name="open-a-sample-pipeline"></a>Öppna en exempelpipeline

1. Logga in <a href="https://ml.azure.com?tabs=jre" target="_blank">på ml.azure.com</a>och välj den arbetsyta som du vill arbeta med.

1. Välj **Designer**.

1. Välj en exempelpipeline under avsnittet **Ny pipeline.**

    Välj **Visa fler exempel** för en fullständig lista med exempel.

### <a name="submit-a-pipeline-run"></a>Skicka en pipeline-körning

Om du vill köra en pipeline måste du först ange standardberäkningsmål för att köra pipelinen på.

1. Välj **beräkningsmål** i fönstret Inställningar till **Select compute target**höger om arbetsytan .

1. Välj ett befintligt beräkningsmål i dialogrutan som visas eller skapa ett nytt. Välj **Spara**.

1. Välj **Skicka** högst upp på arbetsytan om du vill skicka en pipeline-körning.

Beroende på exempelpipelinen och beräkningsinställningarna kan det ta lite tid att slutföra körningarna. Standardberäkningsinställningarna har en minsta nodstorlek på 0, vilket innebär att designern måste allokera resurser efter inaktiv. Upprepade pipeline-körningar tar mindre tid eftersom beräkningsresurserna redan har allokerats. Dessutom använder designern cachelagrade resultat för varje modul för att ytterligare förbättra effektiviteten.


### <a name="review-the-results"></a>Granska resultaten

När pipelinen har körts kan du granska pipelinen och visa utdata för varje modul för att lära dig mer.

Gör så här för att visa modulutdata:

1. Välj en modul på arbetsytan.

1. I rutan modulinformation till höger om arbetsytan väljer du **Utdata + loggar**. Välj ikonen ![visualisera](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) diagramikonen för att se resultatet för varje modul. 

Använd exemplen som utgångspunkter för några av de vanligaste maskininlärningsscenarierna.

## <a name="regression-samples"></a>Regressionsprover

Läs mer om de inbyggda regressionsexemplen.

| Exempelrubrik | Beskrivning | 
| --- | --- |
| [Exempel 1: Regression - Automobile Pris Förutsägelse (Basic)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-basic.md) | Förutsäg bilpriser med linjär regression. |
| [Exempel 2: Regression - Automobile Pris Förutsägelse (Avancerat)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-compare-algorithms.md) | Förutsäga bilpriserna med hjälp av beslut skog och ökat beslut träd regressors. Jämför modeller för att hitta den bästa algoritmen.

## <a name="classification-samples"></a>Klassificeringsprover

Läs mer om de inbyggda klassificeringsexempelna. Du kan läsa mer om exemplen utan dokumentationslänkar genom att öppna exemplen och visa modulkommentarerna i stället.

| Exempelrubrik | Beskrivning | 
| --- | --- |
| [Exempel 3: Binär klassificering med funktionsval - Inkomstprediktion](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-predict-income.md) | Förutsäg inkomst som hög eller låg, med hjälp av en två-klass ökat beslutsträd. Använd Pearson-korrelation för att välja funktioner.
| [Exempel 4: Binär klassificering med anpassat Python-skript - Kreditriskförutsägels](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-credit-risk-cost-sensitive.md) | Klassificera kreditansökningar som hög eller låg risk. Använd modulen Kör Python Script för att väga dina data.
| [Exempel 5: Binär klassificering - Förutsägelse av kundrelationer](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-churn.md) | Förutse kundomsättning med hjälp av tvåklassförstärkt beslutsträd. Använd SMOTE för att prova partiska data.
| [Exempel 7: Textklassificering – Wikipedia SP 500 Dataset](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-text-classification.md) | Klassificera företagstyper från Wikipedia artiklar med multiclass logistisk regression. |
| Exempel 12: Klassificering av fleraklasser - Brevigenkänning | Skapa en ensemble av binära klassificerare för att klassificera skrivna bokstäver. |

## <a name="recommender-samples"></a>Rekommendera prover

Läs mer om de inbyggda rekommendationsexemplen. Du kan läsa mer om exemplen utan dokumentationslänkar genom att öppna exemplen och visa modulkommentarerna i stället.

| Exempelrubrik | Beskrivning | 
| --- | --- |
| Exempel 10: Rekommendation - Film Betyg Tweets | Bygg en filmrekomnaremotor från filmtitlar och betyg. |

## <a name="utility-samples"></a>Verktygsprover

Läs mer om de exempel som demonstrerar verktyg och funktioner för maskininlärning. Du kan läsa mer om exemplen utan dokumentationslänkar genom att öppna exemplen och visa modulkommentarerna i stället.

| Exempelrubrik | Beskrivning | 
| --- | --- |
| [Exempel 6: Använd anpassat R-skript - Förutsägelse om flygförsening](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-flight-delay.md) |
| Exempel 8: Korsvalidering för binär klassificering - Vuxen inkomst förutsägelse | Använd korsvalidering för att skapa en binär klassificerare för vuxeninkomst.
| Exempel 9: Permutationsfunktionsbetydelse | Använd permutationsfunktionens betydelse för att beräkna prioritetspoäng för testdatauppsättningen. 
| Exempel 11: Justera parametrar för binär klassificering - vuxen inkomst förutsägelse | Använd Tune Model Hyperparametrar för att hitta optimala hyperparametrar för att bygga en binär klassificerare. |

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Lär dig hur du bygger och distribuerar maskininlärningsmodeller med [självstudiekurs: Förutsäg bilpriset med designern](tutorial-designer-automobile-price-train-score.md)
