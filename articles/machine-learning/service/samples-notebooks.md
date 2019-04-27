---
title: Exempel på Jupyter Notebooks
titleSuffix: Azure Machine Learning service
description: Hitta och använd exempel på Jupyter-anteckningsböcker för att utforska Azure Machine Learning-tjänsten i Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: b597d6efa87aa2811ce42f3315698acfa17426b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60753474"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Använd Jupyter-anteckningsböcker för att utforska Azure Machine Learning-tjänsten

Vi har tagit fram en serie med Jupyter Python-anteckningsböcker som du kan använda för att utforska Azure Machine Learning-tjänsten. 

Lär dig hur du använder tjänsten med dokumentationen på den här webbplatsen och anpassar de här anteckningsböckerna efter din situation. 

Använd någon av sökvägarna nedan för att köra en notebook-server med de här exemplen på notebook-filer.  När servern körs kan du hitta notebook-filer med självstudier i mappen **självstudier**, men du kan också utforska de olika funktionerna i mappen **how-to-use-azureml**.


## <a name="try-azure-notebooks-free-jupyter-notebooks-in-the-cloud"></a>Prova Azure Notebooks: Kostnadsfria Jupyter-anteckningsböcker i molnet

Det är lätt att komma igång med Azure Notebooks! [Azure Machine Learning SDK för Python](https://aka.ms/aml-sdk) har redan installerats och konfigurerats för dig i [Azure Notebooks](https://notebooks.azure.com/). Installationen och framtida uppdateringar hanteras automatiskt via Azure-tjänster.
  
[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]


## <a name="use-a-data-science-virtual-machine-dsvm"></a>Använda en Data Science Virtual Machine (DSVM)

[Azure Machine Learning SDK för Python](https://aka.ms/aml-sdk) och notebook-servern har redan installerats och konfigurerats för dig på en DSVM. 

När du har [skapat en DSVM](how-to-configure-environment.md#dsvm) använder du följande steg på din DSVM för att köra notebook-filerna.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]


## <a name="use-your-own-jupyter-notebook-server"></a>Använda en egen Jupyter Notebook-server

Skapa en lokal Jupyter Notebook-server på datorn enligt nedan.

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

Anvisningarna i den här snabbstarten kommer att installera de paket som du behöver för köra anteckningsböckerna för snabbstart och självstudier.  Andra exempelanteckningsböcker kan kräva installation av ytterligare komponenter.  Mer information om dessa komponenter finns i avsnittet om att [installera Azure Machine Learning SDK för Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

## <a name="next-steps"></a>Nästa steg

Utforska [lagringsplatsen med GitHub-anteckningsböcker för Azure Machine Learning-tjänsten](https://aka.ms/aml-notebooks)

Prova de här självstudierna:
+ [Träna och distribuera en modell för avbildningsklassificering med MNIST](tutorial-train-models-with-aml.md)

+ [Förbereda data och använda automatiserad maskininlärning för att träna en regressionsmodell med NYC-taxidatauppsättningen](tutorial-data-prep.md)
