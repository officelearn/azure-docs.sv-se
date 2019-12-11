---
title: MLflow spårning för ML experiment
titleSuffix: Azure Machine Learning
description: Konfigurera MLflow med Azure Machine Learning för att logga mått & artefakter och distribuera modeller från Databricks, din lokala miljö eller VM-miljö.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 09/23/2019
ms.custom: seodec18
ms.openlocfilehash: c06eb4043e0e7de886ef8180845464a6be554fd1
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976561"
---
# <a name="track-metrics-and-deploy-models-with-mlflow-and-azure-machine-learning-preview"></a>Spåra mått och distribuera modeller med MLflow och Azure Machine Learning (för hands version)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Den här artikeln visar hur du aktiverar MLflows spårnings-URI och loggnings-API: t gemensamt kallat [MLflow-spårning](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)med Azure Machine Learning. På så sätt kan du:

+ Spåra och logga experiment mått och artefakter i din [Azure Machine Learning-arbetsyta](https://docs.microsoft.com/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspaces). Om du redan använder MLflow spårning för dina experiment, tillhandahåller arbets ytan en centraliserad, säker och skalbar plats för att lagra utbildnings mått och-modeller.

+ Distribuera dina MLflow-experiment som en Azure Machine Learning-webbtjänst. Genom att distribuera som en webb tjänst kan du använda funktionerna för att identifiera Azure Machine Learning övervakning och data avkänning i dina produktions modeller. 

[MLflow](https://www.mlflow.org) är ett bibliotek med öppen källkod för hantering av livs cykeln för maskin inlärnings experiment. MLFlow-spårning är en komponent i MLflow som loggar och spårar din utbildning kör mått och modell artefakter, oavsett experimentets miljö – på ett fjärrberäknings mål, på en virtuell dator, lokalt på datorn eller i ett Azure Databricks kluster.

Följande diagram illustrerar att med MLflow spårning spårar du ett Experiments körnings mått och lagrings modell artefakter i din Azure Machine Learning-arbetsyta.

![mlflow med Azure Machine Learning-diagram](media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> Informationen i det här dokumentet är främst avsedd för data experter och utvecklare som vill övervaka processen för modell inlärning. Om du är administratör som är intresse rad av övervakning av resursanvändningen och händelser från Azure Machine Learning, till exempel kvoter, slutförda inlärnings körningar eller slutförd modell distribution, se [övervaknings Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Jämför MLflow och Azure Machine Learning klienter

 I tabellen nedan sammanfattas olika klienter som kan använda Azure Machine Learning och deras respektive funktions funktioner.

 MLflow tracking erbjuder funktioner för mått loggning och artefakt lagring som endast är tillgängliga via [Azure Machine Learning python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).


| | MLflow spårning & distribution | Azure Machine Learning python SDK |  Azure Machine Learning CLI | Azure Machine Learning-studio|
|---|---|---|---|---|
| Hantera arbets yta |   | ✓ | ✓ | ✓ |
| Använda data lager  |   | ✓ | ✓ | |
| Logg mått      | ✓ | ✓ |   | |
| Ladda upp artefakter | ✓ | ✓ |   | |
| Visa mått     | ✓ | ✓ | ✓ | ✓ |
| Hantera beräkning   |   | ✓ | ✓ | ✓ |
| Distribuera modeller    | ✓ | ✓ | ✓ | ✓ |
|Övervaka modell prestanda||✓|  |   |
| Identifiera dataavvikelser |   | ✓ |   | ✓ |

## <a name="prerequisites"></a>Krav

* [Installera MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Installera Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) på den lokala datorn SDK: n ger anslutningen till MLflow för att få åtkomst till din arbets yta.
* [Skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>Spåra lokala körningar

Genom MLflow spårning med Azure Machine Learning kan du lagra de inloggade måtten och artefakterna från dina lokala körningar i Azure Machine Learning arbets ytan.

Installera `azureml-contrib-run`-paketet om du vill använda MLflow spårning med Azure Machine Learning på dina experiment lokalt i en Jupyter Notebook eller kod redigerare.

```shell
pip install azureml-contrib-run
```

>[!NOTE]
>Namn rymden azureml. contrib ändras ofta, medan vi arbetar för att förbättra tjänsten. Därför bör allt i det här namn området betraktas som en för hands version och stöds inte fullt ut av Microsoft.

Importera `mlflow`-och [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) -klasserna för att få åtkomst till MLflows spårnings-URI och konfigurera din arbets yta.

I följande kod tilldelar `get_mlflow_tracking_uri()`-metoden en unik spårnings-URI-adress till arbets ytan, `ws`och `set_tracking_uri()` pekar MLflow spårnings-URI: n till den adressen.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>Spårnings-URI: n är giltig till en timme eller mindre. Om du startar om skriptet efter inaktivitet använder du get_mlflow_tracking_uri API för att hämta en ny URI.

Ange namnet på MLflow-experimentet med `set_experiment()` och starta din utbildning och kör `start_run()`. Använd sedan `log_metric()` för att aktivera loggnings-API: et för MLflow och börja logga dina utbildnings körnings mått.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Spåra fjärrkörningar

Med MLflow spårning med Azure Machine Learning kan du lagra de inloggade måtten och artefakterna från fjärrdatorn som körs i Azure Machine Learning arbets ytan.

Med fjärrkörningar kan du träna dina modeller på mer kraftfulla beräkningar, till exempel GPU-aktiverade virtuella datorer eller Machine Learning-beräkning kluster. Se [Konfigurera beräknings mål för modell utbildning](how-to-set-up-training-targets.md) för att lära dig mer om olika beräknings alternativ.

Konfigurera din beräknings-och övnings körnings miljö med klassen [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) . Ta med `mlflow` och `azure-contrib-run` pip-paket i miljöns [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) -avsnitt. Skapa sedan [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) med din fjärrberäkning som beräknings mål.

```Python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace',
                 name='my_experiment')

mlflow_env = Environment(name='mlflow-env')

cd = CondaDependencies.create(pip_packages=['mlflow', 'azureml-contrib-run'])

mlflow_env.python.conda_dependencies = cd

src = ScriptRunConfig(source_directory='./my_script_location', script='my_training_script.py')

src.run_config.target = 'my-remote-compute-compute'
src.run_config.environment = mlflow_env
```

I övnings skriptet importerar du `mlflow` att använda API: er för MLflow-loggning och börjar logga dina körnings mått.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

Med den här beräknings-och övnings körnings konfigurationen använder du metoden `Experiment.submit('train.py')` för att skicka en körning. Den här metoden anger automatiskt MLflow spårnings-URI och dirigerar loggningen från MLflow till din arbets yta.

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Spåra Azure Databricks körningar

Med MLflow spårning med Azure Machine Learning kan du lagra de inloggade måtten och artefakterna från dina Databricks-körningar i Azure Machine Learning arbets ytan.

Om du vill köra dina Mlflow-experiment med Azure Databricks måste du först skapa en [Azure Databricks arbets yta och ett kluster](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)

I klustret, se till att installera biblioteket *azureml-mlflow* från PyPi, för att säkerställa att klustret har åtkomst till de nödvändiga funktionerna och klasserna.
Härifrån kan du importera din experiment-anteckningsbok, koppla ditt kluster till den och köra experimentet. 

### <a name="install-libraries"></a>Installera bibliotek

Om du vill installera bibliotek i klustret går du till fliken **bibliotek** och klickar på **installera ny**

 ![mlflow med Azure Machine Learning-diagram](media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

I fältet **paket** skriver du azureml-mlflow och klickar sedan på installera. Upprepa det här steget vid behov för att installera andra ytterligare paket till klustret för experimentet.

 ![mlflow med Azure Machine Learning-diagram](media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>Konfigurera din bärbara dator och arbets yta

När klustret har kon figurer ATS importerar du din experiment-anteckningsbok, öppnar den och kopplar klustret till den.

Följande kod ska finnas i din experiment antecknings bok. Den här koden hämtar information om din Azure-prenumeration för att skapa en instans av arbets ytan. Den här koden förutsätter att du har en befintlig resurs grupp och Azure Machine Learning arbets ytan, annars kan du [skapa dem](how-to-manage-workspace.md). 

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace
from azureml.mlflow import get_portal_url

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)
```

#### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Anslut dina Azure Databricks och Azure Machine Learning arbets ytor

På [Azure Portal](https://ms.portal.azure.com)kan du länka din Azure DATABRICKS (ADB)-arbets yta till en ny eller befintlig Azure Machine Learning-arbetsyta. Det gör du genom att gå till din ADB-arbetsyta och välja knappen **länk Azure Machine Learning arbets yta** längst ned till höger. Genom att länka dina arbets ytor kan du spåra dina experiment data i Azure Machine Learning-arbetsytan. 

### <a name="link-mlflow-tracking-to-your-workspace"></a>Länka MLflow spårning till din arbets yta

När du har instansierat din arbets yta ställer du in MLflow tracking URI. Genom att göra detta länkar du MLflow-spårningen till Azure Machine Learning-arbetsyta. När du har länkat kommer alla experiment att hamna i den hanterade Azure Machine Learning spårnings tjänsten.

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>Konfigurera MLflow-spårning direkt i din bärbara dator

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

I ditt utbildnings skript importerar du mlflow för att använda API: er för loggning av MLflow och börjar logga dina körnings mått. I följande exempel loggas måttet för epoken. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>Automatisera inställningen av MLflow-spårning

I stället för att manuellt ställa in spårnings-URI: n i varje efterföljande experiment-anteckningsbok i klustret, gör du det automatiskt med hjälp av detta [Azure Machine Learning spårnings kluster init-skript](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md).

När du har konfigurerat korrekt kan du se dina MLflow spårnings data i Azure Machine Learning REST API och alla klienter, och i Azure Databricks via användar gränssnittet i MLflow eller med hjälp av MLflow-klienten.

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Visa mått och artefakter i din arbets yta

Måtten och artefakterna från MLflow-loggning behålls i din arbets yta. Om du vill visa dem när som helst, navigerar du till din arbets yta och hittar experimentet efter namn i din arbets yta i [Azure Machine Learning Studio](https://ml.azure.com).  Eller kör koden nedan. 

```python
run.get_metrics()
ws.get_details()
```

## <a name="deploy-mlflow-models-as-a-web-service"></a>Distribuera MLflow-modeller som en webb tjänst

Genom att distribuera dina MLflow-experiment som en Azure Machine Learning-webbtjänst kan du använda funktionerna för hantering av Azure Machine Learning-modell och data avkänning och tillämpa dem i dina produktions modeller.

Följande diagram visar att med MLflow distributions-API: t kan du distribuera dina befintliga MLflow-modeller som en Azure Machine Learning webb tjänst, trots deras ramverk – PyTorch, Tensorflow, scikit – lära, ONNX, osv. och hantera dina produktions modeller i din arbets yta.

![mlflow med Azure Machine Learning-diagram](media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### <a name="log-your-model"></a>Logga din modell

Innan du kan distribuera måste du se till att din modell sparas så att du kan referera till den och dess sökväg för distribution. I utbildnings skriptet bör det finnas en kod som liknar följande [mlflow. sklearn. log_model ()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html) -metod som sparar din modell i den angivna katalogen med utdata. 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> Ta med parametern `conda_env` för att skicka en ord lista över de beroenden och miljö som den här modellen ska köras i.

### <a name="retrieve-model-from-previous-run"></a>Hämta modell från föregående körning

Om du vill hämta körningen behöver du körnings-ID och sökvägen i körnings historiken för var modellen sparades. 

```python
# gets the list of runs for your experiment as an array
experiment_name = 'experiment-with-mlflow'
exp = ws.experiments[experiment_name]
runs = list(exp.get_runs())

# get the run ID and the path in run history
runid = runs[0].id
model_save_path = 'model'
```

### <a name="create-docker-image"></a>Skapa Docker-avbildning

Funktionen `mlflow.azureml.build_image()` skapar en Docker-avbildning från den sparade modellen i ett Ramverks medveten sätt. Det skapar automatiskt den Framework-/regionsspecifika inferencing omslutnings koden och anger paket beroenden för dig. Ange modell Sök vägen, din arbets yta, kör ID och andra parametrar.

I följande kod skapas en Docker-avbildning med hjälp av *kör:/< kör. id >/Model* som model_uri Sök väg för ett Scikit-test.

```python
import mlflow.azureml

azure_image, azure_model = mlflow.azureml.build_image(model_uri='runs:/{}/{}'.format(runid, model_save_path),
                                                      workspace=ws,
                                                      model_name='sklearn-model',
                                                      image_name='sklearn-image',
                                                      synchronous=True)
```
Det kan ta flera minuter att skapa Docker-avbildningen. 

### <a name="deploy-the-docker-image"></a>Distribuera Docker-avbildningen 

När avbildningen har skapats använder du Azure Machine Learning SDK för att distribuera avbildningen som en webb tjänst.

Börja med att ange distributions konfigurationen. Azure Container Instance (ACI) är ett lämpligt alternativ för en snabb distribution av utveckling och testning, medan Azure Kubernetes service (AKS) är lämpligt för skalbara produktions distributioner.

#### <a name="deploy-to-aci"></a>Distribuera till ACI

Konfigurera distributions konfigurationen med metoden [deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Du kan också lägga till taggar och beskrivningar för att hålla reda på din webb tjänst.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Distribuera sedan avbildningen med hjälp av metoden Azure Machine Learning SDK [deploy_from_image ()](/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none--overwrite-false-) . 

```python
webservice = Webservice.deploy_from_image( image=azure_image, 
                                           workspace=ws, 
                                           name='diabetes-model-1', 
                                           deployment_config=aci_config)

webservice.wait_for_deployment(show_output=True)
```
#### <a name="deploy-to-aks"></a>Distribuera till AKS

Om du vill distribuera till AKS måste du först skapa ett AKS-kluster och ta över Docker-avbildningen som du vill distribuera. I det här exemplet ska du ta över den tidigare skapade avbildningen från ACI-distributionen.

Använd [avbildnings](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image.image?view=azure-ml-py) klassen för att hämta avbildningen från den tidigare ACI-distributionen. 

```python
from azureml.core.image import Image

# Get the image by name, you can change this based on the image you want to deploy
myimage = Image(workspace=ws, name='sklearn-image') 
```

Skapa ett AKS-kluster med metoden [ComputeTarget. Create ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-) . Det kan ta 20-25 minuter att skapa ett nytt kluster.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aks-mlflow' 

# Create the cluster
aks_target = ComputeTarget.create(workspace=ws, 
                                  name=aks_name, 
                                  provisioning_configuration=prov_config)

aks_target.wait_for_completion(show_output = True)

print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```
Konfigurera distributions konfigurationen med metoden [deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Du kan också lägga till taggar och beskrivningar för att hålla reda på din webb tjänst.

```python
from azureml.core.webservice import Webservice, AksWebservice
from azureml.core.image import ContainerImage

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)

# Unique service name
service_name ='aks-service'
```

Distribuera sedan avbildningen med hjälp av metoden Azure Machine Learning SDK [deploy_from_image ()](/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none--overwrite-false-) . 

```python
# Webservice creation using single command
aks_service = Webservice.deploy_from_image( workspace=ws, 
                                            name=service_name,
                                            deployment_config = aks_config
                                            image = myimage,
                                            deployment_target = aks_target)

aks_service.wait_for_deployment(show_output=True)
```

Tjänste distributionen kan ta flera minuter.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att använda de loggade måtten och artefakterna i din arbets yta är möjligheten att ta bort dem individuellt otillgänglig. Ta i stället bort resurs gruppen som innehåller lagrings kontot och arbets ytan, så du debiteras inte några avgifter:

1. I Azure-portalen väljer du **Resursgrupper** längst till vänster.

   ![Ta bort i Azure Portal](media/how-to-use-mlflow/delete-resources.png)

1. Välj den resursgrupp i listan som du har skapat.

1. Välj **Ta bort resursgrupp**.

1. Ange resursgruppsnamnet. Välj sedan **Ta bort**.


## <a name="example-notebooks"></a>Exempel-anteckningsböcker

[MLflow med Azure ml-anteckningsböcker](https://aka.ms/azureml-mlflow-examples) demonstrerar och utökar begrepp som presenteras i den här artikeln.

## <a name="next-steps"></a>Nästa steg
* [Hantera dina modeller](concept-model-management-and-deployment.md).
* Övervaka dina produktions modeller för [data avvikelser](how-to-monitor-data-drift.md).
