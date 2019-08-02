---
title: Visuellt gränssnitt
titleSuffix: Azure Machine Learning service
description: Lär dig mer om termer, begrepp och arbets flöde som utgör det visuella gränssnittet (för hands version) för Azure Machine Learning tjänst.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/15/2019
ms.openlocfilehash: 1cb56386f52d1c7b1ec357b912c648c1961b4c1d
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68677990"
---
# <a name="what-is-the-visual-interface-for-azure-machine-learning-service"></a>Vad är det visuella gränssnittet för Azure Machine Learning-tjänsten? 

Med Visual Interface (för hands versionen) för Azure Machine Learning service kan du utveckla data, träna, testa, distribuera, hantera och spåra maskin inlärnings modeller utan att skriva kod.

Det krävs ingen programmering, du ansluter visuellt [data uppsättningar](#dataset) och [moduler](#module) visuellt för att skapa din modell.

Det visuella gränssnittet använder din Azure Machine Learning tjänst [arbets yta](concept-workspace.md) för att:

+ Skriv artefakter för [experiment](#experiment) körningar till arbets ytan.
+ Åtkomst till [data uppsättningar](#dataset).
+ Använd [beräknings resurserna](#compute) på arbets ytan för att köra experimentet. 
+ Registrera [modeller](concept-azure-machine-learning-architecture.md#models).
+ [Distribuera](#deployment) modeller som webb tjänster för beräknings resurser i arbets ytan.

![Översikt över det visuella gränssnittet](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>Arbetsflöde

Det visuella gränssnittet ger dig en interaktiv, visuell arbets yta för att snabbt bygga, testa och iterera i en modell. 

+ Du drar och släpper [moduler](#module) till arbets ytan.
+ Koppla ihop modulerna för att skapa ett [experiment](#experiment).
+ Kör experimentet med beräknings resursen i arbets ytan Machine Learnings tjänst.
+ Upprepa i modell designen genom att redigera experimentet och köra det igen.
+ När du är klar kan du konvertera ditt **utbildnings experiment** till ett **förutsägelse experiment**.
+ [Distribuera](#deployment) det förutsägande experimentet som en webb tjänst så att din modell kan användas av andra.

## <a name="experiment"></a>Experiment

Skapa ett experiment från grunden eller Använd ett befintligt exempel experiment som mall.  Varje gången du kör ett experiment lagras artefakter i din arbets yta.

Ett experiment består av data uppsättningar och analys moduler som du ansluter tillsammans för att skapa en modell. Mer konkret uttryckt så har ett giltigt experiment följande egenskaper:

* Data uppsättningar kan endast anslutas till moduler.
* Moduler kan vara anslutna till antingen data uppsättningar eller andra moduler.
* Alla indataportar för moduler måste ha viss anslutning till data flödet.
* Alla obligatoriska parametrar för varje modul måste anges.


Information om hur du kommer igång med det visuella gränssnittet finns i [Självstudier: Förutsäg bil priset med det visuella gränssnittet](ui-tutorial-automobile-price-train-score.md).

## <a name="dataset"></a>Datauppsättning

En data uppsättning är data som har överförts till det visuella gränssnitt som ska användas i modellerings processen. Ett antal exempel data uppsättningar ingår som du kan experimentera med och du kan ladda upp fler data uppsättningar när du behöver dem.

## <a name="module"></a>Modul

En modul är en algoritm som du kan tillämpa på dina data. Det visuella gränssnittet har ett antal moduler som sträcker sig från data ingångs funktioner till inlärnings-, poängsättnings-och validerings processer.

En modul kan ha en uppsättning parametrar som du kan använda för att konfigurera modulens interna algoritmer. När du väljer en modul på arbets ytan visas modulens parametrar i fönstret Egenskaper till höger om arbets ytan. Du kan ändra parametrarna i det här fönstret för att finjustera din modell.

![Egenskaper för modul](media/ui-concept-visual-interface/properties.png)

För viss hjälp att navigera genom biblioteket med Machine Learning-algoritmer tillgängliga, se [algoritmen & modul referens översikt](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a>Beräknings resurser

Använd beräknings resurser från din arbets yta för att köra experimentet eller vara värd för dina distribuerade modeller som webb tjänster. Beräkningsmål som stöds är:


| Beräkningsmål | Utbildning | Distribution |
| ---- |:----:|:----:|
| Azure Machine Learning-beräkning | ✓ | |
| Azure Kubernetes Service | | ✓ |

Compute-målen är kopplade till din Machine Learning- [arbetsyta](concept-workspace.md). Du hanterar dina beräknings mål i din arbets yta i [Azure Portal](https://portal.azure.com).

## <a name="deployment"></a>Distribution

När förutsägelse analys modellen är klar distribuerar du den som en webb tjänst direkt från det visuella gränssnittet.

Webb tjänsterna tillhandahåller ett gränssnitt mellan ett program och din bedömnings modell. Ett externt program kan kommunicera med bedömnings modellen i real tid. Ett anrop till en webb tjänst returnerar förutsägelse resultat till ett externt program. Om du vill göra ett anrop till en webbtjänst skickar du en API-nyckel som skapades när du distribuerade webbtjänsten. Webb tjänsten baseras på REST, ett populärt arkitektur val för webb program projekt.

Information om hur du distribuerar din modell finns i [Självstudier: Distribuera en maskin inlärnings modell med det visuella](ui-tutorial-automobile-price-deploy.md)gränssnittet.

## <a name="next-steps"></a>Nästa steg

* Lär dig grunderna för förutsägelse analys och maskin inlärning med [Självstudier: Förutsäg bil pris med Visual Interface](ui-tutorial-automobile-price-train-score.md)
* Använd ett av exemplen och ändra till Suite-behoven:
    * [Exempel 1 – regression: Förutsägelse pris](ui-sample-regression-predict-automobile-price-basic.md)
    * [Exempel 2 – regression: Förutsäg priser och jämför algoritmer](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
    * [Exempel 3 – klassificering: Förutsägelse kredit risk](ui-sample-classification-predict-credit-risk-basic.md)
    * [Exempel 4 – klassificering: Förutsägelse kredit risk (kostnads känsligt)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
    * [Exempel 5 – klassificering: Förutsäga omsättning, begär och försäljning](ui-sample-classification-predict-churn.md)
