---
title: Distribuera modeller till Azure Container Instances
titleSuffix: Azure Machine Learning
description: Lär dig hur du distribuerar dina Azure Machine Learning-modeller som en webbtjänst med Azure Container Instances.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 12/27/2019
ms.openlocfilehash: d460112394d7c7b7d2da4e8af41c0085b67226ec
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475474"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Distribuera en modell till Azure Container Instances
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Lär dig hur du använder Azure Machine Learning för att distribuera en modell som en webbtjänst på Azure Container Instances (ACI). Använd Azure Container Instances om något av följande villkor är sant:

- Du måste snabbt distribuera och validera din modell. Du behöver inte skapa ACI-behållare i förväg. De skapas som en del av distributionsprocessen.
- Du testar en modell som är under utveckling. 

Information om kvot- och regiontillgänglighet för ACI finns i artikeln [Kvoter och regiontillgänglighet för Azure Container Instances.](https://docs.microsoft.com/azure/container-instances/container-instances-quotas)

## <a name="prerequisites"></a>Krav

- En Azure Machine Learning-arbetsyta. Mer information finns i [Skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

- En maskininlärningsmodell som är registrerad på arbetsytan. Om du inte har en registrerad modell läser du [Hur och var du ska distribuera modeller](how-to-deploy-and-where.md).

- [Azure CLI-tillägget för machine learning-tjänsten,](reference-azure-machine-learning-cli.md) [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)eller [Azure Machine Learning Visual Studio Code-tillägget](tutorial-setup-vscode-extension.md).

- __Python-kodavsnitten__ i den här artikeln förutsätter att följande variabler har angetts:

    * `ws`- Ställ in på din arbetsyta.
    * `model`- Ställ in på din registrerade modell.
    * `inference_config`- Ställ in på inferenskonfigurationen för modellen.

    Mer information om hur du anger dessa variabler finns i [Hur och var modeller ska distribueras](how-to-deploy-and-where.md).

- __CLI-kodavsnitten__ i den här artikeln förutsätter `inferenceconfig.json` att du har skapat ett dokument. Mer information om hur du skapar det här dokumentet finns i [Hur och var du kan distribuera modeller](how-to-deploy-and-where.md).

## <a name="deploy-to-aci"></a>Distribuera till ACI

Om du vill distribuera en modell till Azure Container Instances skapar du en __distributionskonfiguration__ som beskriver de beräkningsresurser som behövs. Till exempel antal kärnor och minne. Du behöver också en __slutledningskonfiguration__, som beskriver den miljö som behövs för att vara värd för modellen och webbtjänsten. Mer information om hur du skapar inferenskonfigurationen finns i [Hur och var du kan distribuera modeller](how-to-deploy-and-where.md).

### <a name="using-the-sdk"></a>Med SDK

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Mer information om de klasser, metoder och parametrar som används i det här exemplet finns i följande referensdokument:

* [AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Model.deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webbtjänst.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Använda CLI

Om du vill distribuera med CLI använder du följande kommando. Ersätt `mymodel:1` med namn och version av den registrerade modellen. Ersätt `myservice` med namnet för att ge den här tjänsten:

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

Mer information finns i az [ml-modellens distributionsreferens.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) 

## <a name="using-vs-code"></a>Använda VS Code

Se [distribuera dina modeller med VS-kod](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> Du behöver inte skapa en ACI-behållare för att testa i förväg. ACI-behållare skapas efter behov.

## <a name="update-the-web-service"></a>Uppdatera webbtjänsten

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Nästa steg

* [Distribuera en modell med en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md)
* [Felsökning av distribution](how-to-troubleshoot-deployment.md)
* [Använda TLS för att skydda en webbtjänst via Azure Machine Learning](how-to-secure-web-service.md)
* [Använda en ML-modell som distribueras som en webbtjänst](how-to-consume-web-service.md)
* [Övervaka dina Azure Machine Learning-modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)
