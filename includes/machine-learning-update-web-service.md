---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: d36bf2db05113656a77e76ff900d95910f313c73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477263"
---
Om du vill uppdatera `update` en webbtjänst använder du metoden. Du kan uppdatera webbtjänsten så att den använder en ny modell, ett nytt postskript eller nya beroenden som kan anges i en slutledningskonfiguration. Mer information finns i dokumentationen för [Webservice.update](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-).

> [!IMPORTANT]
> När du skapar en ny version av en modell måste du uppdatera varje tjänst manuellt som du vill använda den.
>
> Du kan inte använda SDK för att uppdatera en webbtjänst som publicerats från Azure Machine Learning-designern.

**Med SDK**

Följande kod visar hur du använder SDK för att uppdatera modell-, miljö- och inmatningsskriptet för en webbtjänst:

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

Du kan också uppdatera en webbtjänst med hjälp av ML CLI. I följande exempel visas hur du registrerar en ny modell och sedan uppdaterar en webbtjänst för att använda den nya modellen:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> I det här exemplet används ett JSON-dokument för att skicka modellinformationen från registreringskommandot till uppdateringskommandot.
>
> Om du vill uppdatera tjänsten för att använda ett nytt postskript eller `ic` en ny postmiljö skapar du en [inferenskonfigurationsfil](/azure/machine-learning/service/reference-azure-machine-learning-cli#inference-configuration-schema) och anger den med parametern.

Mer information finns i dokumentationen för [uppdatering av az ml-tjänsten.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update)