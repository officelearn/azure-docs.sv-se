---
title: Visuellt gränssnitt
titleSuffix: Azure Machine Learning
description: Lär dig mer om termer, begrepp och arbets flöde som utgör det visuella gränssnittet (för hands version) för Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.date: 9/23/2019
ms.openlocfilehash: a23f123c6ffadaad4f830e1f4eab3820e6ef56f6
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692226"
---
# <a name="what-is-the-visual-interface-for-azure-machine-learning"></a>Vad är det visuella gränssnittet för Azure Machine Learning? 

Med Visual Interface (för hands versionen) för Azure Machine Learning kan du utveckla data, träna, testa, distribuera, hantera och spåra maskin inlärnings modeller utan att skriva kod.

Det krävs ingen programmering, du ansluter visuellt [data uppsättningar](#datasets) och [moduler](#module) visuellt för att skapa din modell.

Det visuella gränssnittet använder din Azure Machine Learning [arbets yta](concept-workspace.md) för att:

+ Skapa, redigera och kör [pipeliner](#pipeline) i arbets ytan.
+ Åtkomst till [data uppsättningar](#datasets).
+ Använd [beräknings resurserna](#compute) i arbets ytan för att köra pipelinen. 
+ Registrera [modeller](concept-azure-machine-learning-architecture.md#models).
+ [Publicera](#publish) pipelines som rest-slutpunkter.
+ [Distribuera](#deployment) modeller som pipeline-slutpunkter (för batch-härledning) eller slut punkter i real tid på beräknings resurser i arbets ytan.

![Översikt över det visuella gränssnittet](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>Arbetsflöde

Det visuella gränssnittet ger dig en interaktiv, visuell arbets yta för att snabbt bygga, testa och iterera i en modell. 

+ Du drar och släpper [data uppsättningar](#datasets) och [moduler](#module) till arbets ytan.
+ Anslut modulerna tillsammans för att skapa en [pipeline](#pipeline).
+ Kör pipelinen med hjälp av beräknings resursen i arbets ytan för Machine Learnings tjänsten.
+ Upprepa i modell designen genom att redigera pipelinen och köra den igen.
+ När du är klar konverterar du din **utbildnings pipeline** till en **härlednings pipeline**.
+ [Publicera](#publish) din pipeline som en REST-slutpunkt om du vill skicka den igen utan python-koden konstruerad.
+ [Distribuera](#deployment) härlednings pipelinen som en pipeline-slutpunkt eller slut punkt i real tid så att din modell kan användas av andra.

## <a name="pipeline"></a>Pipeline

Skapa en ML- [pipeline](concept-azure-machine-learning-architecture.md#ml-pipelines) från grunden eller Använd en befintlig exempel-pipeline som mall. Varje gången du kör en pipeline lagras artefakter i din arbets yta. Pipeline-körningar grupperas i [experiment](concept-azure-machine-learning-architecture.md#experiments).

En pipeline består av data uppsättningar och analys moduler som du ansluter tillsammans för att skapa en modell. Mer specifikt har en giltig pipeline följande egenskaper:

* Data uppsättningar kan endast anslutas till moduler.
* Moduler kan vara anslutna till antingen data uppsättningar eller andra moduler.
* Alla indataportar för moduler måste ha viss anslutning till data flödet.
* Alla obligatoriska parametrar för varje modul måste anges.


Information om hur du kommer igång med det visuella gränssnittet finns i [Självstudier: förutsäga Automobile-priset med det visuella gränssnittet](ui-tutorial-automobile-price-train-score.md).

## <a name="datasets"></a>Datauppsättningar

En Machine Learning-datauppsättning gör det enkelt att komma åt och arbeta med dina data. Ett antal exempel data uppsättningar ingår i det visuella gränssnittet så att du kan experimentera med. Du kan [Registrera](./how-to-create-register-datasets.md) fler data uppsättningar när du behöver dem.

## <a name="module"></a>Modul

En modul är en algoritm som du kan tillämpa på dina data. Det visuella gränssnittet har ett antal moduler som sträcker sig från data ingångs funktioner till inlärnings-, poängsättnings-och validerings processer.

En modul kan ha en uppsättning parametrar som du kan använda för att konfigurera modulens interna algoritmer. När du väljer en modul på arbets ytan visas modulens parametrar i fönstret Egenskaper till höger om arbets ytan. Du kan ändra parametrarna i det här fönstret för att finjustera din modell.

![Egenskaper för modul](media/ui-concept-visual-interface/properties.png)

För viss hjälp att navigera genom biblioteket med Machine Learning-algoritmer tillgängliga, se [algoritmen & modul referens översikt](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a>Beräknings resurser

Använd beräknings resurser från din arbets yta för att köra din pipeline och vara värd för dina distribuerade modeller som real tids slut punkter eller pipelines slut punkter (för batch-härledning). De beräknings mål som stöds är:

| Beräkningsmål | Utbildning | Distribution |
| ---- |:----:|:----:|
| Azure Machine Learning Compute | ✓ | |
| Azure Kubernetes Service | | ✓ |

Compute-målen är kopplade till din Machine Learning- [arbetsyta](concept-workspace.md). Du hanterar dina beräknings mål i din arbets yta i [Azure Portal](https://portal.azure.com) eller i [landnings sidan för din arbets yta (för hands version)](https://ml.azure.com).

## <a name="publish"></a>Publicera

När du har en pipeline klar kan du publicera den som en REST-slutpunkt. En [PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py) kan skickas utan den python-kod som konstruerade den.

Dessutom kan en PublishedPipeline användas för att skicka en pipeline igen med olika PipelineParameter-värden och indata.

## <a name="deployment"></a>Distribution

När din förutsägelse modell är klar kan du distribuera den som en pipeline-slutpunkt eller real tids slut punkt direkt från det visuella gränssnittet.

Pipeline-slutpunkten är en [PublishedPipeline, som du kan skicka en pipeline-körning med olika PipelineParameter-värden och indata för batch-härledning.

Real tids slut punkten tillhandahåller ett gränssnitt mellan ett program och din bedömnings modell. Ett externt program kan kommunicera med bedömnings modellen i real tid. Ett anrop till en slut punkt i real tid returnerar förutsägelse resultat till ett externt program. För att anropa en slut punkt för Real tid skickar du en API-nyckel som skapades när du distribuerade slut punkten. Slut punkten baseras på REST, ett populärt arkitektur val för webb program projekt.

Information om hur du distribuerar din modell finns i [Självstudier: Distribuera en maskin inlärnings modell med det visuella gränssnittet](ui-tutorial-automobile-price-deploy.md).

## <a name="next-steps"></a>Nästa steg

* Lär dig grunderna för förutsägelse analys och maskin inlärning med [Självstudier: förutsäga det mobila priset med det visuella gränssnittet](ui-tutorial-automobile-price-train-score.md)
* Använd ett av exemplen och ändra till Suite-behoven:

    * [Exempel 1 – regression: Förutsäg pris](how-to-ui-sample-regression-predict-automobile-price-basic.md)
    * [Exempel 2 – regression: förutsäga priser och jämför algoritmer](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
    * [Exempel 3 – klassificering: förutsägelse kredit risk](how-to-ui-sample-classification-predict-credit-risk-basic.md)
    * [Exempel 4 – klassificering: förutsägelse kredit risk (kostnads känsligt)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
    * [Exempel 5 – klassificering: förutsägelse omsättning, begär och försäljning](how-to-ui-sample-classification-predict-churn.md)
    * [Exempel 6 – klassificering: förutsäga flyg fördröjningar](how-to-ui-sample-classification-predict-flight-delay.md)

