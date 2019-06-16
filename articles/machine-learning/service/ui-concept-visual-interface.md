---
title: Visuellt gränssnitt
titleSuffix: Azure Machine Learning service
description: Lär dig om villkor, begrepp och arbetsflöde som tillsammans bildar det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/15/2019
ms.openlocfilehash: be07e0f3438ea93312d4eb440e7e63b8f98e11b8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077380"
---
# <a name="what-is-the-visual-interface-for-azure-machine-learning-service"></a>Vad är det visuella gränssnittet för Azure Machine Learning-tjänsten? 

Det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten kan du Förbered data, träna, testa, distribuera, hantera och spåra machine learning-modeller utan att skriva kod.

Det finns ingen programmering krävs, du visuellt ansluta [datauppsättningar](#dataset) och [moduler](#module) att konstruera din modell.

Det visuella gränssnittet använder Azure Machine Learning-tjänsten [arbetsytan](concept-workspace.md) till:

+ Skriva artefakter i [experimentera](#experiment) körs till arbetsytan.
+ Åtkomst [datauppsättningar](#dataset).
+ Använd den [beräkningsresurser](#compute) i arbetsytan för att köra experimentet. 
+ Registrera [modeller](concept-azure-machine-learning-architecture.md#models).
+ [Distribuera](#deployment) modeller som webbtjänster på beräkningsresurser i arbetsytan.

![Översikt över det visuella gränssnittet](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>Arbetsflöde

Det visuella gränssnittet ger dig en interaktiv, visuell arbetsyta för att snabbt bygga, testa och iterera på en modell. 

+ Du drar och släpper [moduler](#module) till arbetsytan.
+ Koppla ihop moduler i formulär en [experimentera](#experiment).
+ Kör experimentet med hjälp av beräkningsresursen i arbetsytan Machine Learning-tjänsten.
+ Iterera din Modelldesign genom att redigera experimentet och köra det igen.
+ När du är klar kan du konvertera dina **träningsexperiment** till en **förutsägelseexperiment**.
+ [Distribuera](#deployment) förutsägelseexperiment som en webbsida tjänsten så att din modell kan användas av andra.

## <a name="experiment"></a>Experiment

Skapa ett experiment från grunden eller använda ett befintligt exempelexperiment som mall.  Varje gång du kör ett experiment lagras artefakter i din arbetsyta.

Ett experiment består av datauppsättningar och analytiska moduler som du kopplar ihop för att skapa en modell. Mer konkret uttryckt så har ett giltigt experiment följande egenskaper:

* Datauppsättningar kan bara anslutas till moduler.
* Moduler kan anslutas till datauppsättningar eller andra moduler.
* Alla indataportar för moduler måste ha någon typ av koppling till dataflödet.
* Alla nödvändiga parametrar för varje modul måste anges.

Ett exempel på ett enkelt experiment finns i [snabbstarten: Förbereda och visualisera data utan att skriva kod i Azure Machine Learning](ui-quickstart-run-experiment.md).

En fullständig genomgång av en lösning för förutsägelseanalys finns i [självstudien: Förutsäga bil pris med det visuella gränssnittet](ui-tutorial-automobile-price-train-score.md).

## <a name="dataset"></a>Datauppsättning

En datauppsättning är data som har överförts till det visuella gränssnittet ska användas i modelleringsprocessen. Ett antal provdatauppsättningar ingår att experimentera med och du kan föra över fler datauppsättningar efter behov.

## <a name="module"></a>Modul

En modul är en algoritm som du kan tillämpa på dina data. Det visuella gränssnittet har ett antal moduler, med allt från dataåtkomstfunktioner till utbildning, bedömning och processer för verifiering.

En modul kan ha en uppsättning parametrar som du kan använda för att konfigurera modulens interna algoritmer. När du väljer en modul på arbetsytan visas modulens parametrar i fönstret egenskaper till höger om arbetsytan. Du kan ändra parametrarna i det här fönstret för att finjustera din modell.

![Modulen egenskaper](media/ui-concept-visual-interface/properties.png)

Vissa hjälp med att navigera genom bibliotek med maskininlärningsalgoritmer som är tillgängliga finns i [algoritm- och modulreferens referera till översikt](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a> Beräkningsresurser

Använd beräkningsresurser från din arbetsyta för att köra dina experiment eller värden dina distribuerade modeller som webbtjänster. Beräkningsmål som stöds är:


| Beräkningsmål | Utbildning | Distribution |
| ---- |:----:|:----:|
| Azure Machine Learning-beräkning | ✓ | |
| Azure Kubernetes Service | | ✓ |

Compute mål är kopplade till din Machine Learning [arbetsytan](concept-workspace.md). Du hanterar din beräkningsmål i din arbetsyta i den [Azure-portalen](https://portal.azure.com).

## <a name="deployment"></a>Distribution

När din prediktiva analysmodell är färdig kan distribuera du den som en webbtjänst direkt från det visuella gränssnittet.

Webbtjänsterna är ett gränssnitt mellan ett program och dina bedömningsmodell. Ett externt program kan kommunicera med en arbetsflödesbaserad poängmodell i realtid. Ett anrop till en webbtjänst returnerar förutsägelser till ett externt program. Om du vill göra ett anrop till en webbtjänst skickar du en API-nyckel som skapades när du distribuerade webbtjänsten. Webbtjänsten baseras på REST, ett populärt arkitekturval för programmeringsprojekt.

Läs hur du distribuerar din modell i [självstudien: Distribuera en modell för maskininlärning med det visuella gränssnittet](ui-tutorial-automobile-price-deploy.md).

## <a name="next-steps"></a>Nästa steg

* Lär dig grunderna i förutsägelseanalyser och maskininlärning med [snabbstarten: Förbereda och visualisera data utan att skriva kod i Azure Machine Learning](ui-quickstart-run-experiment.md).
* Använd någon av exempel och ändra till suite dina behov:
    * [Exempel 1 – Regression: Förutsäga pris](ui-sample-regression-predict-automobile-price-basic.md)
    * [Exempel 2 – Regression: Förutsäga pris och jämföra algoritmer](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
    * [Exempel 3 – klassificering: Förutsäga kreditrisk](ui-sample-classification-predict-credit-risk-basic.md)
    * [Exempel 4 - klassificering: Förutsäga kreditrisken (kostnad känsliga)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
    * [Exempel 5 - klassificering: Förutsäga kundomsättning, begär och ökad försäljning](ui-sample-classification-predict-churn.md)
