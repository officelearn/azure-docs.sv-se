---
title: Bygg ML-modeller med designer
titleSuffix: Azure Machine Learning
description: Lär dig mer om de termer, begrepp och arbetsflöden som utgör designern för Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.date: 11/12/2019
ms.openlocfilehash: 78a6e7fa8d030185f537136a3a2124d8bc59d808
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79037631"
---
# <a name="what-is-azure-machine-learning-designer-preview"></a>Vad är Azure Machine Learning Designer (förhandsversion)? 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Med Azure Machine Learning-designern kan du koppla [datauppsättningar](#datasets) och [moduler](#module) visuellt på en interaktiv arbetsyta för att skapa maskininlärningsmodeller. Mer information om hur du kommer igång med designern finns i [Självstudiekurs: Förutsäga bilpris med designern](tutorial-designer-automobile-price-train-score.md)

![Exempel på Azure Machine Learning-designer](./media/concept-designer/designer-drag-and-drop.gif)

Designern använder [arbetsytan](concept-workspace.md) Azure Machine Learning för att ordna delade resurser som:

+ [Pipelines](#pipeline)
+ [Datamängder](#datasets)
+ [Beräkna resurser](#compute)
+ [Registrerade modeller](concept-azure-machine-learning-architecture.md#models)
+ [Publicerade pipelines](#publish)
+ [Slutpunkter i realtid](#deploy)

## <a name="model-training-and-deployment"></a>Modellutbildning och distribution

Designern ger dig en visuell arbetsyta för att skapa, testa och distribuera maskininlärningsmodeller. Med designern kan du:

+ Dra och släpp [datauppsättningar](#datasets) och [moduler](#module) till arbetsytan.
+ Anslut modulerna tillsammans för att skapa ett [pipeline-utkast](#pipeline-draft).
+ Skicka en [pipeline-körning](#pipeline-run) med hjälp av beräkningsresurserna på arbetsytan Azure Machine Learning.
+ Konvertera dina **träningspipelledningar** till **inferenspipel.**
+ [Publicera](#publish) dina pipelines till en **REST-pipelineslutpunkt** för att skicka nya pipeline-körningar med olika parametrar och datauppsättningar.
    + Publicera en **träningspipeline** för att återanvända en enda pipeline för att träna flera modeller samtidigt som du ändrar parametrar och datauppsättningar.
    + Publicera en **pipeline för inferens för** att göra förutsägelser om nya data med hjälp av en tidigare tränad modell.
+ [Distribuera](#deploy) en **inferenpipeline i realtid** till en slutpunkt i realtid för att göra förutsägelser om nya data i realtid.

![Arbetsflödesdiagram för utbildning, batch inferens och inferens i realtid i designern](./media/concept-designer/designer-workflow-diagram.png)

## <a name="pipeline"></a>Pipeline

En [pipeline](concept-azure-machine-learning-architecture.md#ml-pipelines) består av datauppsättningar och analysmoduler som du ansluter tillsammans. Pipelines har många användningsområden: du kan göra en pipeline som tränar en enda modell, eller en som tränar flera modeller. Du kan skapa en pipeline som gör förutsägelser i realtid eller i batch, eller göra en pipeline som bara rensar data. Med pipelines kan du återanvända ditt arbete och organisera dina projekt.

### <a name="pipeline-draft"></a>Utkast till pipeline

När du redigerar en pipeline i designern sparas dina framsteg som ett **pipeline-utkast**. Du kan redigera ett pipeline-utkast när som helst genom att lägga till eller ta bort moduler, konfigurera beräkningsmål, skapa parametrar och så vidare.

En giltig rörledning har följande egenskaper:

* Datauppsättningar kan bara ansluta till moduler.
* Moduler kan bara ansluta till antingen datauppsättningar eller andra moduler.
* Alla indataportar för moduler måste ha någon anslutning till dataflödet.
* Alla nödvändiga parametrar för varje modul måste ställas in.

När du är redo att köra pipeline-utkastet skickar du en pipeline-körning.

### <a name="pipeline-run"></a>Pipeline-körning

Varje gång du kör en pipeline lagras konfigurationen av pipelinen och dess resultat på arbetsytan som en **pipeline-körning**. Du kan gå tillbaka till alla pipeline-körningar för att kontrollera den för felsökning eller granskning. **Klona** en pipeline-körning för att skapa ett nytt pipeline-utkast som du kan redigera.

Pipeline-körningar grupperas i experiment för att ordna [körningshistorik.](concept-azure-machine-learning-architecture.md#experiments) Du kan ställa in experimentet för varje pipeline-körning. 

## <a name="datasets"></a>Datauppsättningar

En maskininlärningsdatauppsättning gör det enkelt att komma åt och arbeta med dina data. Ett antal exempeldatauppsättningar ingår i designern som du kan experimentera med. Du kan [registrera](how-to-create-register-datasets.md) fler datauppsättningar när du behöver dem.

## <a name="module"></a>Modul

En modul är en algoritm som du kan tillämpa på dina data. Designern har ett antal moduler som sträcker sig från datainträngningsfunktioner till utbildnings-, poäng- och valideringsprocesser.

En modul kan ha en uppsättning parametrar som du kan använda för att konfigurera modulens interna algoritmer. När du väljer en modul på arbetsytan modulens visas modulens parametrar i fönstret Egenskaper på arbetsytans högra sida. Du kan ändra parametrarna i det här fönstret för att finjustera din modell. Du kan ange beräkningsresurser för enskilda moduler i designern. 

![Modulegenskaper](./media/concept-designer/properties.png)

Mer information om hur du navigerar genom biblioteket med tillgängliga maskininlärningsalgoritmer finns i [referensöversikten för Algoritm & modul](algorithm-module-reference/module-reference.md)

## <a name="compute-resources"></a><a name="compute"></a>Beräkna resurser

Använd beräkningsresurser från arbetsytan för att köra pipelinen och vara värd för dina distribuerade modeller som slutpunkter i realtid eller pipelineslutpunkter (för batchinferens). De beräkningsmål som stöds är:

| Beräkningsmål | Utbildning | Distribution |
| ---- |:----:|:----:|
| Azure Machine Learning-beräkning | ✓ | |
| Azure Kubernetes Service | | ✓ |

Beräkningsmål är kopplade till din [Azure Machine Learning-arbetsyta](concept-workspace.md). Du hanterar dina beräkningsmål på arbetsytan i [Azure Machine Learning Studio (klassiskt).](https://ml.azure.com)

## <a name="deploy"></a>Distribuera

Om du vill utföra inferencing i realtid måste du distribuera en pipeline som **slutpunkt**i realtid . Slutpunkten i realtid skapar ett gränssnitt mellan ett externt program och din bedömningsmodell. Ett anrop till en slutpunkt i realtid returnerar förutsägelseresultat till programmet i realtid. Om du vill ringa ett samtal till en slutpunkt i realtid skickar du API-nyckeln som skapades när du distribuerade slutpunkten. Slutpunkten är baserad på REST, ett populärt arkitekturval för webbprogrammeringsprojekt.

Slutpunkter i realtid måste distribueras till ett Azure Kubernetes-tjänstkluster.

Mer information om hur du distribuerar din modell finns i [Självstudiekurs: Distribuera en maskininlärningsmodell med designern](tutorial-designer-automobile-price-deploy.md).

## <a name="publish"></a>Publicera

Du kan också publicera en pipeline till en **pipeline-slutpunkt**. I likhet med en slutpunkt i realtid kan du med en pipeline-slutpunkt skicka nya pipeline-körningar från externa program med REST-anrop. Du kan dock inte skicka eller ta emot data i realtid med hjälp av en pipeline-slutpunkt.

Publicerade rörledningar är flexibla, de kan användas för att träna eller omskola modeller, [utföra batchinferencing,](how-to-run-batch-predictions-designer.md)bearbeta nya data och mycket mer. Du kan publicera flera pipelines till en enda pipeline-slutpunkt och ange vilken pipeline-version som ska köras.

En publicerad pipeline körs på de beräkningsresurser som du definierar i pipeline-utkastet för varje modul.

Designern skapar samma [PublishedPipeline-objekt](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py) som SDK.


## <a name="moving-from-the-visual-interface-to-the-designer"></a>Flytta från det visuella gränssnittet till designern

Det visuella gränssnittet (förhandsversionen) har uppdaterats och är nu Azure Machine Learning designer (förhandsversion). Designern har bakåtriktats för att använda en pipeline-baserad backend som helt integreras med de andra funktionerna i Azure Machine Learning. 

Som ett resultat av dessa uppdateringar har vissa begrepp och termer för det visuella gränssnittet ändrats eller bytt namn. Se tabellen nedan för de viktigaste konceptuella förändringarna. 

| Koncept i formgivaren | Tidigare i det visuella gränssnittet |
| ---- |:----:|
| Utkast till pipeline | Experiment |
| Slutpunkt i realtid | Webbtjänst |

### <a name="migrating-to-the-designer"></a>Migrera till designern

Du kan konvertera befintliga experiment med visuella gränssnitt och webbtjänster till pipelines och slutpunkter i realtid i designern. Så här migrerar du resurser för visuellt gränssnitt:

[!INCLUDE [migrate from the visual interface](../../includes/aml-vi-designer-migration.md)]


## <a name="next-steps"></a>Nästa steg

* Lär dig grunderna i prediktiv analys och maskininlärning med [handledning: Förutsäg bilpris med designern](tutorial-designer-automobile-price-train-score.md)
* Lär dig hur du ändrar befintliga [designerexempel](samples-designer.md) för att anpassa dem till dina behov.

