---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/19/2019
ms.author: larryfr
ms.openlocfilehash: 03b40b1f334816f08d7e169f8ff78bdb7c06c4de
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348570"
---
Posterna i `inferenceconfig.json` dokument översikten till parametrarna för klassen [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . I följande tabell beskrivs mappningen mellan entiteter i JSON-dokumentet och parametrarna för-metoden:

| JSON-entitet | Metod parameter | Beskrivning |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Sökväg till lokal fil som innehåller koden som ska köras för avbildningen. |
| `runtime` | `runtime` | Vilken körnings miljö som ska användas för avbildningen. Aktuella körningar som stöds är "Spark-py" och "python". |
| `condaFile` | `conda_file` | Valfri. Sökväg till en lokal fil som innehåller en Conda-miljö definition som ska användas för avbildningen. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | Valfri. Sökväg till lokal fil som innehåller ytterligare Docker-steg att köra när avbildningen konfigureras. |
| `sourceDirectory` | `source_directory` | Valfri. Sökväg till mappar som innehåller alla filer för att skapa avbildningen. |
| `enableGpu` | `enable_gpu` | Valfri. Om du vill aktivera GPU-stöd i avbildningen. GPU-avbildningen måste användas i Microsoft Azure-tjänsten. Till exempel Azure Container Instances, Azure Machine Learning Compute, Azure Virtual Machines och Azure Kubernetes-tjänsten. Standardvärdet är false. |
| `baseImage` | `base_image` | Valfri. En anpassad bild som ska användas som bas avbildning. Om ingen bas avbildning anges används bas avbildningen baserat på den aktuella körnings parametern. |
| `baseImageRegistry` | `base_image_registry` | Valfri. Avbildnings register som innehåller bas avbildningen. |
| `cudaVersion` | `cuda_version` | Valfri. Version av CUDA som ska installeras för avbildningar som behöver GPU-stöd. GPU-avbildningen måste användas på Microsoft Azure Services. Till exempel Azure Container Instances, Azure Machine Learning Compute, Azure Virtual Machines och Azure Kubernetes-tjänsten. Versioner som stöds är 9,0, 9,1 och 10,0. Om ' enable_gpu ' är inställt, är standardvärdet ' 9,1 '. |
| `description` | `description` |  En beskrivning av den här avbildningen. |

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