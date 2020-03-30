---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 5102e8f75da14c58e948e81aaa418539dd18869a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159427"
---
Posterna i `inferenceconfig.json` dokumentöversikten till parametrarna för klassen [InferenceConfig.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) I följande tabell beskrivs mappningen mellan entiteterna i JSON-dokumentet och parametrarna för metoden:

| JSON-enhet | Parametern Metod | Beskrivning |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Sökväg till en lokal fil som innehåller koden som ska köras för avbildningen. |
| `sourceDirectory` | `source_directory` | Valfri. Sökväg till mappar som innehåller alla filer för att skapa bilden, vilket gör det enkelt att komma åt alla filer i den här mappen eller undermappen. Du kan ladda upp en hel mapp från den lokala datorn som beroenden för webbtjänsten. Dina entry_script, conda_file och extra_docker_file_steps sökvägar är relativa banor till den source_directory banan. |
| `environment` | `environment` | Valfri.  Azure Machine [Learning-miljö](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py).|

Du kan inkludera fullständiga specifikationer för en Azure Machine [Learning-miljö](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) i inferenskonfigurationsfilen. Om den här miljön inte finns på din arbetsyta skapas den i Azure Machine Learning. Annars uppdaterar Azure Machine Learning miljön om det behövs. Följande JSON är ett exempel:

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

Du kan också använda en befintlig Azure Machine [Learning-miljö](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) i separerade CLI-parametrar och ta bort "miljö"-nyckeln från inferenskonfigurationsfilen. Använd -e för miljönamnet och --ev för miljöversionen. Om du inte anger --ev används den senaste versionen. Här är ett exempel på en inferenskonfigurationsfil:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

Följande kommando visar hur du distribuerar en modell med hjälp av den tidigare inferenskonfigurationsfilen (med namnet myInferenceConfig.json). 

Den använder också den senaste versionen av en befintlig Azure Machine [Learning-miljö](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) (med namnet AzureML-Minimal).

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```
