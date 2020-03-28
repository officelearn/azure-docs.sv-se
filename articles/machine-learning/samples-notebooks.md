---
title: Exempel på Jupyter Notebooks
titleSuffix: Azure Machine Learning
description: Hitta och använd exempelvis Jupyter-anteckningsböcker för att utforska Azure Machine Learning Python för SDK.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78673630"
---
# <a name="explore-azure-machine-learning-with-jupyter-notebooks"></a>Utforska Azure Machine Learning med Jupyter-anteckningsböcker

[Exemplet Azure Machine Learning Notebooks-databasen](https://github.com/azure/machinelearningnotebooks) innehåller de senaste Azure Machine Learning Python SDK-exemplen. Dessa Juypter-anteckningsböcker är utformade för att hjälpa dig att utforska SDK och fungera som modeller för dina egna maskininlärningsprojekt.

I den här artikeln visas hur du kommer åt databasen från följande miljöer:

- [Beräkningsinstans för Azure Machine Learning](#notebookvm)
- [Ta med din egen bärbara server](#byo)
- [Virtuell dator för datavetenskap](#dsvm)

> [!NOTE]
> När du har klonat databasen hittar du självstudieanteckningsböcker i självstudiemappen och **funktionsspecifika** anteckningsböcker i mappen **how-to-use-azureml.**

<a name="notebookvm"></a>
## <a name="get-samples-on-azure-machine-learning-compute-instance"></a>Hämta exempel på Azure Machine Learning-beräkningsinstans

Det enklaste sättet att komma igång med exemplen är att slutföra [självstudien: Installationsmiljö och arbetsyta](tutorial-1st-experiment-sdk-setup.md). När du är klar har du en dedikerad anteckningsboksserver förinstallerad med SDK och exempeldatabasen. Inga nedladdningar eller installation krävs.

<a name="byo"></a>

## <a name="get-samples-on-your-notebook-server"></a>Hämta exempel på din bärbara server

Om du vill ta med din egen bärbara server för lokal utveckling gör du så här:

[!INCLUDE [aml-your-server](../../includes/aml-your-server.md)]

Dessa instruktioner installerar de bas-SDK-paket som krävs för snabbstarts- och självstudiedatorböckerna. Andra exempel bärbara datorer kan kräva att du installerar extra komponenter. Mer information finns [i Installera Azure Machine Learning SDK för Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

<a name="dsvm"></a>
## <a name="get-samples-on-dsvm"></a>Få exempel på DSVM

Data Science Virtual Machine (DSVM) är en anpassad VM-avbildning som skapats speciellt för datavetenskap. Om du [skapar en DSVM](how-to-configure-environment.md#dsvm)installeras och konfigureras SDK- och anteckningsboksservern åt dig. Du måste dock fortfarande skapa en arbetsyta och klona exempeldatabasen.

[!INCLUDE [aml-dsvm-server](../../includes/aml-dsvm-server.md)]

## <a name="next-steps"></a>Nästa steg

Utforska [exempeldatorerna](https://aka.ms/aml-notebooks) för att ta reda på vad Azure Machine Learning kan göra eller prova följande självstudier:

- [Träna och distribuera en modell för avbildningsklassificering med MNIST](tutorial-train-models-with-aml.md)

- [Förbereda data och använda automatiserad maskininlärning för att träna en regressionsmodell med NYC-taxidatauppsättningen](tutorial-auto-train-models.md)
