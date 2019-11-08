---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 11/06/2019
ms.author: larryfr
ms.openlocfilehash: ab31d45808a8c77c53b895643eec63952201d9e4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73800146"
---
Posterna i `inferenceconfig.json` dokument kartan till parametrarna för klassen [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . I följande tabell beskrivs mappningen mellan entiteter i JSON-dokumentet och parametrarna för-metoden:

| JSON-entitet | Metod parameter | Beskrivning |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Sökväg till en lokal fil som innehåller koden som ska köras för avbildningen. |
| `runtime` | `runtime` | Vilken körnings miljö som ska användas för avbildningen. De aktuella körningarna som stöds är `spark-py` och `python`. |
| `condaFile` | `conda_file` | Valfri. Sökväg till en lokal fil som innehåller en Conda-miljö definition som ska användas för avbildningen. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | Valfri. Sökväg till en lokal fil som innehåller ytterligare Docker-steg att köra när avbildningen konfigureras. |
| `sourceDirectory` | `source_directory` | Valfri. Sökväg till mappar som innehåller alla filer för att skapa avbildningen. |
| `enableGpu` | `enable_gpu` | Valfri. Om du vill aktivera GPU-stöd i avbildningen. GPU-avbildningen måste användas på en Azure-tjänst, till exempel Azure Container Instances, Azure Machine Learning Compute, Azure Virtual Machines och Azure Kubernetes-tjänsten. Standardvärdet är false. |
| `baseImage` | `base_image` | Valfri. Anpassad bild som ska användas som en bas avbildning. Om ingen bas avbildning anges, kommer avbildningen att baseras på den angivna körnings parametern. |
| `baseImageRegistry` | `base_image_registry` | Valfri. Avbildnings register som innehåller bas avbildningen. |
| `cudaVersion` | `cuda_version` | Valfri. Version av CUDA som ska installeras för avbildningar som behöver GPU-stöd. GPU-avbildningen måste användas på en Azure-tjänst, till exempel Azure Container Instances, Azure Machine Learning Compute, Azure Virtual Machines och Azure Kubernetes-tjänsten. Versioner som stöds är 9,0, 9,1 och 10,0. Om `enable_gpu` har angetts är standardvärdet 9,1. |
| `description` | `description` | En beskrivning av avbildningen. |

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