---
title: Vad är en arbetsyta
titleSuffix: Azure Machine Learning service
description: Lär dig vilka en arbetsyta är och varför du behöver en för Azure Machine Learning-tjänsten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/21/2019
ms.openlocfilehash: 5785bf8f6538f1d91e7a23178e29487ebee14f29
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65989839"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Vad är en Azure Machine Learning-arbetsyta?

Arbetsytan är den översta resursen för Azure Machine Learning-tjänsten. Det ger en centraliserad plats för att arbeta med alla artefakter som du skapar när du använder Azure Machine Learning-tjänsten.

Arbetsytan sparar en historik över träningskörningar, inklusive loggar, mått, utdata och en ögonblicksbild av dina skript. Du kan använda den här informationen för att avgöra vilka kör utbildning ger den bästa modellen.  

När du har en modell som du vill kan registrera du den med arbetsytan. Du använder den registrerade modellen och bedömnings-skript för distribution till Azure Container Instances, Azure Kubernetes Service eller till en fält-programmable gate (FPGA) som en REST-baserade HTTP-slutpunkt. Du kan också distribuera modellen till en Azure IoT Edge-enhet som en modul.

## <a name="taxonomy"></a>Taxonomi 

En taxonomi för arbetsytan illustreras i följande diagram:

[![Arbetsytan taxonomi](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

Diagrammet visar följande komponenter i en arbetsyta:

+ En arbetsyta kan innehålla [Notebook VMs](quickstart-run-cloud-notebook.md), molnresurser som konfigurerats med Python-miljön krävs för att köra Azure Machine Learning.
+ [Användarroller](how-to-assign-roles.md) kan du dela din arbetsyta med andra användare, grupper eller projekt.
+ [Beräkningsmål](concept-azure-machine-learning-architecture.md#compute-target) används för att köra dina experiment.
+ När du skapar arbetsytan [associerade resurser](#resources) skapas också.
+ [Experiment](concept-azure-machine-learning-architecture.md#experiment) är träningskörningar som du använder för att skapa dina modeller.  Du kan skapa och köra experiment med
    + Den [Azure Machine Learning-SDK för Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
    + Den [automatiserad machine learning-experiment (förhandsversion)](how-to-create-portal-experiments.md) avsnitt i Azure-portalen.
    + Den [visuella gränssnittet (förhandsversion)](ui-concept-visual-interface.md).
+ [Pipelines](concept-azure-machine-learning-architecture.md#pipeline) är återanvändbara arbetsflöden för träning och träna din modell.
+ [Datauppsättningar](concept-azure-machine-learning-architecture.md#dataset) stöd i hantering av data som används för att skapa en modell utbildnings- och pipeline.
+ När du har en modell som du vill distribuera kan du skapa en [registrerade modellen](concept-azure-machine-learning-architecture.md#model-registry).
+ Använd den registrerade modellen och ett bedömningsskript för att skapa en [distribution](concept-azure-machine-learning-architecture.md#image-registry).

## <a name="tools-for-workspace-interaction"></a>Verktyg för arbetsytan interaktion

Du kan interagera med din arbetsyta på följande sätt:

+ På webben:
    + [Azure Portal](https://azure.portal.com)
    + Den [visuella gränssnittet (förhandsversion)](ui-concept-visual-interface.md)
+ Med hjälp av Azure Machine Learning i Python [SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ Från kommandoraden med hjälp av Azure Machine Learning [CLI-tillägg](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli)

## <a name="machine-learning-with-a-workspace"></a>Machine learning med en arbetsyta

Maskininlärning läsa och/eller skriva artefakter till din arbetsyta. 

+ Kör ett experiment för att träna en modell – experimentera skrivningar Körningsresultat till arbetsytan.
+ Använda automatisk ML för att träna en modell – utbildning resultatet skrivs till arbetsytan.
+ Registrera en modell i arbetsytan.
+ Distribuera en modell – använder den registrerade modellen för att skapa en distribution.
+ Skapa och köra återanvändbara arbetsflöden.
+ Visa machine learning-artefakter som experiment, pipelines, modeller, distributioner.
+ Spåra och övervaka modeller.

## <a name="workspace-management"></a>Arbetsytehantering

Du kan också utföra följande hanteringsaktiviteter för arbetsyta:

| Hanteringsaktivitet för arbetsyta   | Portal              | SDK        | CLI        |
|---------------------------|------------------|------------|------------|
| Skapa en arbetsyta        | **&check;**     | **&check;** | **&check;** |
| Skapa och hantera beräkningsresurser    | **&check;**   | **&check;** |  **&check;**   |
| Hantera åtkomst till arbetsytan    | **&check;**   | |  **&check;**    |
| Skapa en VM-anteckningsbok | **&check;**   | |     |

Kom igång med tjänsten genom att [skapar en arbetsyta](setup-create-workspace.md).

## <a name="resources"></a> Associerade resurser

När du skapar en ny arbetsyta skapas automatiskt flera Azure-resurser som används av arbetsytan:

+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/): Registrerar docker-behållare som du använder vid träning och när du distribuerar en modell. För att minimera kostnader, ACR är **lazy-loaded** tills distributionsavbildningar har skapats.
+ [Azure Storage-konto](https://azure.microsoft.com/services/storage/): Används som standard-datalager för arbetsytan.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): Butiker övervakningsinformation om dina modeller.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): Butiker hemligheter som används av compute mål och annan känslig information som behövs av arbetsytan.

> [!NOTE]
> Förutom att skapa nya versioner, kan du också använda befintliga Azure-tjänster.

## <a name="next-steps"></a>Nästa steg

Kom igång med Azure Machine Learning-tjänsten, se:

+ [Azure Machine Learning-tjänstöversikt](overview-what-is-azure-ml.md)
+ [Skapa en arbetsyta](setup-create-workspace.md)
+ [Hantera en arbetsyta](how-to-manage-workspace.md)
+ [Självstudie: Träna en modell](tutorial-train-models-with-aml.md)
