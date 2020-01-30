---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 6970732f53ffa99849e20f279c8bdf4160c30a0a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845139"
---
Posterna i `inferenceconfig.json` dokument kartan till parametrarna för klassen [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . I följande tabell beskrivs mappningen mellan entiteter i JSON-dokumentet och parametrarna för-metoden:

| JSON-entitet | Metod parameter | Beskrivning |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Sökväg till en lokal fil som innehåller koden som ska köras för avbildningen. |
| `runtime` | `runtime` | Valfri. Vilken körnings miljö som ska användas för avbildningen. Körningar som stöds är `spark-py` och `python`. Om `environment` har angetts ignoreras den här posten. |
| `condaFile` | `conda_file` | Valfri. Sökväg till en lokal fil som innehåller en Conda-miljö definition som ska användas för avbildningen.  Om `environment` har angetts ignoreras den här posten. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | Valfri. Sökväg till en lokal fil som innehåller ytterligare Docker-steg att köra när avbildningen konfigureras.  Om `environment` har angetts ignoreras den här posten.|
| `sourceDirectory` | `source_directory` | Valfri. Sökväg till mappar som innehåller alla filer för att skapa avbildningen, vilket gör det enkelt att komma åt filer i den här mappen eller undermappen. Du kan ladda upp en hel mapp från den lokala datorn som beroenden för WebService. Obs! dina entry_script, conda_file och extra_docker_file_steps sökvägar är relativa sökvägar till source_directory sökvägen. |
| `enableGpu` | `enable_gpu` | Valfri. Om du vill aktivera GPU-stöd i avbildningen. GPU-avbildningen måste användas på en Azure-tjänst, t. ex. Azure Container Instances. Till exempel Azure Machine Learning Compute, Azure Virtual Machines och Azure Kubernetes-tjänsten. Standardvärdet är false. Om `environment` har angetts ignoreras den här posten.|
| `baseImage` | `base_image` | Valfri. Anpassad bild som ska användas som en bas avbildning. Om ingen bas avbildning anges, kommer avbildningen att baseras på den angivna körnings parametern. Om `environment` har angetts ignoreras den här posten. |
| `baseImageRegistry` | `base_image_registry` | Valfri. Avbildnings register som innehåller bas avbildningen. Om `environment` har angetts ignoreras den här posten.|
| `cudaVersion` | `cuda_version` | Valfri. Version av CUDA som ska installeras för avbildningar som behöver GPU-stöd. GPU-avbildningen måste användas på en Azure-tjänst. Till exempel Azure Container Instances, Azure Machine Learning Compute, Azure Virtual Machines och Azure Kubernetes-tjänsten. Versioner som stöds är 9,0, 9,1 och 10,0. Om `enable_gpu` har angetts är standardvärdet 9,1. Om `environment` har angetts ignoreras den här posten. |
| `description` | `description` | En beskrivning av avbildningen. Om `environment` har angetts ignoreras den här posten.  |
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

Du kan inkludera fullständiga specifikationer för en Azure Machine Learning [miljö](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) i konfigurations filen för konfigurations filen. Om den här miljön inte finns i arbets ytan skapas den av Azure Machine Learning. Annars kommer Azure Machine Learning uppdatera miljön om det behövs. Följande JSON är ett exempel:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

Du kan också använda en befintlig Azure Machine Learning [miljö](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) i separata CLI-parametrar och ta bort nyckeln "miljö" från konfigurations filen för konfigurations filen. Använd-e för miljö namnet och--ev för miljö versionen. Om du inte anger--EV kommer den senaste versionen att användas. Här är ett exempel på en konfigurations fil för uppdelning:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

Följande kommando visar hur du distribuerar en modell med hjälp av den tidigare konfigurations filen för härledning (med namnet myInferenceConfig. JSON). 

Den använder också den senaste versionen av en befintlig Azure Machine Learnings [miljö](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) (med namnet azureml – minimal).

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```
