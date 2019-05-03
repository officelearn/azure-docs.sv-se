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
ms.openlocfilehash: cd88fd85ce6d18287c700a54e42b6237a42ea5c9
ms.sourcegitcommit: eea74d11a6d6ea6d187e90e368e70e46b76cd2aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2019
ms.locfileid: "65035375"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Använd Jupyter-anteckningsböcker för att utforska Azure Machine Learning-tjänsten

Vi har tagit fram en serie med Jupyter Python-anteckningsböcker som du kan använda för att utforska Azure Machine Learning-tjänsten. 

Lär dig hur du använder tjänsten med dokumentationen på den här webbplatsen och anpassar de här anteckningsböckerna efter din situation. 

Använd någon av sökvägarna nedan för att köra en notebook-server med de här exemplen på notebook-filer.  När servern körs kan du hitta notebook-filer med självstudier i mappen **självstudier**, men du kan också utforska de olika funktionerna i mappen **how-to-use-azureml**.

## <a name="a-managed-cloud-notebook-server"></a>En hanterad notebook-server

Det är enkelt att komma igång med din egen molnbaserad notebook-server. Exemplet anteckningsboken och [Azure Machine Learning-SDK för Python](https://aka.ms/aml-sdk) redan har installerats och konfigurerats för dig när du skapar den här molnresursen.  

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

* Exemplen är tillgängliga på notebook-webbsidan.

## <a name="a-data-science-virtual-machine-dsvm"></a>En virtuell dator för datavetenskap (DSVM)

[Azure Machine Learning SDK för Python](https://aka.ms/aml-sdk) och notebook-servern har redan installerats och konfigurerats för dig på en DSVM. 

När du har [skapat en DSVM](how-to-configure-environment.md#dsvm) använder du följande steg på din DSVM för att köra notebook-filerna.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

## <a name="your-own-jupyter-notebook-server"></a>En egen Jupyter Notebook-server

Skapa en lokal Jupyter Notebook-server på datorn enligt nedan.

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

Instruktionerna kommer att installera de paket som du behöver köra anteckningsböcker för Snabbstart och självstudier.  Andra exempelanteckningsböcker kan kräva installation av ytterligare komponenter.  Mer information om dessa komponenter finns i avsnittet om att [installera Azure Machine Learning SDK för Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

## <a name="azure-notebooks"></a>Azure Notebooks

Exempelanteckningsböcker och [Azure Machine Learning-SDK för Python](https://aka.ms/aml-sdk) redan har installerats och konfigurerats för dig på [Azure anteckningsböcker](https://notebooks.azure.com/). Installationen och framtida uppdateringar hanteras automatiskt via Azure-tjänster.

Använd den [Azure-portalen](https://portal.azure.com) att komma igång med Azure-anteckningsböcker.  Öppna din arbetsyta och från den **översikt** väljer **Kom igång i Azure anteckningsböcker**.

## <a name="next-steps"></a>Nästa steg

+ Utforska exempelanteckningsböcker för Azure Machine Learning-tjänsten i den här GitHub-lagringsplatsen: https://aka.ms/aml-notebooks

Prova de här självstudierna:
+ [Träna och distribuera en modell för avbildningsklassificering med MNIST](tutorial-train-models-with-aml.md)

+ [Förbereda data och använda automatiserad maskininlärning för att träna en regressionsmodell med NYC-taxidatauppsättningen](tutorial-data-prep.md)