---
title: Vad är en arbetsyta
titleSuffix: Azure Machine Learning
description: Arbetsytan är den resurs på den högsta nivån för Azure Machine Learning. Den behåller en historik över alla utbildningskörningar, inklusive loggar, mått, utdata och en ögonblicksbild av skripten. Du använder den här informationen för att avgöra vilken träningskörning som ger den bästa modellen
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/27/2019
ms.openlocfilehash: 03bc49c24e3c2d32e97f3e5e03bd39da63428a6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505574"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Vad är en Azure Machine Learning-arbetsyta?

Arbetsytan är den resurs på den högsta nivån för Azure Machine Learning, vilket ger en centraliserad plats att arbeta med alla artefakter som du skapar när du använder Azure Machine Learning.  Arbetsytan behåller en historik över alla utbildningskörningar, inklusive loggar, mått, utdata och en ögonblicksbild av skripten. Du kan använda den här informationen för att avgöra vilken träningskörning som ger den bästa modellen.  

När du har en modell du gillar registrerar du den med arbetsytan. Du använder sedan den registrerade modellen och bedömningsskripten för att distribuera till Azure Container Instances, Azure Kubernetes Service eller till en fältprogrammerbar gate array (FPGA) som en REST-baserad HTTP-slutpunkt. Du kan också distribuera modellen till en Azure IoT Edge-enhet som en modul.

Priser och tillgängliga funktioner beror på om [Basic- eller Enterprise-utgåvan](overview-what-is-azure-ml.md#sku) har valts för arbetsytan. Du väljer utgåvan när du [skapar arbetsytan](#create-workspace).  Du kan också [uppgradera](#upgrade) från Basic till Enterprise edition.

## <a name="taxonomy"></a>Taxonomi 

En taxonomi för arbetsytan illustreras i följande diagram:

[![Arbetsytetaxonomi](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

Diagrammet visar följande komponenter på en arbetsyta:

+ En arbetsyta kan innehålla [Azure Machine Learning-beräkningsinstanser,](concept-compute-instance.md)molnresurser som konfigurerats med Python-miljön som krävs för att köra Azure Machine Learning.

+ [Med användarroller](how-to-assign-roles.md) kan du dela arbetsytan med andra användare, team eller projekt.
+ [Beräkningsmål](concept-azure-machine-learning-architecture.md#compute-targets) används för att köra dina experiment.
+ När du skapar arbetsytan skapas också [associerade resurser](#resources) åt dig.
+ [Experiment](concept-azure-machine-learning-architecture.md#experiments) är utbildning körs du använder för att bygga dina modeller.  
+ [Pipelines](concept-azure-machine-learning-architecture.md#ml-pipelines) är återanvändbara arbetsflöden för utbildning och omskolning av din modell.
+ [Datauppsättningar](concept-azure-machine-learning-architecture.md#datasets-and-datastores) stöd i hanteringen av de data som du använder för modellutbildning och skapande av pipeline.
+ När du har en modell som du vill distribuera skapar du en registrerad modell.
+ Använd den registrerade modellen och ett bedömningsskript för att skapa en [distributionsslutpunkt](concept-azure-machine-learning-architecture.md#endpoints).

## <a name="tools-for-workspace-interaction"></a>Verktyg för interaktion med arbetsyta

Du kan interagera med arbetsytan på följande sätt:

+ På webben:
    + [Azure Machine Learning-studio](https://ml.azure.com) 
    + [Azure Machine Learning designer (förhandsversion)](concept-designer.md) - Endast tillgängligt i Enterprise Edition-arbetsytor. [Enterprise edition](overview-what-is-azure-ml.md#sku)
+ I alla Python-miljöer med [Azure Machine Learning SDK för Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ I alla R-miljöer med [Azure Machine Learning SDK för R](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ På kommandoraden med azure machine learning [CLI-tillägget](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli)

## <a name="machine-learning-with-a-workspace"></a>Maskininlärning med en arbetsyta

Maskininlärningsuppgifter läser och/eller skriver artefakter till arbetsytan.

+ Kör ett experiment för att träna en modell - skriver experimentkörningsresultat till arbetsytan.
+ Använd automatiserad ML för att träna en modell - skriver träningsresultat till arbetsytan.
+ Registrera en modell på arbetsytan.
+ Distribuera en modell - använder den registrerade modellen för att skapa en distribution.
+ Skapa och kör återanvändbara arbetsflöden.
+ Visa maskininlärningsartefakter som experiment, pipelines, modeller, distributioner.
+ Spåra och övervaka modeller.

## <a name="workspace-management"></a>Hantering av arbetsyta

Du kan också utföra följande arbeteytehanteringsuppgifter:

| Uppgift om hantering av arbetsytor   | Portalen              | Studio | Python SDK / R SDK       | CLI        |
|---------------------------|---------|---------|------------|------------|
| Skapa en arbetsyta        | **&check;**     | | **&check;** | **&check;** |
| Hantera åtkomst till arbetsyta    | **&check;**   || |  **&check;**    |
| Uppgradera till Enterprise-utgåvan    | **&check;** | **&check;**  | |     |
| Skapa och hantera beräkningsresurser    | **&check;**   | **&check;** | **&check;** |  **&check;**   |
| Skapa en virtuell dator för bärbara datorer |   | **&check;** | |     |

> [!WARNING]
> Det går inte att flytta din Azure Machine Learning-arbetsyta till en annan prenumeration, eller flytta den ägande prenumerationen till en ny klientorganisation. Om du gör det kan det orsaka fel.

## <a name="create-a-workspace"></a><a name='create-workspace'></a>Skapa en arbetsyta

När du skapar en arbetsyta bestämmer du om du vill skapa den med [Basic eller Enterprise Edition](overview-what-is-azure-ml.md#sku). Utgåvan bestämmer vilka funktioner som är tillgängliga på arbetsytan. Enterprise Edition ger dig bland andra tillgång till [Azure Machine Learning-designern](concept-designer.md) och studioversionen av att skapa [automatiserade maskininlärningsexperiment](tutorial-first-experiment-automated-ml.md).  Mer information och prisinformation finns i [Azure Machine Learning-priser](https://azure.microsoft.com/pricing/details/machine-learning/).

Det finns flera sätt att skapa en arbetsyta:  

* Använd [Azure-portalen](how-to-manage-workspace.md) för ett peka-och-klicka-gränssnitt för att gå igenom varje steg.
* Använda [Azure Machine Learning SDK för Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) för att skapa en arbetsyta i farten från Python-skript eller Jupiter-anteckningsböcker
* Använd en [Azure Resource Manager-mall](how-to-create-workspace-template.md) eller [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) när du behöver automatisera eller anpassa skapandet med företagets säkerhetsstandarder.
* Om du arbetar i Visual Studio-kod använder du [tillägget VS-kod](tutorial-setup-vscode-extension.md).

> [!NOTE]
> Arbetsytans namn är skiftlägesokänsligt.

## <a name="upgrade-to-enterprise-edition"></a><a name="upgrade"></a>Uppgradera till Enterprise-utgåvan

Du kan [uppgradera arbetsytan från Basic till Enterprise-versionen](how-to-manage-workspace.md#upgrade) med Azure-portalen. Du kan inte nedgradera en enterprise edition-arbetsyta till en basic edition-arbetsyta. 

## <a name="associated-resources"></a><a name="resources"></a>Associerade resurser

När du skapar en ny arbetsyta skapas automatiskt flera Azure-resurser som används av arbetsytan:

+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/): Registrerar docker-behållare som du använder under träningen och när du distribuerar en modell. För att minimera kostnaderna **läses** ACR in tills distributionsavbildningar skapas.
+ [Azure Storage-konto:](https://azure.microsoft.com/services/storage/)Används som standarddatalager för arbetsytan.  Jupyter-anteckningsböcker som används med dina Azure Machine Learning-beräkningsinstanser lagras även här.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): Lagrar övervakningsinformation om dina modeller.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): Lagrar hemligheter som används av beräkningsmål och annan känslig information som behövs av arbetsytan.

> [!NOTE]
> Förutom att skapa nya versioner kan du också använda befintliga Azure-tjänster.

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång med Azure Machine Learning finns i:

+ [Översikt över Azure Machine Learning](overview-what-is-azure-ml.md)
+ [Skapa en arbetsyta](how-to-manage-workspace.md)
+ [Hantera en arbetsyta](how-to-manage-workspace.md)
+ [Självstudiekurs: Kom igång med att skapa ditt första ML-experiment med Python SDK](tutorial-1st-experiment-sdk-setup.md)
+ [Självstudiekurs: Kom igång med Azure Machine Learning med R SDK](tutorial-1st-r-experiment.md)
+ [Självstudiekurs: Skapa din första klassificeringsmodell med automatiserad maskininlärning](tutorial-first-experiment-automated-ml.md) (Endast tillgängligt i arbetsytor i [Företagsutgåvor)](overview-what-is-azure-ml.md#sku)
+ [Handledning: Förutsäga bil pris med designern](tutorial-designer-automobile-price-train-score.md) (Endast tillgängligt i [Enterprise edition](overview-what-is-azure-ml.md#sku) arbetsytor)
