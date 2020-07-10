---
title: Bygg ML-modeller med designer (för hands version)
titleSuffix: Azure Machine Learning
description: Lär dig mer om termer, begrepp och arbets flöde som utgör designern för Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.date: 06/28/2020
ms.custom: designer
ms.openlocfilehash: a2074b1ecd37fd18a9a0ac9091f05e734765e40b
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86199981"
---
# <a name="what-is-azure-machine-learning-designer-preview"></a>Vad är Azure Machine Learning Designer (förhandsversion)? 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Med Azure Machine Learning Designer kan du visuellt ansluta [data uppsättningar](#datasets) och [moduler](#module) på en interaktiv arbets yta för att skapa maskin inlärnings modeller. Information om hur du kommer igång med design verktyget finns i [Självstudier: förutsäga Automobile-priset med designern](tutorial-designer-automobile-price-train-score.md)

![Exempel på Azure Machine Learning designer](./media/concept-designer/designer-drag-and-drop.gif)

Designern använder din Azure Machine Learning- [arbetsyta](concept-workspace.md) för att organisera delade resurser, till exempel:

+ [Pipelines](#pipeline)
+ [Datauppsättningar](#datasets)
+ [Beräknings resurser](#compute)
+ [Registrerade modeller](concept-azure-machine-learning-architecture.md#models)
+ [Publicerade pipelines](#publish)
+ [Real tids slut punkter](#deploy)

## <a name="model-training-and-deployment"></a>Modell utbildning och distribution

Designern ger dig en visuell arbets yta för att bygga, testa och distribuera maskin inlärnings modeller. Med designern kan du:

+ Dra och släpp [data uppsättningar](#datasets) och [moduler](#module) till arbets ytan.
+ Anslut modulerna för att skapa ett [pipeline-utkast](#pipeline-draft).
+ Skicka en [pipeline-körning](#pipeline-run) med beräknings resurserna i Azure Machine Learning-arbetsytan.
+ Omvandla dina **utbildnings pipeliner** till **härlednings pipeliner**.
+ [Publicera](#publish) pipelines till en rest- **slutpunkt** för att skicka en ny pipeline som körs med olika parametrar och data uppsättningar.
    + Publicera en **utbildnings pipeline** för att återanvända en enda pipeline för att träna flera modeller när du ändrar parametrar och data uppsättningar.
    + Publicera en **pipeline för batch-härledning** för att göra förutsägelser för nya data med hjälp av en tidigare tränad modell.
+ [Distribuera](#deploy) en **härlednings pipeline** i real tid till en slut punkt i real tid för att göra förutsägelser om nya data i real tid.

![Arbets flödes diagram för utbildning, batch-härledning och real tids härledning i designern](./media/concept-designer/designer-workflow-diagram.png)

## <a name="pipeline"></a>Pipeline

En [pipeline](concept-azure-machine-learning-architecture.md#ml-pipelines) består av data uppsättningar och analys moduler som du ansluter till. Pipelines har många användnings områden: du kan skapa en pipeline som tågen en enskild modell eller en som har flera modeller. Du kan skapa en pipeline som gör förutsägelser i real tid eller i batch eller göra en pipeline som bara rensar data. Med pipelines kan du återanvända ditt arbete och organisera dina projekt.

### <a name="pipeline-draft"></a>Pipeline-utkast

När du redigerar en pipeline i designern sparas förloppet som ett **pipeline-utkast**. Du kan redigera ett pipeline-utkast när som helst genom att lägga till eller ta bort moduler, konfigurera beräknings mål, skapa parametrar och så vidare.

En giltig pipeline har följande egenskaper:

* Data uppsättningar kan bara ansluta till moduler.
* Moduler kan bara ansluta till antingen data uppsättningar eller andra moduler.
* Alla indataportar för moduler måste ha viss anslutning till data flödet.
* Alla obligatoriska parametrar för varje modul måste anges.

När du är redo att köra ditt pipeline-utkast skickar du en pipeline-körning.

### <a name="pipeline-run"></a>Pipeline-körning

Varje gången du kör en pipeline lagras konfigurationen av pipelinen och dess resultat i arbets ytan som en **pipeline-körning**. Du kan gå tillbaka till alla pipeline-körningar för att kontrol lera fel söknings-eller gransknings syfte. **Klona** en pipeline-körning för att skapa ett nytt pipeline-utkast som du kan redigera.

Pipeline-körningar grupperas i [experiment](concept-azure-machine-learning-architecture.md#experiments) för att organisera körnings historik. Du kan ställa in experimentet för varje pipeline-körning. 

## <a name="datasets"></a>Datauppsättningar

En Machine Learning-datauppsättning gör det enkelt att komma åt och arbeta med dina data. Det finns flera exempel data uppsättningar i designern som du kan experimentera med. Du kan [Registrera](how-to-create-register-datasets.md) fler data uppsättningar när du behöver dem.

## <a name="module"></a>Modul

En modul är en algoritm som du kan tillämpa på dina data. Designern har flera moduler som sträcker sig från data ingångs funktioner till inlärnings-, poängsättnings-och validerings processer.

En modul kan ha en uppsättning parametrar som du kan använda för att konfigurera modulens interna algoritmer. När du väljer en modul på arbetsytan modulens visas modulens parametrar i fönstret Egenskaper på arbetsytans högra sida. Du kan ändra parametrarna i det här fönstret för att finjustera din modell. Du kan ställa in beräknings resurser för enskilda moduler i designern. 

:::image type="content" source="./media/concept-designer/properties.png"alt-text="Egenskaper för modul":::


För viss hjälp att navigera genom biblioteket med Machine Learning-algoritmer tillgängliga, se [algoritmen & module Reference Overview](algorithm-module-reference/module-reference.md). Information om hur du väljer en algoritm finns i [lathund-bladet Azure Machine Learning algorithm](algorithm-cheat-sheet.md).

## <a name="compute-resources"></a><a name="compute"></a>Beräknings resurser

Använd beräknings resurser från din arbets yta för att köra din pipeline och vara värd för dina distribuerade modeller som real tids slut punkter eller pipelines slut punkter (för batch-härledning). De beräknings mål som stöds är:

| Beräkningsmål | Utbildning | Distribution |
| ---- |:----:|:----:|
| Azure Machine Learning Compute | ✓ | |
| Azure Machine Learning beräknings instans | ✓ | |
| Azure Kubernetes Service | | ✓ |

Compute-målen är kopplade till din [Azure Machine Learning-arbetsyta](concept-workspace.md). Du hanterar dina beräknings mål i din arbets yta i [Azure Machine Learning Studio](https://ml.azure.com).

## <a name="deploy"></a>Distribuera

För att utföra inferencing i real tid måste du distribuera en pipeline som en **slut punkt i real tid**. Slut punkten för Real tid skapar ett gränssnitt mellan ett externt program och din bedömnings modell. Ett anrop till en slut punkt i real tid returnerar förutsägelse resultat till programmet i real tid. För att anropa en slut punkt för Real tid skickar du den API-nyckel som skapades när du distribuerade slut punkten. Slut punkten baseras på REST, ett populärt arkitektur val för webb program projekt.

Real tids slut punkter måste distribueras till ett Azure Kubernetes service-kluster.

Information om hur du distribuerar din modell finns i [Självstudier: Distribuera en maskin inlärnings modell med designern](tutorial-designer-automobile-price-deploy.md).

## <a name="publish"></a>Publicera

Du kan också publicera en pipeline till en **pipeline-slutpunkt**. På samma sätt som en slut punkt i real tid kan du skicka nya pipeline-körningar från externa program med hjälp av REST-anrop för en pipeline-slutpunkt. Du kan dock inte skicka eller ta emot data i real tid med hjälp av en pipeline-slutpunkt.

Publicerade pipelines är flexibla, de kan användas för att träna eller träna modeller, [utföra batch-inferencing](how-to-run-batch-predictions-designer.md), bearbeta nya data och mycket mer. Du kan publicera flera pipelines till en enda pipeline-slutpunkt och ange vilken pipeline-version som ska köras.

En publicerad pipeline körs på de beräknings resurser som du definierar i pipeline-utkastet för varje modul.

Designern skapar samma [PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py) -objekt som SDK: n.


## <a name="moving-from-the-visual-interface-to-the-designer"></a>Flytta från det visuella gränssnittet till designern

Visual Interface (för hands version) har uppdaterats och är nu Azure Machine Learning designer (för hands version). Designern har återskapats för att använda en pipeline-baserad server del som helt kan integreras med de andra funktionerna i Azure Machine Learning. 

Som ett resultat av dessa uppdateringar har vissa begrepp och villkor för det visuella gränssnittet ändrats eller bytt namn. Se tabellen nedan för de viktigaste koncept ändringarna. 

| Koncept i designern | Tidigare i det visuella gränssnittet |
| ---- |:----:|
| Pipeline-utkast | Experiment |
| Slut punkt i real tid | Webbtjänst |

### <a name="migrating-to-the-designer"></a>Migrera till designern

Du kan konvertera befintliga experiment och webb tjänster för visuella gränssnitt till pipelines och real tids slut punkter i designern. Använd följande steg för att migrera dina Visual Interface-till gångar:

[!INCLUDE [migrate from the visual interface](../../includes/aml-vi-designer-migration.md)]


## <a name="next-steps"></a>Nästa steg

* Lär dig grunderna för förutsägelse analys och maskin inlärning med [Självstudier: förutsäga bil pris med designern](tutorial-designer-automobile-price-train-score.md)
* Lär dig hur du ändrar befintliga [Designer-exempel](samples-designer.md) för att anpassa dem efter dina behov.
