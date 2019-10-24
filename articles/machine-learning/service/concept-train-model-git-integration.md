---
title: Git-integrering för Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Lär dig hur Azure Machine Learning integreras med en lokal git-lagringsplats. När du skickar en utbildning som körs från en lokal katalog som är git-lagringsplats, spåras information om lagrings platsen, gren och pågående genomförande som en del av körningen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 10/11/2019
ms.openlocfilehash: c8b2407b18f0d7115ce51fc28b956e7fd764c71e
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756020"
---
# <a name="git-integration-for-azure-machine-learning"></a>Git-integrering för Azure Machine Learning

[Git](https://git-scm.com/) är ett populärt versions kontroll system som gör att du kan dela och samar beta om dina projekt. När du skickar ett utbildnings jobb till Azure Machine Learning, om inlärnings filen lagras i en lokal git-lagringsplats, spåras information om lagrings platsen som en del av inlärnings processen.

Eftersom Azure Machine Learning spårar information från en lokal git-lagrings platsen är den inte kopplad till någon speciell Central lagrings plats. Din lagrings plats kan klonas från GitHub, GitLab, BitBucket, Azure DevOps eller någon annan git-kompatibel tjänst.

## <a name="how-does-git-integration-work"></a>Hur fungerar git-integrering?

När du skickar en utbildning som körs från python SDK eller Machine Learning CLI överförs filerna som behövs för att träna modellen till din arbets yta. Om kommandot `git` är tillgängligt i utvecklings miljön använder överförings processen för att kontrol lera om filerna lagras på en git-lagringsplats. I så fall, överförs information från git-lagringsplatsen också som en del av övnings körningen. Den här informationen lagras i följande egenskaper för övnings körningen:

| Egenskap | Git-kommando som används för att hämta värdet | Beskrivning |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | Den URI som din lagrings plats har kopierats från. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | Den URI som din lagrings plats har kopierats från. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | Den aktiva grenen när körningen skickades. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | Den aktiva grenen när körningen skickades. |
| `azureml.git.commit` | `git rev-parse HEAD` | Bekräfta hashen för den kod som skickades för körningen. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | Bekräfta hashen för den kod som skickades för körningen. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`, om grenen/genomförandet är smutsig; annars `false`. |

Den här informationen skickas för körningar som använder en uppskattnings-, maskin inlärnings-pipeline eller skript körning.

Om dina utbildningsbaserade filer inte finns i en git-lagringsplats i utvecklings miljön eller om kommandot `git` inte är tillgängligt spåras ingen git-relaterad information.

## <a name="view-the-logged-information"></a>Visa den loggade informationen

Git-informationen lagras i egenskaperna för en utbildnings körning. Du kan visa den här informationen med hjälp av Azure Portal, python SDK och CLI. 

### <a name="azure-portal"></a>Azure portal

1. Välj din arbets yta från [Azure Portal](https://portal.azure.com).
1. Välj __experiment__och välj sedan ett av experimenten.
1. Välj en av körningarna från kolumnen __Kör nummer__ .
1. Välj __loggar__och expandera sedan __loggarna__ och __azureml__ -posterna. Välj den länk som börjar med __### \_azure__.

    ![Posten # # #_azure i portalen](./media/concept-train-model-git-integration/azure-machine-learning-logs.png)

Den loggade informationen innehåller text som liknar följande JSON:

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>Python SDK

När du har skickat in en utbildnings körning returneras ett [körnings](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) objekt. Attributet `properties` för det här objektet innehåller den loggade git-informationen. Följande kod hämtar exempelvis commit hash:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>CLI

Kommandot `az ml run` CLI kan användas för att hämta egenskaperna från en körning. Följande kommando returnerar till exempel egenskaperna för den senaste körningen i experimentet med namnet `train-on-amlcompute`:

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Mer information finns i referens dokumentationen för [AZ ml-körning](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest) .

## <a name="next-steps"></a>Nästa steg

* [Konfigurera och Använd Compute-mål för modell träning](how-to-set-up-training-targets.md)