---
title: Så här distribuerar du modeller till Azure Kubernetes Service
titleSuffix: Azure Machine Learning
description: Lär dig hur du distribuerar dina Azure Machine Learning-modeller som en webbtjänst med Azure Kubernetes Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 01/16/2020
ms.openlocfilehash: 792964f28ddb3fcb10932b8de9499a9c7027960f
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475391"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Distribuera en modell till ett Azure Kubernetes-tjänstkluster
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Lär dig hur du använder Azure Machine Learning för att distribuera en modell som en webbtjänst på Azure Kubernetes Service (AKS). Azure Kubernetes Service är bra för storskaliga produktionsdistributioner. Använd Azure Kubernetes-tjänsten om du behöver en eller flera av följande funktioner:

- __Snabb svarstid__.
- __Automatisk skalning__ av den distribuerade tjänsten.
- __Maskinvaruaccelerationsalternativ__ som GPU och fältprogrammerbara grindmatriser (FPGA).

> [!IMPORTANT]
> Klusterskalning tillhandahålls inte via Azure Machine Learning SDK. Mer information om skalning av noderna i ett AKS-kluster finns [i Skala antalet noder i ett AKS-kluster](../aks/scale-cluster.md).

När du distribuerar till Azure Kubernetes Service distribuerar du till ett AKS-kluster som är __anslutet till din arbetsyta__. Det finns två sätt att ansluta ett AKS-kluster till din arbetsyta:

* Skapa AKS-klustret med Azure Machine Learning SDK, Machine Learning CLI eller [Azure Machine Learning studio](https://ml.azure.com). Den här processen ansluter automatiskt klustret till arbetsytan.
* Bifoga ett befintligt AKS-kluster till din Azure Machine Learning-arbetsyta. Ett kluster kan kopplas till Azure Machine Learning SDK, Machine Learning CLI eller Azure Machine Learning studio.

> [!IMPORTANT]
> Skapande- eller bifogade filer är en engångsaktivitet. När ett AKS-kluster är anslutet till arbetsytan kan du använda det för distributioner. Du kan koppla från eller ta bort AKS-klustret om du inte längre behöver det. När du har tagits bort eller tagits bort kan du inte längre distribuera till klustret.

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

## <a name="create-a-new-aks-cluster"></a>Skapa ett nytt AKS-kluster

**Tidsuppskattning**: Cirka 20 minuter.

Att skapa eller bifoga ett AKS-kluster är en engångsprocess för arbetsytan. Du kan återanvända det här klustret för flera distributioner. Om du tar bort klustret eller resursgruppen som innehåller det måste du skapa ett nytt kluster nästa gång du behöver distribuera. Du kan ha flera AKS-kluster kopplade till arbetsytan.

> [!TIP]
> Om du vill skydda AKS-klustret med hjälp av ett Virtuellt Azure-nätverk måste du först skapa det virtuella nätverket. Mer information finns [i Säker experiment och slutledning med Azure Virtual Network](how-to-enable-virtual-network.md#aksvnet).

Om du vill skapa ett AKS-kluster för __utveckling,__ __validering__och __testning__ i stället för produktion kan du ange __klustersyftet__ att __testa__.

> [!WARNING]
> Om du `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`anger är klustret som skapas inte lämpligt för trafik på produktionsnivå och kan öka inferenstiderna. Utvecklings-/testkluster garanterar inte heller feltolerans. Vi rekommenderar minst 2 virtuella processorer för utvecklings-/testkluster.

Följande exempel visar hur du skapar ett nytt AKS-kluster med SDK och CLI:

**Med SDK**

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
> För [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), om du `agent_count` väljer `vm_size`anpassade `cluster_purpose` värden `DEV_TEST`för och , `agent_count` och inte `vm_size` är , måste du se multiplicerat med är större än eller lika med 12 virtuella processorer. Om du till exempel `vm_size` använder en av "Standard_D3_v2", som har 4 virtuella `agent_count` processorer, bör du välja en av 3 eller fler.
>
> Azure Machine Learning SDK ger inte stöd för skalning av ett AKS-kluster. Om du vill skala noderna i klustret använder du användargränssnittet för AKS-klustret i Azure Machine Learning-studion. Du kan bara ändra antalet noder, inte klustrets vm-storlek.

Mer information om de klasser, metoder och parametrar som används i det här exemplet finns i följande referensdokument:

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget.create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**Använda CLI**

```azurecli
az ml computetarget create aks -n myaks
```

Mer information finns i [az ml computetarget create aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) reference.

## <a name="attach-an-existing-aks-cluster"></a>Koppla ett befintligt AKS-kluster

**Tidsuppskattning:** Cirka 5 minuter.

Om du redan har AKS-kluster i din Azure-prenumeration och det är version 1.17 eller lägre, kan du använda det för att distribuera avbildningen.

> [!TIP]
> Det befintliga AKS-klustret kan finnas i en annan Azure-region än arbetsytan Azure Machine Learning.
>
> Om du vill skydda AKS-klustret med hjälp av ett Virtuellt Azure-nätverk måste du först skapa det virtuella nätverket. Mer information finns [i Säker experiment och slutledning med Azure Virtual Network](how-to-enable-virtual-network.md#aksvnet).

När du kopplar ett AKS-kluster till en arbetsyta kan du definiera `cluster_purpose` hur du ska använda klustret genom att ange parametern.

Om du inte `cluster_purpose` anger parametern `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`eller anger måste klustret ha minst 12 virtuella processorer tillgängliga.

Om du `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`anger behöver klustret inte ha 12 virtuella processorer. Vi rekommenderar minst 2 virtuella processorer för utveckling/test. Ett kluster som har konfigurerats för utveckling/test är dock inte lämpligt för trafik på produktionsnivå och kan öka inferenstiderna. Utvecklings-/testkluster garanterar inte heller feltolerans.

> [!WARNING]
> Skapa inte flera, samtidiga bilagor till samma AKS-kluster från arbetsytan. Bifoga till exempel ett AKS-kluster till en arbetsyta med två olika namn. Varje ny bilaga kommer att bryta den tidigare befintliga bilagan(er).
>
> Om du vill återankoppla ett AKS-kluster, till exempel för att ändra TLS eller annan klusterkonfigurationsinställning, måste du först ta bort den befintliga bilagan med [AksCompute.ta bort()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--).

Mer information om hur du skapar ett AKS-kluster med Hjälp av Azure CLI eller portal finns i följande artiklar:

* [Skapa ett AKS-kluster (CLI)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Skapa ett AKS-kluster (portal)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

I följande exempel visas hur du kopplar ett befintligt AKS-kluster till din arbetsyta:

**Med SDK**

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

Mer information om de klasser, metoder och parametrar som används i det här exemplet finns i följande referensdokument:

* [AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**Använda CLI**

Om du vill koppla ett befintligt kluster med CLI måste du hämta resurs-ID:et för det befintliga klustret. Använd följande kommando för att hämta det här värdet. Ersätt `myexistingcluster` med namnet på AKS-klustret. Ersätt `myresourcegroup` med resursgruppen som innehåller klustret:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Det här kommandot returnerar ett värde som liknar följande text:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Om du vill koppla det befintliga klustret till arbetsytan använder du följande kommando. Ersätt `aksresourceid` med det värde som returnerades av föregående kommando. Ersätt `myresourcegroup` med resursgruppen som innehåller arbetsytan. Ersätt `myworkspace` med arbetsytenamnet.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Mer information finns i [az ml computetarget attach aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) reference.

## <a name="deploy-to-aks"></a>Distribuera till AKS

Om du vill distribuera en modell till Azure Kubernetes Service skapar du en __distributionskonfiguration__ som beskriver de beräkningsresurser som behövs. Till exempel antal kärnor och minne. Du behöver också en __slutledningskonfiguration__, som beskriver den miljö som behövs för att vara värd för modellen och webbtjänsten. Mer information om hur du skapar inferenskonfigurationen finns i [Hur och var du kan distribuera modeller](how-to-deploy-and-where.md).

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

Mer information om de klasser, metoder och parametrar som används i det här exemplet finns i följande referensdokument:

* [AksCompute (akscompute)](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py)
* [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [Model.deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webbtjänst.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Använda CLI

Om du vill distribuera med CLI använder du följande kommando. Ersätt `myaks` med namnet på AKS-beräkningsmålet. Ersätt `mymodel:1` med namn och version av den registrerade modellen. Ersätt `myservice` med namnet för att ge den här tjänsten:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

Mer information finns i az [ml-modellens distributionsreferens.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)

### <a name="using-vs-code"></a>Använda VS Code

Information om hur du använder VS-kod finns [i distribuera till AKS via vs-kodtillägget](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> Distribuera via VS-kod kräver att AKS-klustret skapas eller kopplas till din arbetsyta i förväg.

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Distribuera modeller till AKS med kontrollerad distribution (förhandsgranskning)
Analysera och befordra modellversioner på ett kontrollerat sätt med hjälp av slutpunkter. Distribuera upp till 6 versioner bakom en enda slutpunkt och konfigurera % av poängtrafik till varje distribuerad version. Du kan aktivera appinsikter för att visa operativa mått för slutpunkter och distribuerade versioner.

### <a name="create-an-endpoint"></a>Skapa en slutpunkt
När du är redo att distribuera dina modeller skapar du en bedömningsslutpunkt och distribuerar din första version. Steget nedan visar hur du distribuerar och skapar slutpunkten med hjälp av SDK. Den första distributionen definieras som standardversionen, vilket innebär att ospecificerad trafik percentil i alla versioner kommer att gå till standardversionen.  

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
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>Uppdatera och lägga till versioner i en slutpunkt

Lägg till en annan version till slutpunkten och konfigurera poängsättningstrafik percentilen går till versionen. Det finns två typer av versioner, en kontroll och en behandlingsversion. Det kan finnas flera behandlingsversioner för att jämföra med en enda kontrollversion.

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

Uppdatera befintliga versioner eller ta bort dem i en slutpunkt. Du kan ändra versionens standardtyp, kontrolltyp och trafik percentilen.

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


## <a name="web-service-authentication"></a>Autentisering av webbtjänster

Vid distribution till Azure Kubernetes Service aktiveras __nyckelbaserad__ autentisering som standard. Du kan också aktivera __tokenbaserad__ autentisering. Tokenbaserad autentisering kräver att klienter använder ett Azure Active Directory-konto för att begära en autentiseringstoken, som används för att göra begäranden till den distribuerade tjänsten.

Om __disable__ du vill `auth_enabled=False` inaktivera autentisering anger du parametern när du skapar distributionskonfigurationen. I följande exempel inaktiveras autentisering med SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Information om hur du autentiserar från ett klientprogram finns i [modellen Förbruka en Azure Machine Learning som distribueras som en webbtjänst](how-to-consume-web-service.md).

### <a name="authentication-with-keys"></a>Autentisering med nycklar

Om nyckelautentisering är aktiverat kan `get_keys` du använda metoden för att hämta en primär och sekundär autentiseringsnyckel:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Om du behöver återskapa en nyckel kan du använda[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

### <a name="authentication-with-tokens"></a>Autentisering med token

Om du vill aktivera `token_auth_enabled=True` tokenautentisering anger du parametern när du skapar eller uppdaterar en distribution. I följande exempel kan tokenautentisering med hjälp av SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Om tokenautentisering är aktiverat kan `get_token` du använda metoden för att hämta en JWT-token och den tokens utgångstid:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Du måste begära en ny token efter `refresh_by` tokens tid.
>
> Microsoft rekommenderar starkt att du skapar din Azure Machine Learning-arbetsyta i samma region som azure Kubernetes Service-klustret. Om du vill autentisera med en token ringer webbtjänsten ett anrop till den region där arbetsytan Azure Machine Learning skapas. Om arbetsytans region inte är tillgänglig kan du inte hämta en token för webbtjänsten, även om klustret finns i en annan region än arbetsytan. Detta resulterar effektivt i tokenbaserad autentisering är otillgänglig tills arbetsytans region är tillgänglig igen. Ju större avstånd det tar mellan klustrets region och arbetsytans region, desto längre tid tar det att hämta en token.

## <a name="update-the-web-service"></a>Uppdatera webbtjänsten

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Nästa steg

* [Säker experiment och slutledning i ett virtuellt nätverk](how-to-enable-virtual-network.md)
* [Distribuera en modell med en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md)
* [Felsökning av distribution](how-to-troubleshoot-deployment.md)
* [Använda TLS för att skydda en webbtjänst via Azure Machine Learning](how-to-secure-web-service.md)
* [Använda en ML-modell som distribueras som en webbtjänst](how-to-consume-web-service.md)
* [Övervaka dina Azure Machine Learning-modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)
