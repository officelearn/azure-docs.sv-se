---
title: Exempel på Jupyter Notebooks
titleSuffix: Azure Machine Learning service
description: Hitta och använda exempel Jupyter-anteckningsböcker för att utforska Python SDK för Azure Machine Learning-tjänsten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 05/29/2019
ms.custom: seodec18
ms.openlocfilehash: ea4d5a807c25ea0406b49dac8a83ef1a34e0e8b3
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391802"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Använd Jupyter-anteckningsböcker för att utforska Azure Machine Learning-tjänsten

Den [lagringsplatsen för Azure Machine Learning anteckningsböcker](https://github.com/azure/machinelearningnotebooks) innehåller de senaste Azure Machine Learning Python SDK-exempel. Dessa Juypter datorer har utformats för att utforska SDK: N och fungera som modeller för din egen machine learning-projekt.

Den här artikeln visar hur du kommer åt databasen från följande miljöer:

- [Azure Machine Learning-anteckningsbok VM](#azure-machine-learning-notebook-vm)
- [Ta med din egen notebook-server](#bring-your-own-jupyter-notebook-server)
- [Virtuell dator för datavetenskap](#data-science-virtual-machine)
- [Azure Notebooks](#azure-notebooks)

> [!NOTE]
> När du har klonat lagringsplatsen, hittar du självstudien anteckningsböcker i den **självstudier** mappen och funktionsspecifika anteckningsböcker i den **How-to-till-användning – azureml** mapp.

## <a name="azure-machine-learning-notebook-vm"></a>Azure Machine Learning-anteckningsbok VM

Det enklaste sättet att komma igång med exemplen är att slutföra den [molnbaserade notebook Snabbstart](quickstart-run-cloud-notebook.md). När klar har du en dedikerad notebook-server med SDK och exempellagringsplatsen. Inga nedladdningar eller installeras.

## <a name="bring-your-own-jupyter-notebook-server"></a>Ta med din egen Jupyter Notebook-server

Följ dessa steg om du vill ta med din egen notebook-server för lokal utveckling:

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

De här anvisningarna installera de grundläggande SDK-paket krävs för Snabbstart och självstudier anteckningsböcker. Andra exempelanteckningsböcker som kan kräva att du kan installera ytterligare komponenter. Mer information finns i [installera Azure Machine Learning-SDK för Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

## <a name="data-science-virtual-machine"></a>Virtuell dator för datavetenskap

Den virtuella datorn på datavetenskap (DSVM) är en anpassad VM-avbildning som skapats specifikt för datavetenskap. Om du [skapar en DSVM](how-to-configure-environment.md#dsvm), SDK och notebook-server är installerat och konfigurerat åt dig. Du måste dock fortfarande skapa en arbetsyta och klona exempellagringsplatsen.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

## <a name="azure-notebooks"></a>Azure Notebooks

På [Azure anteckningsböcker](https://notebooks.azure.com/), SDK och notebook-server är installerat och konfigurerat åt dig. Azure-anteckningsböcker ger en fullständigt hanterad och lättviktig notebook-miljö för att utforska.

För att komma åt lagringsplatsen för exemplet på Azure-datorer, navigera till din Azure Machine Learning-arbetsyta via den [Azure-portalen](https://portal.azure.com). Från den **översikt** väljer **Kom igång i Azure anteckningsböcker**.

## <a name="next-steps"></a>Nästa steg

Utforska den [exempel anteckningsböcker](https://aka.ms/aml-notebooks) för att identifiera vilka Azure Machine Learning service kan göra eller prova de här självstudierna:

- [Träna och distribuera en modell för avbildningsklassificering med MNIST](tutorial-train-models-with-aml.md)

- [Förbereda data och använda automatiserad maskininlärning för att träna en regressionsmodell med NYC-taxidatauppsättningen](tutorial-data-prep.md)