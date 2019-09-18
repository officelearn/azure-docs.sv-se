---
title: Distribuera modeller till Azure Kubernetes-tjänsten
titleSuffix: Azure Machine Learning
description: Lär dig hur du distribuerar dina Azure Machine Learning modeller som en webb tjänst med Azure Kubernetes-tjänsten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/08/2019
ms.openlocfilehash: c32560f7bb182ac347e9e5a71b53b57cf80fac38
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034618"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Distribuera en modell till ett Azure Kubernetes service-kluster

Lär dig hur du använder Azure Machine Learning för att distribuera en modell som en webb tjänst på Azure Kubernetes service (AKS). Azure Kubernetes-tjänsten är lämplig för storskaliga produktions distributioner. Använd Azure Kubernetes-tjänsten om du behöver en eller flera av följande funktioner:

- __Snabb svars tid__.
- Automatisk __skalning__ av den distribuerade tjänsten.
- Alternativ för __maskin varu acceleration__ , t. ex. GPU och fält-programmerbara grind mat ris (FPGA).

> [!IMPORTANT]
> Kluster-skalning tillhandahålls inte via Azure Machine Learning SDK. Mer information om hur du skalar noderna i ett AKS-kluster finns i [skala antalet noder i ett AKS-kluster](../../aks/scale-cluster.md).

När du distribuerar till Azure Kubernetes-tjänsten distribuerar du till ett AKS-kluster som är __anslutet till din arbets yta__. Det finns två sätt att ansluta ett AKS-kluster till din arbets yta:

* Skapa AKS-klustret med hjälp av Azure Machine Learning SDK, Machine Learning CLI, [Azure Portal](https://portal.azure.com) -eller [arbets ytans landnings sida (för hands version)](https://ml.azure.com). Den här processen ansluter automatiskt klustret till arbets ytan.
* Koppla ett befintligt AKS-kluster till din Azure Machine Learning-arbetsyta. Ett kluster kan kopplas med hjälp av Azure Machine Learning SDK, Machine Learning CLI eller Azure Portal.

> [!IMPORTANT]
> Processen för att skapa eller bifogad fil är en engångs uppgift. När ett AKS-kluster är anslutet till arbets ytan kan du använda det för distributioner. Du kan koppla bort eller ta bort AKS-klustret om du inte längre behöver det. När detatched eller tagits bort kommer du inte längre att kunna distribuera till klustret.

## <a name="prerequisites"></a>Förutsättningar

- En Azure Machine Learning-arbetsyta. Mer information finns i [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

- En Machine Learning-modell som registrerats i din arbets yta. Om du inte har en registrerad modell, se [hur och var modeller ska distribueras](how-to-deploy-and-where.md).

- [Azure CLI-tillägget för Machine Learning-tjänst](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)eller [Azure Machine Learning Visual Studio Code-tillägget](how-to-vscode-tools.md).

- I __python__ -kodfragmenten i den här artikeln förutsätter vi att följande variabler har angetts:

    * `ws`– Ställ in på din arbets yta.
    * `model`– Ställ in på din registrerade modell.
    * `inference_config`-Ange som modellens konfigurations konfiguration.

    Mer information om hur du ställer in dessa variabler finns i [hur och var modeller ska distribueras](how-to-deploy-and-where.md).

- __CLI__ -kodfragmenten i den här artikeln förutsätter att du har `inferenceconfig.json` skapat ett dokument. Mer information om hur du skapar det här dokumentet finns i [så här distribuerar du modeller](how-to-deploy-and-where.md).

## <a name="create-a-new-aks-cluster"></a>Skapa ett nytt AKS-kluster

**Tids uppskattning**: Ungefär 20 minuter.

Att skapa eller ansluta ett AKS-kluster är en process för arbets ytan. Du kan återanvända det här klustret för flera distributioner. Om du tar bort klustret eller resurs gruppen som innehåller den måste du skapa ett nytt kluster nästa gången du behöver distribuera. Du kan ha flera AKS-kluster kopplade till din arbets yta.

> [!TIP]
> Om du vill skydda ditt AKS-kluster med hjälp av ett Azure-Virtual Network måste du först skapa det virtuella nätverket. Mer information finns i [säker experimentering och härledning med Azure Virtual Network](how-to-enable-virtual-network.md#aksvnet).

Om du vill skapa ett AKS-kluster för __utveckling__, __validering__och __testning__ i stället för produktion kan du ange ett __kluster syfte__ för __dev-test__.

> [!WARNING]
> Om du ställer `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`in är det kluster som skapas inte lämpligt för trafik på produktions nivå och kan öka eventuella härlednings tider. Utvecklings-och test kluster garanterar inte heller fel tolerans. Vi rekommenderar minst 2 virtuella processorer för dev/test-kluster.

Följande exempel visar hur du skapar ett nytt AKS-kluster med hjälp av SDK och CLI:

**Använda SDK: n**

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> För [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), om du väljer anpassade värden för `agent_count` och `vm_size`, och `cluster_purpose` inte, `DEV_TEST`och inte, måste du se `agent_count` till att multiplicerat `vm_size` med är större än eller lika med 12 virtuella processorer. Om du till exempel använder en `vm_size` av "Standard_D3_v2", som har 4 virtuella processorer, bör du välja en `agent_count` av 3 eller fler.
>
> Azure Machine Learning SDK ger inte stöd för skalning av ett AKS-kluster. Om du vill skala noderna i klustret använder du användar gränssnittet för ditt AKS-kluster i Azure Portal. Du kan bara ändra antalet noder, inte klustrets virtuella dator storlek.

Mer information om klasser, metoder och parametrar som används i det här exemplet finns i följande referens dokument:

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.provisioning_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget. Create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**Använda CLI**

```azurecli
az ml computetarget create aks -n myaks
```

Mer information finns i [AZ ml computetarget Create be](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) reference.

## <a name="attach-an-existing-aks-cluster"></a>Koppla ett befintligt AKS-kluster

**Tids uppskattning:** Cirka 5 minuter.

Om du redan har AKS-kluster i din Azure-prenumeration och det är version 1.12. # # kan du använda det för att distribuera avbildningen.

> [!TIP]
> Det befintliga AKS-klustret kan finnas i en Azure-region än din Azure Machine Learning-arbetsyta.
>
> Om du vill skydda ditt AKS-kluster med hjälp av ett Azure-Virtual Network måste du först skapa det virtuella nätverket. Mer information finns i [säker experimentering och härledning med Azure Virtual Network](how-to-enable-virtual-network.md#aksvnet).

> [!WARNING]
> När du kopplar ett AKS-kluster till en arbets yta kan du definiera hur du ska använda klustret genom att ange `cluster_purpose` parametern.
>
> Om du inte anger `cluster_purpose` parametern eller anger `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`så måste klustret ha minst 12 virtuella processorer tillgängliga.
>
> Om du ställer `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`in behöver klustret inte ha 12 virtuella processorer. Vi rekommenderar minst 2 virtuella processorer för utveckling/testning. Men ett kluster som har kon figurer ATS för utveckling/testning är inte lämpligt för trafik på produktions nivå och kan öka eventuella härlednings tider. Utvecklings-och test kluster garanterar inte heller fel tolerans.

Mer information om hur du skapar ett AKS-kluster med hjälp av Azure CLI eller portalen finns i följande artiklar:

* [Skapa ett AKS-kluster (CLI)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Skapa ett AKS-kluster (portal)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

Följande exempel visar hur du kopplar en befintlig AKS-1.12. # #-kluster till din arbets yta:

**Använda SDK: n**

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'myexistingcluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'myaks', attach_config)
```

Mer information om klasser, metoder och parametrar som används i det här exemplet finns i följande referens dokument:

* [AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute. attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**Använda CLI**

Om du vill koppla ett befintligt kluster med CLI måste du hämta resurs-ID för det befintliga klustret. Använd följande kommando för att hämta det här värdet. Ersätt `myexistingcluster` med namnet på ditt AKS-kluster. Ersätt `myresourcegroup` med resurs gruppen som innehåller klustret:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Det här kommandot returnerar ett värde som liknar följande text:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Använd följande kommando för att koppla det befintliga klustret till din arbets yta. Ersätt `aksresourceid` med det värde som returnerades av föregående kommando. Ersätt `myresourcegroup` med den resurs grupp som innehåller din arbets yta. Ersätt `myworkspace` med namnet på din arbets yta.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Mer information finns i [AZ ml computetarget Attach AKS](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) reference.

## <a name="deploy-to-aks"></a>Distribuera till AKS

Om du vill distribuera en modell till Azure Kubernetes-tjänsten skapar du en __distributions konfiguration__ som beskriver de beräknings resurser som behövs. Till exempel antal kärnor och minne. Du behöver också en __konfiguration__med en konfiguration som beskriver den miljö som krävs för att vara värd för modellen och webb tjänsten. Mer information om hur du skapar en konfigurations konfiguration finns i [hur och var modeller ska distribueras](how-to-deploy-and-where.md).

### <a name="using-the-sdk"></a>Med SDK

```python
from azureml.core.webservice import AksWebservice, Webservice
from azureml.core.model import Model

aks_target = AksCompute(ws,"myaks")
# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

Mer information om klasser, metoder och parametrar som används i det här exemplet finns i följande referens dokument:

* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py)
* [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [Modell. Deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)
* [WebService. wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Använda CLI

Använd följande kommando för att distribuera med hjälp av CLI. Ersätt `myaks` med namnet på AKS Compute Target. Ersätt `mymodel:1` med namnet och versionen för den registrerade modellen. Ersätt `myservice` med namnet för att ge den här tjänsten:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aks-deploy-config.md)]

Mer information finns i [distributions referens för AZ ml-modellen](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) . 

### <a name="using-vs-code"></a>Använda VS Code

Information om hur du använder VS Code finns i [distribuera till AKS via vs Code-tillägget](how-to-vscode-tools.md#deploy-and-manage-models).

> [!IMPORTANT] 
> Att distribuera via VS Code kräver att AKS-klustret skapas eller kopplas till din arbets yta i förväg.

## <a name="web-service-authentication"></a>Webb tjänstens autentisering

När du distribuerar till Azure Kubernetes-tjänsten aktive ras __nyckelbaserad__ autentisering som standard. Du kan också aktivera __tokenbaserad__ autentisering. Token-baserad autentisering kräver att klienter använder ett Azure Active Directory konto för att begära en autentiseringstoken, som används för att göra förfrågningar till den distribuerade tjänsten.

Om du vill __inaktivera__ autentisering ställer `auth_enabled=False` du in parametern när du skapar distributions konfigurationen. I följande exempel inaktive ras autentisering med hjälp av SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Information om hur du autentiserar från ett klient program finns i [använda en Azure Machine Learning modell som distribueras som en webb tjänst](how-to-consume-web-service.md).

### <a name="authentication-with-keys"></a>Autentisering med nycklar

Om du har aktiverat autentisering av `get_keys` nycklar kan du använda-metoden för att hämta en primär nyckel och en sekundär autentiseringsnyckel:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Om du behöver återskapa en nyckel använder du[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

### <a name="authentication-with-tokens"></a>Autentisering med token

Om du vill aktivera token- `token_auth_enabled=True` autentisering anger du parametern när du skapar eller uppdaterar en distribution. I följande exempel aktive ras token-autentisering med SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Om token-autentisering har Aktiver ATS kan `get_token` du använda metoden för att hämta en JWT-token och dess förfallo tid för token:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Du måste begära en ny token efter det att token `refresh_by` har uppnåtts.
>
> Microsoft rekommenderar starkt att du skapar din Azure Machine Learning arbets yta i samma region som ditt Azure Kubernetes service-kluster. För att autentisera med en token kommer webb tjänsten att ringa till den region där din Azure Machine Learning arbets yta skapas. Om arbets ytans region inte är tillgänglig kan du inte hämta en token för din webb tjänst även om klustret finns i en annan region än din arbets yta. Detta leder till att tokenbaserad autentisering inte är tillgängligt förrän arbets ytans region är tillgänglig igen. Dessutom ökar avståndet mellan klustrets region och arbets ytans region, desto längre tid tar det att hämta en token.

## <a name="update-the-web-service"></a>Uppdatera webbtjänsten

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Nästa steg

* [Säker experimentering och härledning i ett virtuellt nätverk](how-to-enable-virtual-network.md)
* [Så här distribuerar du en modell med en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md)
* [Distributions fel sökning](how-to-troubleshoot-deployment.md)
* [Skydda Azure Machine Learning-webbtjänster med SSL](how-to-secure-web-service.md)
* [Använd en ML-modell som distribueras som en webbtjänst](how-to-consume-web-service.md)
* [Övervaka dina Azure Machine Learning modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)
