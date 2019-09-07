---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/19/2019
ms.author: larryfr
ms.openlocfilehash: 055909d51fcd1228e8eb26189ba682e09aee6a1a
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390591"
---
Posterna i `inferenceconfig.json` dokument översikten till parametrarna för klassen [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . I följande tabell beskrivs mappningen mellan entiteter i JSON-dokumentet och parametrarna för-metoden:

| JSON-entitet | Metod parameter | Beskrivning |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Sökväg till en lokal fil som innehåller koden som ska köras för avbildningen. |
| `runtime` | `runtime` | Vilken körnings miljö som ska användas för avbildningen. Aktuella körningar som stöds `spark-py` är `python`och. |
| `condaFile` | `conda_file` | Valfritt. Sökväg till en lokal fil som innehåller en Conda-miljö definition som ska användas för avbildningen. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | Valfritt. Sökväg till en lokal fil som innehåller ytterligare Docker-steg att köra när avbildningen konfigureras. |
| `sourceDirectory` | `source_directory` | Valfritt. Sökväg till mappar som innehåller alla filer för att skapa avbildningen. |
| `enableGpu` | `enable_gpu` | Valfritt. Om du vill aktivera GPU-stöd i avbildningen. GPU-avbildningen måste användas på en Azure-tjänst, till exempel Azure Container Instances, Azure Machine Learning Compute, Azure Virtual Machines och Azure Kubernetes-tjänsten. Standardvärdet är false. |
| `baseImage` | `base_image` | Valfritt. Anpassad bild som ska användas som en bas avbildning. Om ingen bas avbildning anges, kommer avbildningen att baseras på den angivna körnings parametern. |
| `baseImageRegistry` | `base_image_registry` | Valfritt. Avbildnings register som innehåller bas avbildningen. |
| `cudaVersion` | `cuda_version` | Valfritt. Version av CUDA som ska installeras för avbildningar som behöver GPU-stöd. GPU-avbildningen måste användas på en Azure-tjänst, till exempel Azure Container Instances, Azure Machine Learning Compute, Azure Virtual Machines och Azure Kubernetes-tjänsten. Versioner som stöds är 9,0, 9,1 och 10,0. Om `enable_gpu` är inställt är standardvärdet 9,1. |
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