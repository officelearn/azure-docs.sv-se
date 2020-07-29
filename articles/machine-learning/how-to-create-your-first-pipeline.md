---
title: Skapa, kör & spåra ML-pipelines
titleSuffix: Azure Machine Learning
description: Skapa och kör en pipeline för maskin inlärning med Azure Machine Learning SDK för python. Använd ML-pipelines för att skapa och hantera arbets flöden som häftar ihop Machine Learning-faser (ML). I de här faserna ingår förberedelse av data, modell utbildning, modell distribution och härledning/poängsättning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: nilsp
author: NilsPohlmann
ms.date: 12/05/2019
ms.topic: conceptual
ms.custom: how-to, tracking-python
ms.openlocfilehash: dff86176bac56063c9b588fb27e3549069ed380e
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87326366"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Skapa och kör maskin inlärnings pipeliner med Azure Machine Learning SDK

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du skapar, publicerar, kör och spårar en [pipeline för maskin inlärning](concept-ml-pipelines.md) med hjälp av [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).  Använd **ml-pipelines** för att skapa ett arbets flöde som häftar samman olika ml-faser och publicera sedan den pipelinen i Azure Machine Learning arbets ytan för att komma åt senare eller dela med andra.  ML-pipelines är idealiska för scenarier med batch-poäng, med hjälp av olika beräkningar, återanvända steg i stället för att köra om dem, samt dela ML-arbetsflöden med andra.

Även om du kan använda en annan typ av pipeline som kallas för en [Azure-pipeline](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2Fmachine-learning%2Fservice%2Fcontext%2Fml-context&view=azure-devops&tabs=yaml) för CI/CD-automatisering av ml-aktiviteter, lagras inte den typen av pipeline i arbets ytan. [Jämför dessa olika pipeliner](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use).

Varje fas i en ML-pipeline, till exempel data förberedelse och modell utbildning, kan innehålla ett eller flera steg.

De ML-pipeliner som du skapar visas för medlemmarna i din Azure Machine Learning- [arbetsyta](how-to-manage-workspace.md). 

ML pipelines använder fjärrberäknings mål för beräkning och lagring av mellanliggande och slutliga data som är kopplade till den pipelinen. De kan läsa och skriva data till och från [Azure Storage](https://docs.microsoft.com/azure/storage/) platser som stöds.

Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Krav

* Skapa en [Azure Machine Learning arbets yta](how-to-manage-workspace.md) för att lagra alla dina pipeline-resurser.

* [Konfigurera utvecklings miljön](how-to-configure-environment.md) för att installera Azure Machine Learning SDK eller använd en [Azure Machine Learning beräknings instans](concept-compute-instance.md) med SDK redan installerad.

Börja med att koppla din arbets yta:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

## <a name="set-up-machine-learning-resources"></a>Konfigurera Machine Learning-resurser

Skapa de resurser som krävs för att köra en ML-pipeline:

* Konfigurera ett data lager som används för att komma åt de data som behövs i pipeline-stegen.

* Konfigurera ett `Dataset` objekt så att det pekar på beständiga data som finns i, eller är tillgängliga i, ett data lager. Konfigurera ett `PipelineData` objekt för temporära data som skickas mellan pipeline-steg. 

* Konfigurera [beräknings målen](concept-azure-machine-learning-architecture.md#compute-targets) som dina pipeline-steg ska köras på.

### <a name="set-up-a-datastore"></a>Konfigurera ett data lager

Ett data lager lagrar data för pipelinen för åtkomst. Varje arbets yta har ett standard data lager. Du kan registrera ytterligare data lager. 

När du skapar din arbets yta är [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) och [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) kopplade till arbets ytan. Ett standard data lager har registrerats för att ansluta till Azure Blob Storage. Mer information finns i [bestämma när du ska använda Azure Files, Azure-blobbar eller Azure-diskar](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks). 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

Ladda upp datafiler eller kataloger till data lagret så att de kan nås från dina pipeliner. I det här exemplet används Blob Storage som data lager:

```python
def_blob_store.upload_files(
    ["iris.csv"],
    target_path="train-dataset",
    overwrite=True)
```

En pipeline består av ett eller flera steg. Ett steg är en enhets körning på ett beräknings mål. Steg kan använda data källor och skapa "mellanliggande" data. Ett steg kan skapa data, till exempel en modell, en katalog med modell och beroende filer eller temporära data. Dessa data är sedan tillgängliga för andra steg senare i pipelinen.

Mer information om hur du ansluter din pipeline till dina data finns i artikeln [så här får du åtkomst till data](how-to-access-data.md) och [hur du registrerar data uppsättningar](how-to-create-register-datasets.md). 

### <a name="configure-data-using-dataset-and-pipelinedata-objects"></a>Konfigurera data med hjälp av `Dataset` och- `PipelineData` objekt

Du har precis skapat en data källa som kan refereras till i en pipeline som indata till ett steg. Det bästa sättet att tillhandahålla data till en pipeline är ett [data uppsättnings](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.Dataset) objekt. `Dataset`Objektet pekar på data som finns i eller kan nås från ett data lager eller en webb-URL. `Dataset`Klassen är abstrakt, så du kommer att skapa en instans av antingen en `FileDataset` (som refererar till en eller flera filer) eller en `TabularDataset` som skapats av från en eller flera filer med avgränsade kolumner med data.

`Dataset`objekt stöder versions-, diff-och sammanfattnings statistik. `Dataset`s är Lazy utvärderas (t. ex. python-generatorer) och är effektiva att delmängds dem genom att dela eller filtrera. 

Du skapar en `Dataset` med metoder som [from_file](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) eller [from_delimited_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-).

```python
from azureml.core import Dataset

iris_tabular_dataset = Dataset.Tabular.from_delimited_files([(def_blob_store, 'train-dataset/iris.csv')])
```

Mellanliggande data (eller utdata från ett steg) representeras av ett [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) -objekt. `output_data1`skapas som utdata från ett steg och används som indata för ett eller flera framtida steg. `PipelineData`introducerar ett data beroende mellan stegen och skapar en implicit körnings ordning i pipelinen. Det här objektet kommer att användas senare när du skapar pipeline-steg.

```python
from azureml.pipeline.core import PipelineData

output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

Mer information och exempel kod för att arbeta med data uppsättningar och pipeline-data finns i [Flytta data till och mellan ml-steg (python)](how-to-move-data-in-out-of-pipelines.md).

## <a name="set-up-a-compute-target"></a>Konfigurera ett beräknings mål

I Azure Machine Learning syftar termen __Compute__ (eller __Compute Target__) på de datorer eller kluster som utför beräknings stegen i din Machine Learning-pipeline.   Se [Compute-mål för modell utbildning](how-to-set-up-training-targets.md) för en fullständig lista över beräknings mål och hur du skapar och kopplar dem till din arbets yta.  Processen för att skapa och eller koppla ett beräknings mål är detsamma oavsett om du tränar en modell eller kör ett pipeline-steg. När du har skapat och kopplat ditt beräknings mål använder du `ComputeTarget` objektet i ditt [pipeline-steg](#steps).

> [!IMPORTANT]
> Det finns inte stöd för att utföra hanterings åtgärder på beräknings mål inifrån Fjärrjobb. Eftersom Machine Learning-pipelines skickas som ett Fjärrjobb använder du inte hanterings åtgärder på beräknings mål inifrån pipelinen.

Nedan visas exempel på hur du skapar och kopplar beräknings mål för:

* Azure Machine Learning-beräkning
* Azure Databricks 
* Azure Data Lake Analytics

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute

Du kan skapa en Azure Machine Learning beräkning för att köra dina steg.

```python
from azureml.core.compute import ComputeTarget, AmlCompute

compute_name = "aml-compute"
vm_size = "STANDARD_NC6"
if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,  # STANDARD_NC6 is GPU-enabled
                                                                min_nodes=0,
                                                                max_nodes=4)
    # create the compute target
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # Can poll for a minimum number of nodes and for a specific timeout.
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current cluster status, use the 'status' property
    print(compute_target.status.serialize())
```

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

Azure Databricks är en Apache Spark-baserad miljö i Azure-molnet. Den kan användas som ett beräknings mål med en Azure Machine Learning pipeline.

Skapa en Azure Databricks arbets yta innan du använder den. Information om hur du skapar en arbets ytas resurs finns i [köra ett Spark-jobb på Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) -dokument.

Om du vill bifoga Azure Databricks som ett beräknings mål anger du följande information:

* __Databricks Compute Name__: det namn som du vill tilldela till den här beräknings resursen.
* __Databricks namn på arbets yta__: namnet på arbets ytan Azure Databricks.
* __Databricks__: åtkomst-token som används för att autentisera till Azure Databricks. Om du vill generera en åtkomsttoken, se [Authentication](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html) -dokumentet.

Följande kod visar hur du kopplar Azure Databricks som ett beräknings mål med Azure Machine Learning SDK (Databricks-__arbetsytan måste finnas i samma prenumeration som din AML-arbetsyta__):

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Ett mer detaljerat exempel finns i en [exempel antecknings bok](https://aka.ms/pl-databricks) på GitHub.

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics är en stor data analys plattform i Azure-molnet. Den kan användas som ett beräknings mål med en Azure Machine Learning pipeline.

Skapa ett Azure Data Lake Analytics konto innan du använder det. Information om hur du skapar den här resursen finns i dokumentet [Kom igång med Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) .

Om du vill bifoga Data Lake Analytics som ett beräknings mål måste du använda Azure Machine Learning SDK och ange följande information:

* __Compute-namn__: det namn som du vill tilldela till den här beräknings resursen.
* __Resurs grupp__: den resurs grupp som innehåller det data Lake Analytics kontot.
* __Konto namn__: namnet på data Lake Analytics kontot.

Följande kod visar hur du kopplar Data Lake Analytics som ett beräknings mål:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Ett mer detaljerat exempel finns i en [exempel antecknings bok](https://aka.ms/pl-adla) på GitHub.

> [!TIP]
> Azure Machine Learning pipelines kan bara arbeta med data som lagras i standard data lagret för det Data Lake Analytics kontot. Om de data du behöver arbeta med finns i ett lager som inte är standard kan du använda en [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) för att kopiera data före träning.

## <a name="construct-your-pipeline-steps"></a><a id="steps"></a>Skapa dina pipeline-steg

När du har skapat och kopplat ett beräknings mål till din arbets yta är du redo att definiera ett pipeline-steg. Det finns många inbyggda steg som är tillgängliga via Azure Machine Learning SDK. De mest grundläggande stegen är en [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py), som kör ett Python-skript i ett angivet beräknings mål:

```python
from azureml.pipeline.steps import PythonScriptStep

ds_input = my_dataset.as_named_input('input1')

trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", ds_input.as_download(), "--output", output_data1],
    inputs=[ds_input],
    outputs=[output_data1],
    compute_target=compute_target,
    source_directory=project_folder,
    allow_reuse=True
)
```

Åter användning av tidigare resultat ( `allow_reuse` ) är nyckel när du använder pipelines i en samarbets miljö eftersom det tar onödigt smidigt att ta bort onödig omgång. Åter användning är standard beteendet när script_name, indata och parametrarna för ett steg är desamma. När utdata från steget återanvänds skickas inte jobbet till data bearbetningen, i stället för att resultaten från föregående körning är omedelbart tillgängliga för nästa stegs körning. Om `allow_reuse` är inställt på falskt skapas alltid en ny körning för det här steget under pipeline-körningen. 

När du har definierat stegen skapar du pipelinen med hjälp av några eller samtliga av dessa steg.

> [!NOTE]
> Ingen fil eller några data överförs till Azure Machine Learning när du definierar stegen eller skapar pipelinen.

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

from azureml.pipeline.core import Pipeline

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

I följande exempel används Azure Databricks Compute Target som skapats tidigare: 

```python
from azureml.pipeline.steps import DatabricksStep

dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    compute_target=databricks_compute,
    allow_reuse=False
)
# List of steps to run
steps = [dbStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=steps)
```

### <a name="use-a-dataset"></a>Använd en data uppsättning 

Data uppsättningar som skapats från Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database och Azure Database for PostgreSQL kan användas som indata till alla pipeline-steg. Du kan skriva utdata till en [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py), [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)eller om du vill skriva data till ett bestämt data lager använder [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py). 

> [!IMPORTANT]
> Det går bara att skriva utdata tillbaka till ett data lager med hjälp av PipelineData för Azure blob-och Azure-filresurs-datalager. Den här funktionen stöds inte för [ADLS gen 2-datalager](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) för tillfället.

```python
dataset_consuming_step = PythonScriptStep(
    script_name="iris_train.py",
    inputs=[iris_tabular_dataset.as_named_input("iris_data")],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Sedan hämtar du data uppsättningen i din pipeline med hjälp av ord listan [Kör. input_datasets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#input-datasets) .

```python
# iris_train.py
from azureml.core import Run, Dataset

run_context = Run.get_context()
iris_dataset = run_context.input_datasets['iris_data']
dataframe = iris_dataset.to_pandas_dataframe()
```

Linjen `Run.get_context()` är värda markeringar. Den här funktionen hämtar en `Run` som representerar den aktuella experimentella körningen. I exemplet ovan använder vi den för att hämta en registrerad data uppsättning. En annan vanlig användning av `Run` objektet är att hämta både själva experimentet och arbets ytan där experimentet finns: 

```python
# Within a PythonScriptStep

ws = Run.get_context().experiment.workspace
```

Mer information, inklusive alternativa sätt att skicka och komma åt data, finns i [Flytta data till och mellan steg om ml-pipeline (python)](how-to-move-data-in-out-of-pipelines.md).

## <a name="submit-the-pipeline"></a>Skicka pipelinen

När du skickar pipelinen kontrollerar Azure Machine Learning beroendena för varje steg och överför en ögonblicks bild av käll katalogen som du har angett. Om ingen käll katalog anges överförs den aktuella lokala katalogen. Ögonblicks bilden lagras också som en del av experimentet i din arbets yta.

> [!IMPORTANT]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
>
> Mer information finns i [ögonblicks bilder](concept-azure-machine-learning-architecture.md#snapshots).

```python
from azureml.core import Experiment

# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

När du kör en pipeline första gången Azure Machine Learning:

* Hämtar ögonblicks bilden av projektet till beräknings målet från blob-lagringen som är kopplad till arbets ytan.
* Skapar en Docker-avbildning som motsvarar varje steg i pipelinen.
* Laddar ned Docker-avbildningen för varje steg till beräknings målet från behållar registret.
* Konfigurerar åtkomst till `Dataset` och `PipelineData` objekt. Som `as_mount()` åtkomst läge används säkring för att tillhandahålla virtuell åtkomst. Om Mount inte stöds eller om användaren har angett åtkomst som `as_download()` , kopieras data istället till beräknings målet.
* Kör steget i beräknings målet som anges i steg definitionen. 
* Skapar artefakter, till exempel loggar, STDOUT och stderr, mått och utdata som anges i steget. Dessa artefakter överförs och behålls i användarens standard data lager.

![Diagram över att köra ett experiment som en pipeline](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

Mer information finns i referensen till [experiment klassen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) .

### <a name="view-results-of-a-pipeline"></a>Visa resultat för en pipeline

Se listan över alla dina pipeliner och deras körnings information i Studio:

1. Logga in på [Azure Machine Learning Studio](https://ml.azure.com).

1. [Visa din arbets yta](how-to-manage-workspace.md#view).

1. Till vänster väljer du **pipelines** för att se alla dina pipeline-körningar.
 ![lista över maskin inlärnings pipeliner](./media/how-to-create-your-first-pipeline/pipelines.png)
 
1. Välj en enskild pipeline för att se körnings resultaten.

## <a name="git-tracking-and-integration"></a>Git-spårning och integrering

När du startar en utbildning som kör där käll katalogen är en lokal git-lagringsplats, lagras information om lagrings platsen i körnings historiken. Mer information finns i [git-integrering för Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="publish-a-pipeline"></a>Publicera en pipeline

Du kan publicera en pipeline för att köra den med olika indata senare. För REST-slutpunkten för en redan publicerad pipeline för att godkänna parametrar måste du Parameterisera pipelinen innan du publicerar den.

1. Om du vill skapa en pipeline-parameter använder du ett [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) -objekt med ett standardvärde.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Lägg till det här `PipelineParameter` objektet som en parameter till något av stegen i pipelinen enligt följande:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. Publicera den här pipelinen som ska ta emot en parameter när den anropas.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

### <a name="run-a-published-pipeline"></a>Köra en publicerad pipeline

Alla publicerade pipeliner har en REST-slutpunkt. Med pipeline-slutpunkten kan du utlösa en körning av pipelinen från alla externa system, inklusive icke-python-klienter. Den här slut punkten aktiverar "hanterad repeterbarhet" i batch-poängsättning och ominlärnings scenarier.

Om du vill anropa körningen av föregående pipeline behöver du en Azure Active Directory Authentication Head-token, enligt beskrivningen i [AzureCliAuthentication-klass](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py) referens eller få mer information i [autentiseringen i Azure Machine Learning](https://aka.ms/pl-restep-auth) Notebook.

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

## <a name="create-a-versioned-pipeline-endpoint"></a>Skapa en slut punkt för en versions pipeline

Du kan skapa en pipeline-slutpunkt med flera publicerade pipeliner bakom den. Detta kan användas som en publicerad pipeline, men ger dig en fast REST-slutpunkt när du itererar och uppdaterar dina ML-pipelines.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PublishedPipeline.get(workspace="ws", name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

### <a name="submit-a-job-to-a-pipeline-endpoint"></a>Skicka ett jobb till en pipeline-slutpunkt

Du kan skicka ett jobb till standard versionen av en pipeline-slutpunkt:

```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```

Du kan också skicka ett jobb till en angiven version:

```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

Samma kan åstadkommas med hjälp av REST API:

```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

### <a name="use-published-pipelines-in-the-studio"></a>Använda publicerade pipelines i Studio

Du kan också köra en publicerad pipeline från Studio:

1. Logga in på [Azure Machine Learning Studio](https://ml.azure.com).

1. [Visa din arbets yta](how-to-manage-workspace.md#view).

1. Välj **slut punkter**till vänster.

1. Välj **pipeline-slutpunkter**högst upp.
 ![lista över de publicerade pipelinen för Machine Learning](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. Välj en pipeline för att köra, använda eller Granska resultat från tidigare körningar av pipelinens slut punkt.

### <a name="disable-a-published-pipeline"></a>Inaktivera en publicerad pipeline

Om du vill dölja en pipeline från listan över publicerade pipeliner inaktiverar du den, antingen i Studio eller från SDK:

```python
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

Du kan aktivera den igen med `p.enable()` . Mer information finns i klass referens för [PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py) .

## <a name="caching--reuse"></a>Cachelagring & återanvända  

För att optimera och anpassa beteendet för dina pipeliner kan du göra några saker runt cachelagring och åter användning. Du kan till exempel välja att:
+ **Inaktivera standard åter användning av steget Kör utdata** genom att ställa in `allow_reuse=False` under [steg definition](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py). Åter användning är nyckel när du använder pipelines i en samarbets miljö eftersom du tar bort onödiga körningar ger flexibilitet. Du kan dock välja att inte använda.
+ **Framtvinga generering av utdata för alla steg i en körning** med`pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

Som standard har `allow_reuse` steg Aktiver ATS och den som `source_directory` anges i steg definitionen hashas. Om skriptet för ett specifikt steg däremot är detsamma ( `script_name` , indata och parametrar) och inget annat i har ändrats, så kommer ` source_directory` utdata från föregående steg att återanvändas, jobbet skickas inte till beräkningen och resultaten från föregående körning är omedelbart tillgängliga för nästa steg i stället.

```python
step = PythonScriptStep(name="Hello World",
                        script_name="hello_world.py",
                        compute_target=aml_compute,
                        source_directory=source_directory,
                        allow_reuse=False,
                        hash_paths=['hello_world.ipynb'])
```

## <a name="next-steps"></a>Nästa steg

- Använd [dessa Jupyter-anteckningsböcker på GitHub](https://aka.ms/aml-pipeline-readme) för att utforska maskin inlärnings pipeliner ytterligare.
- Se SDK Reference-hjälpen för [azureml-pipeline-Core-](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) paketet och AzureML- [pipeline-steg-](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) paketet.
- Se tips om [hur du](how-to-debug-pipelines.md) felsöker och felsöker pipeliner.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]
