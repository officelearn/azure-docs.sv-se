---
title: MLflow Tracking för ML-experiment
titleSuffix: Azure Machine Learning
description: Konfigurera MLflow med Azure Machine Learning för att logga mått och artefakter från ML-modeller som skapats i Databricks-kluster, din lokala miljö eller VM-miljö.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: dce7db9fc508c70d79be62a7e97b3bf52a316b22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76983706"
---
# <a name="track-models-metrics-with-mlflow-and-azure-machine-learning-preview"></a>Spåra modellermått med MLflow och Azure Machine Learning (förhandsversion)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Den här artikeln visar hur du aktiverar MLflows spårnings-URI- och loggnings-API, kollektivt känt som [MLflow Tracking,](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)för att ansluta dina MLflow-experiment och Azure Machine Learning. Om du gör det kan du spåra och logga experimentmått och artefakter på [arbetsytan Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces). Om du redan använder MLflow Tracking för dina experiment tillhandahåller arbetsytan en centraliserad, säker och skalbar plats för att lagra träningsmått och modeller.

<!--
+ Deploy your MLflow experiments as an Azure Machine Learning web service. By deploying as a web service, you can apply the Azure Machine Learning monitoring and data drift detection functionalities to your production models. 
-->

[MLflow](https://www.mlflow.org) är ett bibliotek med öppen källkod för att hantera livscykeln för dina maskininlärningsexperiment. MLFlow Tracking är en komponent i MLflow som loggar och spårar dina utbildningskörningsmått och modellartefakter, oavsett experimentets miljö – lokalt på datorn, på ett fjärrberäkningsmål, en virtuell dator eller ett Azure Databricks-kluster. 

Följande diagram visar att med MLflow Tracking spårar du ett experiments körningsmått och lagrar modellartefakter på din Azure Machine Learning-arbetsyta.

![mlflow med azure machine learning-diagram](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> Informationen i det här dokumentet är främst till datavetare och utvecklare som vill övervaka modellutbildningsprocessen. Om du är administratör som är intresserad av att övervaka resursanvändning och händelser från Azure Machine Learning, till exempel kvoter, slutförda utbildningskörningar eller slutförda modelldistributioner, läser [du Övervaka Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Jämför MLflow- och Azure Machine Learning-klienter

 I tabellen nedan sammanfattas de olika klienter som kan använda Azure Machine Learning och deras respektive funktionsfunktioner.

 MLflow Tracking erbjuder funktioner för mätningsloggning och artefaktlagring som endast annars är tillgängliga via [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).


| | Spårning av&nbsp;MLflow <!--& Deployment--> | Azure Machine Learning Python SDK |  Azure Machine Learning CLI | Azure Machine Learning-studio|
|---|---|---|---|---|
| Hantera arbetsyta |   | ✓ | ✓ | ✓ |
| Använda datalager  |   | ✓ | ✓ | |
| Logga mått      | ✓ | ✓ |   | |
| Ladda upp artefakter | ✓ | ✓ |   | |
| Visa mått     | ✓ | ✓ | ✓ | ✓ |
| Hantera beräkning   |   | ✓ | ✓ | ✓ |

<!--| Deploy models    | ✓ | ✓ | ✓ | ✓ |
|Monitor model performance||✓|  |   |
| Detect data drift |   | ✓ |   | ✓ |
-->
## <a name="prerequisites"></a>Krav

* [Installera MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Installera Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) på den lokala datorn SDK tillhandahåller anslutningen för MLflow för att komma åt din arbetsyta.
* [Skapa en Azure Machine Learning Workspace](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>Spåra lokala körningar

Med MLflow Tracking med Azure Machine Learning kan du lagra de loggade måtten och artefakterna från din lokala körningar på din Azure Machine Learning-arbetsyta.

Installera `azureml-mlflow` paketet för att använda MLflow Tracking med Azure Machine Learning på dina experiment lokalt körs i en Jupyter Notebook eller kodredigerare.

```shell
pip install azureml-mlflow
```

>[!NOTE]
>Namnområdet azureml.contrib ändras ofta när vi arbetar för att förbättra tjänsten. Som sådan bör allt i det här namnområdet betraktas som en förhandsgranskning och stöds inte fullt ut av Microsoft.

Importera `mlflow` och [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) klasser för att komma åt MLflow spårning URI och konfigurera din arbetsyta.

I följande kod `get_mlflow_tracking_uri()` tilldelar metoden en unik spårnings-URI-adress till arbetsytan `ws`och `set_tracking_uri()` pekar URI-spårnings-URI för MLflow till den adressen.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>Spårnings-URI:n är giltig upp till en timme eller mindre. Om du startar om skriptet efter en inaktiv tid använder du api:et för get_mlflow_tracking_uri för att få en ny URI.

Ställ in MLflow-experimentnamnet med `set_experiment()` och `start_run()`starta träningskörningen med . Använd `log_metric()` sedan för att aktivera MLflow loggning API och börja logga din utbildning köra mått.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Spåra fjärrkörningar

Med MLflow Tracking med Azure Machine Learning kan du lagra de loggade måtten och artefakterna från din fjärrkontroll hamnar på din Azure Machine Learning-arbetsyta.

Med fjärrkörningar kan du träna dina modeller på mer kraftfulla beräkningar, till exempel GPU-aktiverade virtuella datorer eller Machine Learning Compute-kluster. Se [Ställ in beräkningsmål för modellutbildning](how-to-set-up-training-targets.md) om du vill veta mer om olika beräkningsalternativ.

Konfigurera din beräknings- och [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) träningskörningsmiljö med klassen. Inkludera `mlflow` `azureml-mlflow` och pip paket i [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) miljöns avsnitt. Konstruera [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) sedan med din fjärrberäkning som beräkningsmål.

```Python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace',
                 name='my_experiment')

mlflow_env = Environment(name='mlflow-env')

cd = CondaDependencies.create(pip_packages=['mlflow', 'azureml-mlflow'])

mlflow_env.python.conda_dependencies = cd

src = ScriptRunConfig(source_directory='./my_script_location', script='my_training_script.py')

src.run_config.target = 'my-remote-compute-compute'
src.run_config.environment = mlflow_env
```

Importera `mlflow` för att använda API:erna för MLflow-loggning i utbildningsskriptet och börja logga dina körningsmått.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

Med den här konfigurationen `Experiment.submit('train.py')` för beräknings- och träningskörning använder du metoden för att skicka en körning. Den här metoden ställer automatiskt in MLflow tracking URI och dirigerar loggningen från MLflow till arbetsytan.

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Spåra Azure Databricks-körningar

Med MLflow Tracking med Azure Machine Learning kan du lagra de loggade måtten och artefakterna från dina Azure Databricks-körs på din Azure Machine Learning-arbetsyta.

Om du vill köra dina Mlflow-experiment med Azure Databricks måste du först skapa en [Azure Databricks-arbetsyta och -kluster](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). I klustret måste du installera *azureml-mlflow-biblioteket* från PyPi för att säkerställa att klustret har åtkomst till nödvändiga funktioner och klasser.

Härifrån importerar du experimentanteckningsboken, bifogar den till azure Databricks-klustret och kör experimentet. 

### <a name="install-libraries"></a>Installera bibliotek

Om du vill installera bibliotek i klustret navigerar du till fliken **Bibliotek** och klickar på **Installera ny**

 ![mlflow med azure machine learning-diagram](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

Skriv azureml-mlflow i fältet **Paket** och klicka sedan på installera. Upprepa det här steget om det behövs för att installera andra ytterligare paket i klustret för experimentet.

 ![mlflow med azure machine learning-diagram](./media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>Konfigurera anteckningsbok och arbetsyta

När klustret har konfigurerats importerar du experimentanteckningsboken, öppnar den och bifogar klustret till den.

Följande kod ska finnas i experimentanteckningsboken. Den här koden hämtar information om din Azure-prenumeration för att instansiera din arbetsyta. Den här koden förutsätter att du har en befintlig resursgrupp och Azure Machine Learning-arbetsyta, annars kan du [skapa dem](how-to-manage-workspace.md). 

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

#### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Ansluta dina Azure-databricks och Azure Machine Learning-arbetsytor

På [Azure-portalen](https://ms.portal.azure.com)kan du länka din Azure Databricks (ADB) arbetsyta till en ny eller befintlig Azure Machine Learning-arbetsyta. Det gör du genom att navigera till ADB-arbetsytan och välja knappen **Länk Azure Machine Learning-arbetsyta** längst ned till höger. Genom att länka dina arbetsytor kan du spåra experimentdata på arbetsytan Azure Machine Learning. 

### <a name="link-mlflow-tracking-to-your-workspace"></a>Länka MLflow-spårning till arbetsytan

När du har instansierat arbetsytan ställer du in URI för MLflow-spårning. På så sätt länkar du MLflow-spårningen till Azure Machine Learning-arbetsytan. När du har länkat kommer alla dina experiment att landa i den hanterade Azure Machine Learning-spårningstjänsten.

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>Direkt ställa in MLflow Tracking i din bärbara dator

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

Importera mlflow i utbildningsskriptet för att använda API:erna för MLflow-loggning och börja logga dina körningsmått. I följande exempel loggar du måttet för epokerförlust. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>Automatisera inställning av MLflow Tracking

I stället för att manuellt ange spårnings-URI i varje efterföljande experiment anteckningsbokssession på dina kluster, gör det automatiskt med hjälp av detta [Azure Machine Learning Tracking Cluster Init-skript](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md).

När du är korrekt konfigurerad kan du se dina MLflow-spårningsdata i AZURE Machine Learning REST API och alla klienter och i Azure Databricks via MLflow-användargränssnittet eller med hjälp av MLflow-klienten.

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Visa mått och artefakter på arbetsytan

Måtten och artefakterna från MLflow-loggning finns på arbetsytan. Om du vill visa dem när som helst navigerar du till arbetsytan och hittar experimentet efter namn i din arbetsyta i [Azure Machine Learning studio](https://ml.azure.com).  Eller kör nedanstående kod. 

```python
run.get_metrics()
ws.get_details()
```

<!-- ## Deploy MLflow models as a web service

Deploying your MLflow experiments as an Azure Machine Learning web service allows you to leverage the Azure Machine Learning model management and data drift detection capabilities and apply them to your production models.

The following diagram demonstrates that with the MLflow deploy API you can deploy your existing MLflow models as an Azure Machine Learning web service, despite their frameworks--PyTorch, Tensorflow, scikit-learn, ONNX, etc., and manage your production models in your workspace.

![mlflow with azure machine learning diagram](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### Log your model

Before you can deploy, be sure that your model is saved so you can reference it and its path location for deployment. In your training script, there should be code similar to the following [mlflow.sklearn.log_model()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html) method, that saves your model to the specified outputs directory. 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> Include the `conda_env` parameter to pass a dictionary representation of the dependencies and environment this model should be run in.

### Retrieve model from previous run

To retrieve the run, you need the run ID and the path in run history of where the model was saved. 

```python
# gets the list of runs for your experiment as an array
experiment_name = 'experiment-with-mlflow'
exp = ws.experiments[experiment_name]
runs = list(exp.get_runs())

# get the run ID and the path in run history
runid = runs[0].id
model_save_path = 'model'
```

### Create Docker image

The `mlflow.azureml.build_image()` function builds a Docker image from the saved model in a framework-aware manner. It automatically creates the framework-specific inferencing wrapper code and specifies package dependencies for you. Specify the model path, your workspace, run ID and other parameters.

The following code builds a docker image using *runs:/<run.id>/model* as the model_uri path for a Scikit-learn experiment.

```python
import mlflow.azureml

azure_image, azure_model = mlflow.azureml.build_image(model_uri='runs:/{}/{}'.format(runid, model_save_path),
                                                      workspace=ws,
                                                      model_name='sklearn-model',
                                                      image_name='sklearn-image',
                                                      synchronous=True)
```
The creation of the Docker image can take several minutes. 

### Deploy the Docker image 

After the image is created, use the Azure Machine Learning SDK to deploy the image as a web service.

First, specify the deployment configuration. Azure Container Instance (ACI) is a suitable choice for a quick dev-test deployment, while Azure Kubernetes Service (AKS) is suitable for scalable production deployments.

#### Deploy to ACI

Set up your deployment configuration with the [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) method. You can also add tags and descriptions to help keep track of your web service.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Then, deploy the image by using the Azure Machine Learning SDK [deploy_from_image()](/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
webservice = Webservice.deploy_from_image( image=azure_image, 
                                           workspace=ws, 
                                           name='diabetes-model-1', 
                                           deployment_config=aci_config)

webservice.wait_for_deployment(show_output=True)
```
#### Deploy to AKS

To deploy to AKS, first create an AKS cluster and bring over the Docker image you want to deploy. For this example, bring over the previously created image from the ACI deployment.

To get the image from the previous ACI deployment use the [Image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image.image?view=azure-ml-py) class. 

```python
from azureml.core.image import Image

# Get the image by name, you can change this based on the image you want to deploy
myimage = Image(workspace=ws, name='sklearn-image') 
```

Create an AKS cluster using the [ComputeTarget.create()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-) method. It may take 20-25 minutes to create a new cluster.

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
Set up your deployment configuration with the [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) method. You can also add tags and descriptions to help keep track of your web service.

```python
from azureml.core.webservice import Webservice, AksWebservice
from azureml.core.image import ContainerImage

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)

# Unique service name
service_name ='aks-service'
```

Then, deploy the image by using the Azure Machine Learning SDK [deploy_from_image()](/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
# Webservice creation using single command
aks_service = Webservice.deploy_from_image( workspace=ws, 
                                            name=service_name,
                                            deployment_config = aks_config
                                            image = myimage,
                                            deployment_target = aks_target)

aks_service.wait_for_deployment(show_output=True)
```

The service deployment can take several minutes.

## Clean up resources

If you don't plan to use the logged metrics and artifacts in your workspace, the ability to delete them individually is currently unavailable. Instead, delete the resource group that contains the storage account and workspace, so you don't incur any charges:

1. In the Azure portal, select **Resource groups** on the far left.

   ![Delete in the Azure portal](./media/how-to-use-mlflow/delete-resources.png)

1. From the list, select the resource group you created.

1. Select **Delete resource group**.

1. Enter the resource group name. Then select **Delete**.

 -->

 ## <a name="example-notebooks"></a>Exempel på anteckningsböcker

[MLflow med Azure ML-anteckningsböcker](https://aka.ms/azureml-mlflow-examples) demonstrerar och utökar begrepp som presenteras i den här artikeln.

## <a name="next-steps"></a>Nästa steg
* [Hantera dina modeller](concept-model-management-and-deployment.md).
* Övervaka dina produktionsmodeller för [datadrift](how-to-monitor-data-drift.md).
