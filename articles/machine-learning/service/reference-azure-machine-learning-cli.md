---
title: Hur du använder Azure Machine Learning CLI-tillägg
description: Läs mer om Azure Machine Learning CLI-tillägget för Azure CLI. Azure CLI är ett plattformsoberoende kommandoradsverktyg som gör det möjligt att arbeta med resurser i Azure-molnet. Machine Learning-tillägget kan du arbeta med Azure Machine Learning-tjänsten.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 09/24/2018
ms.openlocfilehash: 13d09471191deed670db97a9f18e15bc9577dd1a
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51713426"
---
# <a name="use-the-azure-machine-learning-cli-extension"></a>Använd Azure Machine Learning CLI-tillägget

Azure Machine Learning CLI är ett tillägg till den [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), ett plattformsoberoende kommandoradsgränssnitt för Azure-plattformen. Det här tillägget innehåller kommandon för att arbeta med Azure Machine Learning-tjänsten från kommandoraden. Det kan du skapa skript som automatiserar dina arbetsflöden för maskininlärning. Du kan till exempel skapa skript som utför följande åtgärder:

+ Köra experiment för att skapa machine learning-modeller

+ Registrera maskininlärningsmodeller för kundens användning

+ Paketera, distribuera och spåra livscykeln för din machine learning-modeller

CLI är inte en ersättning för Azure Machine Learning-SDK. Det är ett kompletterande verktyg som är optimerad för att hantera mycket parametriserade uppgifter som:

* Skapa beräkningsresurser

* Skicka parametriserade experiment

* Modellen

* Skapa avbildningar

* Distributionen av tjänsten

## <a name="prerequisites"></a>Förutsättningar

* Den [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

> [!NOTE]
> Du måste ha en Azure-prenumeration om du vill använda CLI. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://aka.ms/AMLfree) innan du börjar.

## <a name="install-the-extension"></a>Installera tillägget

Om du vill installera tillägget Machine Learning CLI använder du följande kommando:

```azurecli-interactive
az extension add -s https://azuremlsdktestpypi.blob.core.windows.net/wheels/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1/azure_cli_ml-0.1.68-py2.py3-none-any.whl --pip-extra-index-urls  https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
```

När du uppmanas, väljer `y` att installera tillägget.

För att kontrollera att tillägget har installerats, använder du följande kommando för att visa en lista över ML-specifika underkommandon:

```azurecli-interactive
az ml -h
```

> [!TIP]
> Att uppdatera tillägget måste du __ta bort__ , och sedan __installera__ den. Detta installerar den senaste versionen.

## <a name="remove-the-extension"></a>Ta bort tillägget

Om du vill ta bort tillägget CLI, använder du följande kommando:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Resurshantering

Följande kommandon visar hur du använder CLI för att hantera resurser som används av Azure Machine Learning.


+ Skapa en arbetsyta för Azure Machine Learning-tjänsten:

   ```azurecli-interactive
   az ml workspace create -n myworkspace -g myresourcegroup
   ```

+ Ange en standardarbetsyta:

   ```azurecli-interactive
   az configure --defaults aml_workspace=myworkspace group=myresourcegroup
   ```

+ Skapa en DSVM (data science VM). Du kan också skapa BatchAI kluster för distribuerad utbildning eller AKS-kluster för distribution.


  ```azurecli-interactive
  az ml computetarget setup dsvm -n mydsvm
  ```

## <a name="experiments"></a>Experiment

Följande kommandon visar hur du använder CLI för att arbeta med experiment:

* Koppla ett projekt (kör konfiguration) innan du skickar ett experiment:

    ```azurecli-interactive
    az ml project attach --experiment-name myhistory
    ```

* Starta en körning av experimentet. När du använder det här kommandot kan du ange ett beräkningsmål. I det här exemplet `local` använder den lokala datorn för att träna modellen genom att använda den `train.py` skript:

    ```azurecli-interactive
    az ml run submit -c local train.py
    ```

* Visa en lista över skickade experiment:

    ```azurecli-interactive
    az ml history list
    ```

## <a name="model-registration-image-creation--deployment"></a>Modeller registrering, skapa avbildningar och distribution

Följande kommandon visar hur du kan registrera en träningsmodell och sedan distribuera den som en tjänst för produktion:

+ Registrera en modell med Azure Machine Learning:

  ```azurecli-interactive
  az ml model register -n mymodel -m sklearn_regression_model.pkl
  ```

+ Skapa en avbildning som innehåller dina machine learning-modell och beroenden: 

  ```azurecli-interactive
  az ml image create container -n myimage -r python -m mymodel:1 -f score.py -c myenv.yml
  ```

+ Distribuera en avbildning till ett beräkningsmål:

  ```azurecli-interactive
  az ml service create aci -n myaciservice --image-id myimage:1
  ```
