---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/10/2019
ms.author: larryfr
ms.openlocfilehash: 469d87a828df19ca30260cada9dcea43859be9e0
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75901952"
---
Använd metoden `update` om du vill uppdatera en webb tjänst. Du kan uppdatera webb tjänsten så att den använder en ny modell, ett nytt registrerings skript eller nya beroenden som kan anges i en konfiguration för en konfiguration. Mer information finns i dokumentationen för [WebService. Update](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-).

> [!IMPORTANT]
> När du skapar en ny version av en modell måste du manuellt uppdatera varje tjänst som du vill använda.
>
> Du kan inte använda SDK för att uppdatera en webb tjänst som publicerats från Azure Machine Learning designer.

**Använda SDK: n**

Följande kod visar hur du använder SDK för att uppdatera modell-, miljö-och registrerings skriptet för en webb tjänst:

```python
from azureml.core import Environment
from azureml.core.webservice import Webservice
from azureml.core.model import Model, InferenceConfig

# Register new model.
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

# Use version 3 of the environment.
deploy_env = Environment.get(workspace=ws,name="myenv",version="3")
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=deploy_env)

service_name = 'myservice'
# Retrieve existing service.
service = Webservice(name=service_name, workspace=ws)



# Update to new model(s).
service.update(models=[new_model], inference_config=inference_config)
print(service.state)
print(service.get_logs())
```

**Använda CLI**

Du kan också uppdatera en webb tjänst med hjälp av ML CLI. I följande exempel visas hur du registrerar en ny modell och sedan uppdaterar en webb tjänst så att den använder den nya modellen:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> I det här exemplet används ett JSON-dokument för att skicka modell informationen från registrerings kommandot till kommandot Update.
>
> Om du vill uppdatera tjänsten för att använda ett nytt start skript eller en ny miljö skapar du en [konfigurations fil för en konfigurations fil](/azure/machine-learning/service/reference-azure-machine-learning-cli#inference-configuration-schema) och anger den med parametern `ic`.

Mer information finns i dokumentationen om [AZ ml-tjänst uppdatering](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update) .