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
ms.openlocfilehash: 12da1b20c5e4e6299445b8ec8ec90eeec6711e2c
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805526"
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

<a name="automated-ml-setup"></a>

## <a name="automated-machine-learning-setup"></a>Konfigurera automatiserad maskininlärning 

_De här stegen gäller endast för notebook-filerna i mappen **how-to-use-azureml/automated-machine-learning**._

Du kan använda något av ovanstående alternativ, men du kan också installera miljön och skapa en arbetsyta på samma gång med följande instruktioner. 

1. Installera [Mini-conda](https://conda.io/miniconda.html). Välj 3.7 eller senare. Installera enligt anvisningarna. 
   >[!NOTE]
   >Du kan använda en befintlig conda om det är version 4.4.10 eller senare. Använd `conda -V` för att visa versionen. Du kan uppdatera en conda-version med kommandot: `conda update conda`. Du behöver inte installera just mini-conda.

1. Ladda ned exempelanteckningsböckerna från [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning
) som en ZIP-fil och extrahera innehållet till en lokal katalog. Anteckningsböckerna för automatiserad maskininlärning finns i mappen `how-to-use-azureml/automated-machine-learning`.

1. Konfigurera en ny Conda-miljö. 
   1. Öppna en Conda-prompt på den lokala datorn.
   
   1. Navigera till de filer du extraherade till den lokala datorn.
   
   1. Öppna mappen **automated-machine-learning**.
   
   1. Kör `automl_setup.cmd` i conda-prompten för Windows eller `.sh`-filen för ditt operativsystem. Det kan ta cirka 10 minuter att köra det.

      Konfigurationsskriptet:
      + Skapar en ny conda-miljö
      + Installerar nödvändiga paket
      + Konfigurerar widgeten
      + Startar en Jupyter-anteckningsbok
      
   >[!NOTE]
   > Skriptet tar conda-miljönamnet som en valfri parameter. Standardinställningen för conda-miljönamnet är `azure_automl`. Det exakta kommandot beror på operativsystemet. Detta är användbart om du skapar en ny miljö eller uppgraderar till en ny version. Du kan t.ex. använda automl_setup.cmd azure_automl_sandbox om du vill skapa ett miljönamn som azure_automl_sandbox. 
      
1. När skriptet har slutförts visas en Jupyter Notebook-startsida i webbläsaren.

1. Navigera till sökvägen där du sparade anteckningsböckerna. 

1. Öppna mappen automated-machine-learning och öppna sedan notebook-filen **configuration.ipynb**. 

1. Kör cellerna i anteckningsboken för att registrera Machine Learning Services-resursprovider och skapa en arbetsyta.

Nu är du redo att öppna och köra anteckningsböckerna som sparats på den lokala datorn.


## <a name="next-steps"></a>Nästa steg

Utforska [lagringsplatsen med GitHub-anteckningsböcker för Azure Machine Learning-tjänsten](https://aka.ms/aml-notebooks)

Prova de här självstudierna:
+ [Träna och distribuera en modell för avbildningsklassificering med MNIST](tutorial-train-models-with-aml.md)

+ [Förbereda data och använda automatiserad maskininlärning för att träna en regressionsmodell med NYC-taxidatauppsättningen](tutorial-data-prep.md)
