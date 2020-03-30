---
title: Git-integrering för Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Lär dig mer om hur Azure Machine Learning integreras med en lokal Git-databas. När du skickar en utbildningskörning från en lokal katalog som är en Git-databas spåras information om repo, gren och aktuellt genomförande som en del av körningen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.openlocfilehash: 7cc2e346a35cd1cdf1278b527dc451a903d60f89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402833"
---
# <a name="git-integration-for-azure-machine-learning"></a>Git-integrering för Azure Machine Learning

[Git](https://git-scm.com/) är ett populärt versionskontrollsystem som låter dig dela och samarbeta i dina projekt. 

Azure Machine Learning stöder Git-databaser fullt ut för att spåra arbete – du kan klona databaser direkt på ditt delade arbetsytefilsystem, använda Git på din lokala arbetsstation eller använda Git från en CI/CD-pipeline.

När du skickar ett jobb till Azure Machine Learning spåras information om arkivet som en del av utbildningsprocessen om källfilerna lagras i en lokal git-databas.

Eftersom Azure Machine Learning spårar information från en lokal git-repo, är den inte knuten till någon specifik central databas. Din databas kan klonas från GitHub, GitLab, Bitbucket, Azure DevOps eller någon annan git-kompatibel tjänst.

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Klona Git-databaser i ditt arbetsytefilsystem
Azure Machine Learning tillhandahåller ett delat filsystem för alla användare på arbetsytan.
Om du vill klona en Git-databas till den här filresursen rekommenderar vi att du skapar en beräkningsinstans & öppnar en terminal.
När terminalen har öppnats har du tillgång till en fullständig Git-klient och kan klona och arbeta med Git via Git CLI-upplevelsen.

Vi rekommenderar att du klonar databasen i användarkatalogen så att andra inte gör kollisioner direkt på din arbetsgren.

Du kan klona alla Git-databaser som du kan autentisera till (GitHub, Azure Repos, BitBucket, etc.)

För en guide om hur man använder Git CLI, läs här [här](https://guides.github.com/introduction/git-handbook/).

## <a name="track-code-that-comes-from-git-repositories"></a>Spårkod som kommer från Git-databaser

När du skickar in en utbildningskörning från Python SDK eller Machine Learning CLI överförs de filer som behövs för att träna modellen till din arbetsyta. Om `git` kommandot är tillgängligt i utvecklingsmiljön används det för att kontrollera om filerna lagras i en git-databas. Om så är fallet överförs även information från git-databasen som en del av träningskörningen. Den här informationen lagras i följande egenskaper för träningskörningen:

| Egenskap | Git-kommando som används för att hämta värdet | Beskrivning |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | Uri:n som databasen klonades från. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | Uri:n som databasen klonades från. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | Den aktiva grenen när körningen skickades. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | Den aktiva grenen när körningen skickades. |
| `azureml.git.commit` | `git rev-parse HEAD` | Commit-hash för koden som skickades för körningen. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | Commit-hash för koden som skickades för körningen. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`, om grenen/begå är smutsig; annars. `false` |

Den här informationen skickas för körningar som använder en uppskattnings-, machine learning-pipeline eller skriptkörning.

Om dina träningsfiler inte finns i en git-databas `git` i utvecklingsmiljön, eller om kommandot inte är tillgängligt, spåras ingen git-relaterad information.

> [!TIP]
> Om du vill kontrollera om kommandot git är tillgängligt i utvecklingsmiljön öppnar du en skalsession, kommandotolk, PowerShell eller annat kommandoradsgränssnitt och skriver följande kommando:
>
> ```
> git --version
> ```
>
> Om installerat, och i sökvägen, får `git version 2.4.1`du ett svar som liknar . Mer information om hur du installerar git i utvecklingsmiljön finns på [Git-webbplatsen](https://git-scm.com/).

## <a name="view-the-logged-information"></a>Visa den loggade informationen

git-informationen lagras i egenskaperna för en träningskörning. Du kan visa den här informationen med Azure-portalen, Python SDK och CLI. 

### <a name="azure-portal"></a>Azure Portal

1. Välj arbetsyta på [Azure-portalen.](https://portal.azure.com)
1. Välj __Experiment__och välj sedan ett av experimenten.
1. Välj en av körningarna från kolumnen __KÖRNUMMER.__
1. Välj __Loggar__och expandera sedan __loggar och__ __azureml-poster.__ Välj länken som börjar __ ### \___ med azure .

    ![Posten ###_azure i portalen](./media/concept-train-model-git-integration/azure-machine-learning-logs.png)

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

När du har skickat in en träningskörning returneras ett [Run-objekt.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) Attributet `properties` för det här objektet innehåller den loggade git-informationen. Följande kod hämtar till exempel commit-hash:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>CLI

`az ml run` CLI-kommandot kan användas för att hämta egenskaperna från en körning. Följande kommando returnerar till exempel egenskaperna för den `train-on-amlcompute`sista körningen i experimentet med namnet :

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Mer information finns i referensdokumentationen [för az ml run.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest)

## <a name="next-steps"></a>Nästa steg

* [Ställa in och använda beräkningsmål för modellutbildning](how-to-set-up-training-targets.md)
