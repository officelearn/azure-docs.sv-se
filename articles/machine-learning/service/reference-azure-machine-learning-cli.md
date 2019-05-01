---
title: Machine learning-CLI-tillägg
titleSuffix: Azure Machine Learning service
description: Läs mer om Azure Machine Learning CLI-tillägget för Azure CLI. Azure CLI är ett plattformsoberoende kommandoradsverktyg som gör det möjligt att arbeta med resurser i Azure-molnet. Machine Learning-tillägget kan du arbeta med Azure Machine Learning-tjänsten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: 2992ec9f43aac9e0d80c5e42873d26ac3a9c3fd1
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64916977"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>Använda CLI-tillägg för Azure Machine Learning-tjänsten

Azure Machine Learning CLI är ett tillägg till den [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), ett plattformsoberoende kommandoradsgränssnitt för Azure-plattformen. Det här tillägget innehåller kommandon för att arbeta med Azure Machine Learning-tjänsten från kommandoraden. Det kan du automatisera arbetsflöden för maskininlärning. Exempelvis kan du utföra följande åtgärder:

+ Köra experiment för att skapa machine learning-modeller

+ Registrera maskininlärningsmodeller för kundens användning

+ Paketera, distribuera och spåra livscykeln för din machine learning-modeller

CLI är inte en ersättning för Azure Machine Learning-SDK. Det är en kompletterande verktyg som är optimerad för att hantera mycket parametriserade uppgifter som passar själva att automation.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Du måste ha en Azure-prenumeration om du vill använda CLI. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

* Den [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="install-the-extension"></a>Installera tillägget

Om du vill installera tillägget Machine Learning CLI använder du följande kommando:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Exempel-filer som du kan använda med kommandona nedan finns [här](http://aka.ms/azml-deploy-cloud).

När du uppmanas, väljer `y` att installera tillägget.

För att kontrollera att tillägget har installerats, använder du följande kommando för att visa en lista över ML-specifika underkommandon:

```azurecli-interactive
az ml -h
```

## <a name="remove-the-extension"></a>Ta bort tillägget

Om du vill ta bort tillägget CLI, använder du följande kommando:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Resurshantering

Följande kommandon visar hur du använder CLI för att hantera resurser som används av Azure Machine Learning.

+ Om du inte redan har en, skapa en resursgrupp:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Skapa en arbetsyta för Azure Machine Learning-tjänsten:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

+ Koppla en arbetsytekonfiguration till en mapp för att aktivera sammanhangsbaserad medvetenhet för CLI.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

+ Koppla en Azure blob-behållare som ett datalager.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

+ Koppla ett AKS-kluster som en Compute-mål.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myrg -w myworkspace
    ```

+ Skapa ett nytt AMLcompute mål

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```
    
## <a id="experiments"></a>Köra experiment

+ Koppla en arbetsytekonfiguration till en mapp för att aktivera sammanhangsbaserad medvetenhet för CLI.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

* Starta en körning av experimentet. När du använder det här kommandot kan du ange namnet på filen runconfig (text före \*.runconfig om du tittar på ditt filsystem) mot - c-parametern.

    ```azurecli-interactive
    az ml run submit-script -c local -e testexperiment train.py
    ```

* Visa en lista över experiment:

    ```azurecli-interactive
    az ml experiment list
    ```

## <a name="model-registration-profiling-deployment"></a>Registrering av modellen, profilering, distribution

Följande kommandon visar hur du kan registrera en träningsmodell och sedan distribuera den som en tjänst för produktion:

+ Registrera en modell med Azure Machine Learning:

  ```azurecli-interactive
  az ml model register -n mymodel -p sklearn_regression_model.pkl
  ```

+ Distribuera din modell till AKS

  ```azurecli-interactive
  az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
  ```
