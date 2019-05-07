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
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: be3cedc4b496f4f64a52217099f64092dfb49228
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149853"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>Använda CLI-tillägg för Azure Machine Learning-tjänsten

Azure Machine Learning CLI är ett tillägg till den [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), ett plattformsoberoende kommandoradsgränssnitt för Azure-plattformen. Det här tillägget innehåller kommandon för att arbeta med Azure Machine Learning-tjänsten. Det kan du automatisera din machine learning-aktiviteter. I följande lista innehåller vissa exempel som du kan göra med CLI-tillägg:

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

    Mer information finns i [az ml-arbetsyta skapar](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create).

+ Koppla en arbetsytekonfiguration till en mapp för att aktivera sammanhangsbaserad medvetenhet för CLI.
    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Mer information finns i [az ml mappen bifoga](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

+ Koppla en Azure blob-behållare som ett datalager.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    Mer information finns i [az ml datalager bifoga-blob](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob).

    
+ Koppla ett AKS-kluster som en Compute-mål.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    Mer information finns i [az ml computetarget bifoga aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

+ Skapa ett nytt AMLcompute mål.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    Mer information finns i [az ml computetarget skapa amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

## <a id="experiments"></a>Köra experiment

* Starta en körning av experimentet. När du använder det här kommandot kan du ange namnet på filen runconfig (text före \*.runconfig om du tittar på ditt filsystem) mot - c-parametern.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    Mer information finns i [az ml kör skicka skript](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

* Visa en lista över experiment:

    ```azurecli-interactive
    az ml experiment list
    ```

    Mer information finns i [az ml experimentera lista](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

## <a name="model-registration-profiling-deployment"></a>Registrering av modellen, profilering, distribution

Följande kommandon visar hur du kan registrera en träningsmodell och sedan distribuera den som en tjänst för produktion:

+ Registrera en modell med Azure Machine Learning:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    Mer information finns i [az ml-modellen registrera](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register).

+ **VALFRITT** profilera din modell för att få optimal processor- och värden för distribution.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    Mer information finns i [az ml-modellen profil](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

+ Distribuera din modell till AKS

    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
    ```

    Mer information finns i [az ml modelldistribution](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy).


## <a name="next-steps"></a>Nästa steg

* [Kommandot referens för Machine Learning CLI-tillägg för](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest).
