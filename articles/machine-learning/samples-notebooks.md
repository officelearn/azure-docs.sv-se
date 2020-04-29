---
title: Exempel på Jupyter Notebooks
titleSuffix: Azure Machine Learning
description: Hitta och Använd exempel Jupyter Notebooks för att utforska Azure Machine Learning python för SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 7242b82ee5c43878a33731bd1f02b685020f22b0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "78673630"
---
# <a name="explore-azure-machine-learning-with-jupyter-notebooks"></a>Utforska Azure Machine Learning med Jupyter-anteckningsböcker

[Exemplet Azure Machine Learning Notebooks-lagringsplatsen](https://github.com/azure/machinelearningnotebooks) innehåller de senaste Azure Machine Learning python SDK-exemplen. Dessa Juypter-anteckningsböcker är utformade för att hjälpa dig att utforska SDK och fungerar som modeller för dina egna Machine Learning-projekt.

Den här artikeln visar hur du kommer åt databasen från följande miljöer:

- [Azure Machine Learning beräknings instans](#notebookvm)
- [Ta med din egen Notebook-Server](#byo)
- [Virtuell dator för datavetenskap](#dsvm)

> [!NOTE]
> När du har klonat lagrings platsen hittar du själv studie **kurser i självstudierna** och de funktionsbaserade antecknings böckerna i mappen **How-to-use-azureml** .

<a name="notebookvm"></a>
## <a name="get-samples-on-azure-machine-learning-compute-instance"></a>Hämta exempel på Azure Machine Learning beräknings instans

Det enklaste sättet att komma igång med exemplen är att slutföra [självstudien: installations miljö och arbets yta](tutorial-1st-experiment-sdk-setup.md). När du är klar har du en dedikerad Notebook-server som är förinstallerad med SDK och exempel lagrings plats. Inga hämtningar eller installationer krävs.

<a name="byo"></a>

## <a name="get-samples-on-your-notebook-server"></a>Hämta exempel på din Notebook-Server

Följ dessa steg om du vill ta med din egen Notebook-Server för lokal utveckling:

[!INCLUDE [aml-your-server](../../includes/aml-your-server.md)]

De här anvisningarna installerar de grundläggande SDK-paket som krävs för de bärbara datorerna för snabb start och självstudier. Andra exempel på antecknings böcker kan kräva att du installerar extra komponenter. Mer information finns i [installera Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

<a name="dsvm"></a>
## <a name="get-samples-on-dsvm"></a>Hämta exempel på DSVM

Data Science Virtual Machine (DSVM) är en anpassad VM-avbildning som skapats specifikt för data vetenskap. Om du [skapar en DSVM](how-to-configure-environment.md#dsvm)installeras och konfigureras SDK-och Notebook-servern för dig. Du måste dock fortfarande skapa en arbets yta och klona exempel lagrings platsen.

[!INCLUDE [aml-dsvm-server](../../includes/aml-dsvm-server.md)]

## <a name="next-steps"></a>Nästa steg

Utforska [exempel antecknings böckerna](https://aka.ms/aml-notebooks) för att upptäcka vad Azure Machine Learning kan göra, eller prova med de här självstudierna:

- [Träna och distribuera en modell för avbildningsklassificering med MNIST](tutorial-train-models-with-aml.md)

- [Förbereda data och använda automatiserad maskininlärning för att träna en regressionsmodell med NYC-taxidatauppsättningen](tutorial-auto-train-models.md)
