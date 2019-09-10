---
title: Vad är en arbets yta?
titleSuffix: Azure Machine Learning service
description: Arbets ytan är den översta resursen för Azure Machine Learning tjänst. Den innehåller en historik över alla utbildningar som körs, inklusive loggar, statistik, utdata och en ögonblicks bild av dina skript. Du kan använda den här informationen för att avgöra vilken utbildning som ska användas för att skapa den bästa modellen
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 08/06/2019
ms.openlocfilehash: ef060c083109a7096b62ebb35f2b49bb37a371b8
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860512"
---
# <a name="what-is-an-azure-machine-learning-service-workspace"></a>Vad är en Azure Machine Learning service-arbetsyta?

Arbets ytan är den översta resursen för Azure Machine Learning tjänst, vilket ger en central plats för att arbeta med alla artefakter som du skapar när du använder Azure Machine Learning-tjänsten.  Arbets ytan har en historik över alla utbildningar som körs, inklusive loggar, mått, utdata och en ögonblicks bild av dina skript. Du kan använda den här informationen för att avgöra vilken utbildning som ska användas för att skapa den bästa modellen.  

När du har en modell som du gillar kan du registrera den med arbets ytan. Sedan använder du de registrerade modell-och bedömnings skripten för att distribuera till Azure Container Instances, Azure Kubernetes-tjänsten eller till en FPGA (Field-programmerbar grind array) som en REST-baserad HTTP-slutpunkt. Du kan också distribuera modellen till en Azure IoT Edge enhet som en modul.

## <a name="taxonomy"></a>Taxonomi 

En taxonomi i arbets ytan illustreras i följande diagram:

[![Arbetsytan taxonomi](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

Diagrammet visar följande komponenter för en arbets yta:

+ En arbets yta kan innehålla [virtuella notebook-datorer](tutorial-1st-experiment-sdk-setup.md), moln resurser som kon figurer ATS med python-miljön som krävs för att köra Azure Machine Learning.
+ Med [användar roller](how-to-assign-roles.md) kan du dela din arbets yta med andra användare, team eller projekt.
+ [Beräknings mål](concept-azure-machine-learning-architecture.md#compute-targets) används för att köra experimenten.
+ När du skapar arbets ytan skapas även [tillhör ande resurser](#resources) .
+ [Experiment](concept-azure-machine-learning-architecture.md#experiments) är utbildningar som körs och som du kan använda för att bygga modeller.  Du kan skapa och köra experiment med
    + [Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
    + Avsnittet [automatiserade Machine Learning-experiment (för hands version)](how-to-create-portal-experiments.md) i Azure Portal eller din landnings sida för arbets ytan (för hands version).
    + [Visual Interface (för hands version)](ui-concept-visual-interface.md).
+ [Pipelines](concept-azure-machine-learning-architecture.md#ml-pipelines) är återanvändbara arbets flöden för utbildning och omträning av din modell.
+ Data [uppsättnings](concept-azure-machine-learning-architecture.md#datasets-and-datastores) stöd vid hantering av de data du använder för modell utbildning och skapande av pipelines.
+ När du har en modell som du vill distribuera skapar du en registrerad modell.
+ Använd den registrerade modellen och ett bedömnings skript för att skapa en [distribution](concept-azure-machine-learning-architecture.md#deployment).

## <a name="tools-for-workspace-interaction"></a>Verktyg för arbets ytans interaktion

Du kan interagera med din arbets yta på följande sätt:

+ På webben:
    + [Azure Portal](https://portal.azure.com)
    + [Landnings sidan för din arbets yta (för hands version)](https://ml.azure.com)
    + [Visual Interface (för hands version)](ui-concept-visual-interface.md)
+ I python med hjälp av Azure Machine Learning [SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ På kommando raden med hjälp av Azure Machine Learning [CLI-tillägget](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli)

## <a name="machine-learning-with-a-workspace"></a>Machine Learning med en arbets yta

Machine Learning-uppgifter läser och/eller skriver artefakter till din arbets yta. 

+ Kör ett experiment för att träna en modell – skriver experiment körnings resultat till arbets ytan.
+ Använd automatisk ML för att träna en modell – skriver utbildnings resultat till arbets ytan.
+ Registrera en modell i arbets ytan.
+ Distribuera en modell – använder den registrerade modellen för att skapa en distribution.
+ Skapa och kör återanvändbara arbets flöden.
+ Visa maskin inlärnings artefakter som experiment, pipelines, modeller, distributioner.
+ Spåra och övervaka modeller.




## <a name="workspace-management"></a>Hantering av arbets yta

Du kan också utföra följande hanterings uppgifter för arbets ytan:

| Arbets ytans hanterings aktivitet   | Portalen              | SDK        | CLI        |
|---------------------------|------------------|------------|------------|
| Skapa en arbetsyta        | **&check;**     | **&check;** | **&check;** |
| Skapa och hantera beräknings resurser    | **&check;**   | **&check;** |  **&check;**   |
| Hantera åtkomst till arbets ytan    | **&check;**   | |  **&check;**    |
| Skapa en virtuell dator för Notebook | **&check;**   | |     |

### <a name='create-workspace'></a>Skapa en arbets yta

Det finns flera sätt att skapa en arbets yta.

* Använd [Azure Portal](how-to-manage-workspace.md) för ett punkt-och-klick-gränssnitt för att vägleda dig genom varje steg.
* Använd [Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) för att skapa en arbets yta i farten från Python-skript eller Jupiter-anteckningsböcker
* Använd en [Azure Resource Manager mall](how-to-create-workspace-template.md) eller [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) när du behöver automatisera eller anpassa skapandet med företags säkerhets standarder.
* Använd [vs Code-tillägget](how-to-vscode-tools.md#get-started-with-azure-machine-learning)om du arbetar i Visual Studio Code.

## <a name="resources"></a>Associerade resurser

När du skapar en ny arbetsyta skapas automatiskt flera Azure-resurser som används av arbetsytan:

+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/): Registrerar Docker-behållare som du använder under utbildningen och när du distribuerar en modell. För att minimera kostnaderna är ACR en **Lazy-inläst** tills distributions avbildningar skapas.
+ [Azure Storage konto](https://azure.microsoft.com/services/storage/): Används som standard data lager för arbets ytan.  Jupyter-anteckningsböcker som används med dina virtuella dator datorer lagras också här.
+ [Azure Application insikter](https://azure.microsoft.com/services/application-insights/): Lagrar övervaknings information om dina modeller.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): Lagrar hemligheter som används av beräknings mål och annan känslig information som behövs för arbets ytan.

> [!NOTE]
> Förutom att skapa nya versioner kan du också använda befintliga Azure-tjänster.

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång med Azure Machine Learning-tjänsten finns i:

+ [Översikt över Azure Machine Learning tjänsten](overview-what-is-azure-ml.md)
+ [Skapa en arbetsyta](how-to-manage-workspace.md)
+ [Hantera en arbetsyta](how-to-manage-workspace.md)
+ [Självstudier: Träna en modell](tutorial-train-models-with-aml.md)
