---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/26/2019
ms.author: larryfr
ms.openlocfilehash: f1eee95cf35b831fc2a213044d700fd5afbdfc96
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997935"
---
Om du vill uppdatera webbtjänsten använder den `update` metoden. Du kan uppdatera webb tjänsten så att den använder en ny modell, ett Entry-skript eller beroenden som kan anges med en konfigurations konfiguration. Mer information finns i referensen för [WebService. Update](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-).

> [!IMPORTANT]
> När du skapar en ny version av en modell måste du manuellt uppdatera varje tjänst som du vill använda.

**Använda SDK: n**

Följande kod visar hur du använder SDK för att uppdatera modell-, miljö-och registrerings skriptet för en webb tjänst:

```python
from azureml.core import Environment
from azureml.core.webservice import Webservice
from azureml.core.model import Model, InferenceConfig

# register new model
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

# Use version 3 of the environment
deploy_env = Environment.get(workspace=ws,name="myenv",version="3")
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=deploy_env)

service_name = 'myservice'
# Retrieve existing service
service = Webservice(name=service_name, workspace=ws)



# Update to new model(s)
service.update(models=[new_model], inference_config=inference_config)
print(service.state)
print(service.get_logs())
```

**Använda CLI**

Du kan också uppdatera en webb tjänst med hjälp av ML CLI. I följande exempel visas hur du registrerar en ny modell och sedan uppdaterar webb tjänsten så att den använder den nya modellen:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> I det här exemplet används ett JSON-dokument för att skicka modell informationen från registrerings kommandot till kommandot Update.
>
> Om du vill uppdatera tjänsten för att använda ett nytt start skript eller en ny miljö skapar du en [konfigurations fil](/azure/machine-learning/service/reference-azure-machine-learning-cli#inference-configuration-schema) för `ic` en konfigurations fil och anger den med parametern.

Mer information finns i uppdaterings referens för [AZ ml-tjänsten](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update) .