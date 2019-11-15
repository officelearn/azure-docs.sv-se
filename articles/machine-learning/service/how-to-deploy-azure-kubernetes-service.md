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
ms.date: 11/06/2019
ms.openlocfilehash: de746d0b370de2d238c1143c48b75c1505cd9dc0
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091487"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Distribuera en modell till ett Azure Kubernetes service-kluster
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Lär dig hur du använder Azure Machine Learning för att distribuera en modell som en webb tjänst på Azure Kubernetes service (AKS). Azure Kubernetes-tjänsten är lämplig för storskaliga produktions distributioner. Använd Azure Kubernetes-tjänsten om du behöver en eller flera av följande funktioner:

- __Snabb svars tid__.
- Automatisk __skalning__ av den distribuerade tjänsten.
- Alternativ för __maskin varu acceleration__ , t. ex. GPU och fält-programmerbara grind mat ris (FPGA).

> [!IMPORTANT]
> Kluster skalning tillhandahålls inte via Azure Machine Learning SDK. Mer information om hur du skalar noderna i ett AKS-kluster finns i [skala antalet noder i ett AKS-kluster](../../aks/scale-cluster.md).

När du distribuerar till Azure Kubernetes-tjänsten distribuerar du till ett AKS-kluster som är __anslutet till din arbets yta__. Det finns två sätt att ansluta ett AKS-kluster till din arbets yta:

* Skapa AKS-klustret med hjälp av Azure Machine Learning SDK, Machine Learning CLI eller [Azure Machine Learning Studio](https://ml.azure.com). Den här processen ansluter automatiskt klustret till arbets ytan.
* Koppla ett befintligt AKS-kluster till din Azure Machine Learning-arbetsyta. Ett kluster kan kopplas med hjälp av Azure Machine Learning SDK, Machine Learning CLI eller Azure Machine Learning Studio.

> [!IMPORTANT]
> Processen för att skapa eller bifogad fil är en engångs uppgift. När ett AKS-kluster är anslutet till arbets ytan kan du använda det för distributioner. Du kan koppla bort eller ta bort AKS-klustret om du inte längre behöver det. När detatched eller tagits bort kommer du inte längre att kunna distribuera till klustret.

## <a name="prerequisites"></a>Krav

- En Azure Machine Learning-arbetsyta. Mer information finns i [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

- En Machine Learning-modell som registrerats i din arbets yta. Om du inte har en registrerad modell, se [hur och var modeller ska distribueras](how-to-deploy-and-where.md).

- [Azure CLI-tillägget för Machine Learning-tjänst](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)eller [Azure Machine Learning Visual Studio Code-tillägget](how-to-vscode-tools.md).

- I __python__ -kodfragmenten i den här artikeln förutsätter vi att följande variabler har angetts:

    * `ws`-Ställ in på din arbets yta.
    * `model`-Ställ in på din registrerade modell.
    * `inference_config`-Ställ in på modellens konfigurations konfiguration.

    Mer information om hur du ställer in dessa variabler finns i [hur och var modeller ska distribueras](how-to-deploy-and-where.md).

- __CLI__ -kodfragmenten i den här artikeln förutsätter att du har skapat ett `inferenceconfig.json`-dokument. Mer information om hur du skapar det här dokumentet finns i [så här distribuerar du modeller](how-to-deploy-and-where.md).

## <a name="create-a-new-aks-cluster"></a>Skapa ett nytt AKS-kluster

**Uppskattad tidsåtgång**: cirka 20 minuter.

Att skapa eller ansluta ett AKS-kluster är en process för arbets ytan. Du kan återanvända det här klustret för flera distributioner. Om du tar bort klustret eller resurs gruppen som innehåller den måste du skapa ett nytt kluster nästa gången du behöver distribuera. Du kan ha flera AKS-kluster kopplade till din arbets yta.

> [!TIP]
> Om du vill skydda ditt AKS-kluster med hjälp av ett Azure-Virtual Network måste du först skapa det virtuella nätverket. Mer information finns i [säker experimentering och härledning med Azure Virtual Network](how-to-enable-virtual-network.md#aksvnet).

Om du vill skapa ett AKS-kluster för __utveckling__, __validering__och __testning__ i stället för produktion kan du ange ett __kluster syfte__ för __dev-test__.

> [!WARNING]
> Om du ställer in `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`är det kluster som skapas inte lämpligt för trafik på produktions nivå och kan öka eventuella härlednings tider. Utvecklings-och test kluster garanterar inte heller fel tolerans. Vi rekommenderar minst 2 virtuella processorer för dev/test-kluster.

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
> Om du i [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py)väljer anpassade värden för `agent_count` och `vm_size`och `cluster_purpose` inte är `DEV_TEST`måste du se till att `agent_count` multiplicerat med `vm_size` är större än eller lika med 12 virtuella processorer. Om du till exempel använder en `vm_size` av "Standard_D3_v2", som har 4 virtuella processorer, bör du välja en `agent_count` på 3 eller senare.
>
> Azure Machine Learning SDK ger inte stöd för skalning av ett AKS-kluster. Om du vill skala noderna i klustret använder du användar gränssnittet för ditt AKS-kluster i Azure Machine Learning Studio. Du kan bara ändra antalet noder, inte klustrets virtuella dator storlek.

Mer information om klasser, metoder och parametrar som används i det här exemplet finns i följande referens dokument:

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute. provisioning_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget. Create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget. wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**Använda CLI**

```azurecli
az ml computetarget create aks -n myaks
```

Mer information finns i [AZ ml computetarget Create be](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) reference.

## <a name="attach-an-existing-aks-cluster"></a>Koppla ett befintligt AKS-kluster

**Tids uppskattning:** Cirka 5 minuter.

Om du redan har AKS-kluster i din Azure-prenumeration och det är lägre än version 1,14 kan du använda det för att distribuera avbildningen.

> [!TIP]
> Det befintliga AKS-klustret kan finnas i en Azure-region än din Azure Machine Learning-arbetsyta.
>
> Om du vill skydda ditt AKS-kluster med hjälp av ett Azure-Virtual Network måste du först skapa det virtuella nätverket. Mer information finns i [säker experimentering och härledning med Azure Virtual Network](how-to-enable-virtual-network.md#aksvnet).

När du kopplar ett AKS-kluster till en arbets yta kan du definiera hur du ska använda klustret genom att ange parametern `cluster_purpose`.

Om du inte anger parametern `cluster_purpose` eller anger `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`måste klustret ha minst 12 virtuella processorer tillgängliga.

Om du anger `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`behöver klustret inte ha 12 virtuella processorer. Vi rekommenderar minst 2 virtuella processorer för utveckling/testning. Men ett kluster som har kon figurer ATS för utveckling/testning är inte lämpligt för trafik på produktions nivå och kan öka eventuella härlednings tider. Utvecklings-och test kluster garanterar inte heller fel tolerans.

> [!WARNING]
> Skapa inte flera, samtidiga bilagor till samma AKS-kluster från din arbets yta. Du kan till exempel koppla ett AKS-kluster till en arbets yta med två olika namn. Varje ny bilaga kommer att dela upp de tidigare befintliga bifogade filerna.
>
> Om du vill koppla ett AKS-kluster på nytt, t. ex. ändra SSL eller någon annan kluster konfigurations inställning, måste du först ta bort den befintliga bilagan med hjälp av [AksCompute. Detached ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--).

Mer information om hur du skapar ett AKS-kluster med hjälp av Azure CLI eller portalen finns i följande artiklar:

* [Skapa ett AKS-kluster (CLI)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Skapa ett AKS-kluster (portal)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

Följande exempel visar hur du ansluter ett befintligt AKS-kluster till din arbets yta:

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

* [AksCompute. attach_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
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

Använd följande kommando för att koppla det befintliga klustret till din arbets yta. Ersätt `aksresourceid` med det värde som returneras av föregående kommando. Ersätt `myresourcegroup` med resurs gruppen som innehåller din arbets yta. Ersätt `myworkspace` med namnet på din arbets yta.

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
* [AksWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
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

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Distribuera modeller till AKS med hjälp av kontrollerad distribution (för hands version)
Analysera och uppgradera modell versioner på ett kontrollerat sätt med hjälp av slut punkter. Distribuera upp till 6 versioner bakom en enda slut punkt och konfigurera% av bedömnings trafik till varje distribuerad version. Du kan aktivera App Insights om du vill visa drifts mått för slut punkter och distribuerade versioner.

### <a name="create-an-endpoint"></a>Skapa en slutpunkt
När du är redo att distribuera dina modeller skapar du en poäng slut punkt och distribuerar din första version. I steget nedan visas hur du distribuerar och skapar slut punkten med hjälp av SDK. Den första distributionen definieras som standard version, vilket innebär att ospecificerad trafik percentil över alla versioner kommer att gå till standard versionen.  

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')
namespace_name= endpointnamespace 
# define the endpoint and version name
endpoint_name = "mynewendpoint",
version_name= "versiona",
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = true, 
                                                              tags = {'sckitlearn':'demo'},
                                                              decription = testing versions,
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>Uppdatera och lägga till versioner till en slut punkt

Lägg till en annan version till din slut punkt och konfigurera bedömnings trafikens percentil till versionen. Det finns två typer av versioner, en kontroll och en behandlings version. Det kan finnas flera behandlings versioner som hjälper dig att jämföra med en enda kontroll version. 

 ```python
from azureml.core.webservice import AksEndpoint

# add another model deployment to the same endpoint as above
version_name_add = "versionb" 
endpoint.create_version(version_name = version_name_add, 
                        inference_config=inference_config,
                        models=[model], 
                        tags = {'modelVersion':'b'}, 
                        description = "my second version", 
                        traffic_percentile = 10)
```

Uppdatera befintliga versioner eller ta bort dem i en slut punkt. Du kan ändra versionens standard typ, kontroll typ och trafik percentilen. 
 
 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type 
endpoint.update_version(version_name=endpoint.versions["versionb"].name, 
                        description="my second version update", 
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)

# delete a version in an endpoint 
endpoint.delete_version(version_name="versionb")

```


## <a name="web-service-authentication"></a>Webb tjänstens autentisering

När du distribuerar till Azure Kubernetes-tjänsten aktive ras __nyckelbaserad__ autentisering som standard. Du kan också aktivera __tokenbaserad__ autentisering. Token-baserad autentisering kräver att klienter använder ett Azure Active Directory konto för att begära en autentiseringstoken, som används för att göra förfrågningar till den distribuerade tjänsten.

Om du vill __inaktivera__ autentisering ställer du in parametern `auth_enabled=False` när du skapar distributions konfigurationen. I följande exempel inaktive ras autentisering med hjälp av SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Information om hur du autentiserar från ett klient program finns i [använda en Azure Machine Learning modell som distribueras som en webb tjänst](how-to-consume-web-service.md).

### <a name="authentication-with-keys"></a>Autentisering med nycklar

Om du har aktiverat autentisering av nycklar kan du använda metoden `get_keys` för att hämta en primär och sekundär autentiseringsnyckel:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Om du behöver återskapa en nyckel använder du [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

### <a name="authentication-with-tokens"></a>Autentisering med token

Om du vill aktivera token-autentisering anger du parametern `token_auth_enabled=True` när du skapar eller uppdaterar en distribution. I följande exempel aktive ras token-autentisering med SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Om token-autentisering har Aktiver ATS kan du använda metoden `get_token` för att hämta en JWT-token och dess förfallo tid för token:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Du måste begära en ny token efter det att token `refresh_by` tid.
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
