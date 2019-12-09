---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 11/06/2019
ms.author: larryfr
ms.openlocfilehash: 52689c585e148c18d16ad627570414a8de444fea
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927022"
---
Posterna i `inferenceconfig.json` dokument kartan till parametrarna för klassen [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . I följande tabell beskrivs mappningen mellan entiteter i JSON-dokumentet och parametrarna för-metoden:

| JSON-entitet | Metod parameter | Beskrivning |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Sökväg till en lokal fil som innehåller koden som ska köras för avbildningen. |
| `runtime` | `runtime` | Valfri. Vilken körnings miljö som ska användas för avbildningen. De aktuella körningarna som stöds är `spark-py` och `python`. Om `environment` har angetts ignoreras detta. |
| `condaFile` | `conda_file` | Valfri. Sökväg till en lokal fil som innehåller en Conda-miljö definition som ska användas för avbildningen.  Om `environment` har angetts ignoreras detta. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | Valfri. Sökväg till en lokal fil som innehåller ytterligare Docker-steg att köra när avbildningen konfigureras.  Om `environment` har angetts ignoreras detta.|
| `sourceDirectory` | `source_directory` | Valfri. Sökväg till mappar som innehåller alla filer för att skapa avbildningen. |
| `enableGpu` | `enable_gpu` | Valfri. Om du vill aktivera GPU-stöd i avbildningen. GPU-avbildningen måste användas på en Azure-tjänst, till exempel Azure Container Instances, Azure Machine Learning Compute, Azure Virtual Machines och Azure Kubernetes-tjänsten. Standardvärdet är False. Om `environment` har angetts ignoreras detta.|
| `baseImage` | `base_image` | Valfri. Anpassad bild som ska användas som en bas avbildning. Om ingen bas avbildning anges, kommer avbildningen att baseras på den angivna körnings parametern. Om `environment` har angetts ignoreras detta. |
| `baseImageRegistry` | `base_image_registry` | Valfri. Avbildnings register som innehåller bas avbildningen. Om `environment` har angetts ignoreras detta.|
| `cudaVersion` | `cuda_version` | Valfri. Version av CUDA som ska installeras för avbildningar som behöver GPU-stöd. GPU-avbildningen måste användas på en Azure-tjänst, till exempel Azure Container Instances, Azure Machine Learning Compute, Azure Virtual Machines och Azure Kubernetes-tjänsten. Versioner som stöds är 9,0, 9,1 och 10,0. Om `enable_gpu` har angetts är standardvärdet 9,1. Om `environment` har angetts ignoreras detta. |
| `description` | `description` | En beskrivning av avbildningen. Om `environment` har angetts ignoreras detta.  |
| `environment` | `environment` | Valfri.  Azure Machine Learnings [miljö](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py).|

Följande JSON är ett exempel på en konfiguration som kan användas med CLI:

```json
{
    "entryScript": "score.py",
    "runtime": "python",
    "condaFile": "myenv.yml",
    "extraDockerfileSteps": null,
    "sourceDirectory": null,
    "enableGpu": false,
    "baseImage": null,
    "baseImageRegistry": null
}
```

Följande JSON är ett exempel på en konfigurations miljö som använder en befintlig Azure Machine Learning- [miljö](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) med en speciell version som kan användas med CLI:

```json
{
    "entryScript": "score.py",
    "environment":{
        "name": "myenv",
        "version": "1"
    },
    "condaFile": "myenv.yml",
    "sourceDirectory": null
}
```

Följande JSON är ett exempel på en konfiguration för en konfiguration som använder en befintlig Azure Machine Learning- [miljö](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) med den senaste versionen för att användas med CLI:

```json
{
    "entryScript": "score.py",
    "environment":{
        "name": "myenv",
        "version": null
    },
    "condaFile": "myenv.yml",
    "sourceDirectory": null
}
```
