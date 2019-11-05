---
title: Bygg ML-modeller med designer
titleSuffix: Azure Machine Learning
description: Lär dig mer om termer, begrepp och arbets flöde som utgör designern för Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.openlocfilehash: ee97322e58fe7ab3a1474f55c6294822b8ce90da
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517870"
---
# <a name="what-is-azure-machine-learning-designer-preview"></a>Vad är Azure Machine Learning designer (för hands version)? 
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Med designern för Azure Machine Learning kan du utveckla data, träna, testa, distribuera, hantera och spåra maskin inlärnings modeller utan att skriva kod.

Det krävs ingen programmering, du ansluter visuellt [data uppsättningar](#datasets) och [moduler](#module) visuellt för att skapa din modell.

Designern använder din Azure Machine Learning [arbets yta](concept-workspace.md) för att:

+ Skapa, redigera och kör [pipeliner](#pipeline) i arbets ytan.
+ Åtkomst till [data uppsättningar](#datasets).
+ Använd [beräknings resurserna](#compute) i arbets ytan för att köra pipelinen. 
+ Registrera [modeller](concept-azure-machine-learning-architecture.md#models).
+ [Publicera](#publish) pipelines som rest-slutpunkter.
+ [Distribuera](#deployment) modeller som pipeline-slutpunkter (för batch-härledning) eller slut punkter i real tid på beräknings resurser i arbets ytan.

![Översikt över designern](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>Arbetsflöde

Designern ger dig en interaktiv, visuell arbets yta för att snabbt bygga, testa och iterera på en modell. 

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


Information om hur du kommer igång med design verktyget finns i [Självstudier: förutsäga bil priser med designern](tutorial-designer-automobile-price-train-score.md).

## <a name="datasets"></a>Datauppsättningar

En Machine Learning-datauppsättning gör det enkelt att komma åt och arbeta med dina data. Ett antal exempel data uppsättningar ingår i designern så att du kan experimentera med. Du kan [Registrera](./how-to-create-register-datasets.md) fler data uppsättningar när du behöver dem.

## <a name="module"></a>Modul

En modul är en algoritm som du kan tillämpa på dina data. Designern har ett antal moduler som sträcker sig från data ingångs funktioner till inlärnings-, poängsättnings-och validerings processer.

En modul kan ha en uppsättning parametrar som du kan använda för att konfigurera modulens interna algoritmer. När du väljer en modul på arbets ytan visas modulens parametrar i fönstret Egenskaper till höger om arbets ytan. Du kan ändra parametrarna i det här fönstret för att finjustera din modell.

![Egenskaper för modul](media/ui-concept-visual-interface/properties.png)

För viss hjälp att navigera genom biblioteket med Machine Learning-algoritmer tillgängliga, se [algoritmen & modul referens översikt](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a>Beräknings resurser

Använd beräknings resurser från din arbets yta för att köra din pipeline och vara värd för dina distribuerade modeller som real tids slut punkter eller pipelines slut punkter (för batch-härledning). De beräknings mål som stöds är:

| Beräkningsmål | Utbildning | Distribution |
| ---- |:----:|:----:|
| Azure Machine Learning Compute | ✓ | |
| Azure Kubernetes Service | | ✓ |

Compute-målen är kopplade till din Machine Learning- [arbetsyta](concept-workspace.md). Du hanterar dina beräknings mål i din arbets yta i [Azure Machine Learning Studio](https://ml.azure.com).

## <a name="publish"></a>Publicera

När du har en pipeline klar kan du publicera den som en REST-slutpunkt. En [PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py) kan skickas utan den python-kod som konstruerade den.

Dessutom kan en PublishedPipeline användas för att skicka en pipeline igen med olika PipelineParameter-värden och indata.

## <a name="deployment"></a>Distribution

När din förutsägelse modell är klar kan du distribuera den som en pipeline-slutpunkt eller real tids slut punkt direkt från designern.

Pipeline-slutpunkten är en PublishedPipeline, som du kan skicka en pipeline-körning med olika PipelineParameter-värden och indata för batch-härledning.

Real tids slut punkten tillhandahåller ett gränssnitt mellan ett program och din bedömnings modell. Ett externt program kan kommunicera med bedömnings modellen i real tid. Ett anrop till en slut punkt i real tid returnerar förutsägelse resultat till ett externt program. För att anropa en slut punkt för Real tid skickar du en API-nyckel som skapades när du distribuerade slut punkten. Slut punkten baseras på REST, ett populärt arkitektur val för webb program projekt.

Information om hur du distribuerar din modell finns i [Självstudier: Distribuera en maskin inlärnings modell med designern](tutorial-designer-automobile-price-deploy.md).

## <a name="moving-from-the-visual-interface-to-the-designer"></a>Flytta från det visuella gränssnittet till designern

Visual Interface (för hands version) har uppdaterats och är nu Azure Machine Learning designer (för hands version). Designern har återskapats för att använda en pipeline-baserad server del som helt kan integreras med de andra funktionerna i Azure Machine Learning. 

Som ett resultat av dessa uppdateringar har vissa begrepp och villkor för det visuella gränssnittet ändrats eller bytt namn. Se tabellen nedan för de viktigaste koncept ändringarna. 

| Koncept i designern | Tidigare i det visuella gränssnittet |
| ---- |:----:|
| Pipeline-utkast | Olika |
| Slut punkt i real tid | Webbtjänst |

### <a name="migrating-to-the-designer"></a>Migrera till designern

Du kan konvertera befintliga experiment och webb tjänster för visuella gränssnitt till pipelines och real tids slut punkter i designern. Använd följande steg för att migrera dina Visual Interface-till gångar:

[!INCLUDE [migrate from the visual interface](../../../includes/aml-vi-designer-migration.md)]


## <a name="next-steps"></a>Nästa steg

* Lär dig grunderna för förutsägelse analys och maskin inlärning med [Självstudier: förutsäga bil pris med designern](tutorial-designer-automobile-price-train-score.md)
* Använd ett av exemplen och ändra till Suite-behoven:

- [Exempel 1 – regression: förutsäga ett bils pris](how-to-designer-sample-regression-automobile-price-basic.md)
- [Exempel 2-regression: jämför algoritmer för bil förutsägelse av bilar](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Exempel 3 – klassificering med funktions val: inkomst förutsägelse](how-to-designer-sample-classification-predict-income.md)
- [Exempel 4 – klassificering: förutsägelse kredit risk (kostnads känsligt)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Exempel 5 – klassificering: förutsägelse omsättning](how-to-designer-sample-classification-churn.md)
- [Exempel 6 – klassificering: förutsäga flyg fördröjningar](how-to-designer-sample-classification-flight-delay.md)
- [Exempel 7 – text klassificering: Wikipedia SP 500-datauppsättning](how-to-designer-sample-text-classification.md)

