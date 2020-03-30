---
title: Skapa, kör, & spåra ML-pipelines
titleSuffix: Azure Machine Learning
description: Skapa och kör en pipeline för maskininlärning med Azure Machine Learning SDK för Python. Använd ML-pipelines för att skapa och hantera arbetsflöden som syr ihop ml-faser (Machine Learning). Dessa faser omfattar dataförberedelse, modellutbildning, modelldistribution och inferens/bedömning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: 2f62be94c901b383e34608508baa87ea37c893af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283607"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Skapa och kör pipelines för maskininlärning med Azure Machine Learning SDK
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du skapar, publicerar, kör och spårar en pipeline för [maskininlärning](concept-ml-pipelines.md) med hjälp av [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).  Använd **ML-pipelines** för att skapa ett arbetsflöde som innehåller olika ML-faser och sedan publicera pipelinen i din Azure Machine Learning-arbetsyta för att komma åt senare eller dela med andra.  ML-pipelines är idealiska för batchbedömningsscenarier, med hjälp av olika beräkningar, återanvändning av steg istället för att köra om dem, samt dela ML-arbetsflöden med andra.

Du kan använda en annan typ av pipeline som kallas [En Azure Pipeline](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2Fmachine-learning%2Fservice%2Fcontext%2Fml-context&view=azure-devops&tabs=yaml) för CI/CD-automatisering av ML-uppgifter, men den typen av pipeline lagras aldrig i arbetsytan. [Jämför dessa olika rörledningar](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use).

Varje fas i en ML-pipeline, till exempel dataförberedelser och modellutbildning, kan innehålla ett eller flera steg.

De ML-pipelines som du skapar är synliga för medlemmarna i arbetsytan Azure Machine [Learning](how-to-manage-workspace.md). 

ML-pipelines använder fjärrberäkningsmål för beräkning och lagring av mellanliggande och slutliga data som är associerade med den pipelinen. De kan läsa och skriva data till och från [Azure Storage-platser](https://docs.microsoft.com/azure/storage/) som stöds.

Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Krav

* Skapa en [Azure Machine Learning-arbetsyta](how-to-manage-workspace.md) för att lagra alla dina pipeline-resurser.

* [Konfigurera din utvecklingsmiljö](how-to-configure-environment.md) för att installera Azure Machine Learning SDK eller använd en [Azure Machine Learning-beräkningsinstans (förhandsversion)](concept-compute-instance.md) med SDK redan installerat.

Börja med att bifoga arbetsytan:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```


## <a name="set-up-machine-learning-resources"></a>Konfigurera maskininlärningsresurser

Skapa de resurser som krävs för att köra en ML-pipeline:

* Konfigurera ett datalager som används för att komma åt de data som behövs i pipeline-stegen.

* Konfigurera `DataReference` ett objekt så att det pekar på data som finns i eller är tillgängliga i ett datalager.

* Ställ in [beräkningsmålen](concept-azure-machine-learning-architecture.md#compute-targets) som pipeline-stegen ska köras på.

### <a name="set-up-a-datastore"></a>Konfigurera ett datalager

I ett datalager lagras data som pipelinen kan komma åt. Varje arbetsyta har ett standarddatalager. Du kan registrera ytterligare datalager. 

När du skapar din arbetsyta är [Azure-filer](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) och [Azure Blob-lagring](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) kopplade till arbetsytan. Ett standarddatalager registreras för att ansluta till Azure Blob-lagring. Mer information finns i [Bestämma när Azure-filer, Azure-blobbar eller Azure-diskar](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)ska användas . 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

Ladda upp datafiler eller kataloger till datalagret så att de kan vara tillgängliga från dina pipelines. I det här exemplet används Blob-lagringen som datalager:

```python
def_blob_store.upload_files(
    ["./data/20news.pkl"],
    target_path="20newsgroups",
    overwrite=True)
```

En pipeline består av ett eller flera steg. Ett steg är en enhet som körs på ett beräkningsmål. Steg kan använda datakällor och producera "mellanliggande" data. Ett steg kan skapa data som en modell, en katalog med modell och beroende filer eller tillfälliga data. Dessa data är sedan tillgängliga för andra steg senare i pipelinen.

Mer information om hur du ansluter pipelinen till dina data finns i artiklarna [Så här kommer du åt data](how-to-access-data.md) och hur du registrerar [datauppsättningar](how-to-create-register-datasets.md). 

### <a name="configure-data-reference"></a>Konfigurera datareferens

Du har precis skapat en datakälla som kan refereras i en pipeline som en indata till ett steg. En datakälla i en pipeline representeras av ett [DataReference-objekt.](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) Objektet `DataReference` pekar på data som finns i eller är tillgängliga från ett datalager.

```python
from azureml.data.data_reference import DataReference

blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

Mellanliggande data (eller utdata från ett steg) representeras av ett [PipelineData-objekt.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) `output_data1`produceras som utdata för ett steg och används som indata för ett eller flera framtida steg. `PipelineData`introducerar ett databeroende mellan steg och skapar en implicit körningsorder i pipelinen. Det här objektet används senare när pipeline-steg skapas.

```python
from azureml.pipeline.core import PipelineData

output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

### <a name="configure-data-using-datasets"></a>Konfigurera data med hjälp av datauppsättningar

Om du har tabelldata som lagras i en fil eller uppsättning filer är `DataReference`en [tabelldatauppsättning](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) ett effektivt alternativ till en . `TabularDataset`objekt stöder versionshantering, diffs och sammanfattande statistik. `TabularDataset`s är lättjefullt utvärderas (som Python generatorer) och det är effektivt att dela dem genom att dela eller filtrera. Klassen `FileDataset` tillhandahåller liknande lättjasvärderade data som representerar en eller flera filer. 

Du skapar `TabularDataset` en med metoder som [from_delimited_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none-).

```python
from azureml.data import TabularDataset

iris_tabular_dataset = Dataset.Tabular.from_delimited_files([(def_blob_store, 'train-dataset/tabular/iris.csv')])
```

 Du skapar `FileDataset` en med [from_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-).

 Du kan läsa mer om hur du arbetar med datauppsättningar från [Lägg till & registrera datauppsättningar](how-to-create-register-datasets.md) eller den här [exempelanteckningsboken](https://aka.ms/train-datasets).

## <a name="set-up-compute-target"></a>Ställ in beräkningsmål

I Azure Machine Learning __refererar__ termen beräkningar (eller __beräkningsmål)__ till de datorer eller kluster som utför beräkningsstegen i din machine learning-pipeline.   Se [beräkningsmål för modellutbildning](how-to-set-up-training-targets.md) för en fullständig lista över beräkningsmål och hur du skapar och kopplar dem till din arbetsyta.  Processen för att skapa och eller koppla ett beräkningsmål är densamma oavsett om du tränar en modell eller kör ett pipeline-steg. När du har skapat och bifogat beräkningsmål använder du `ComputeTarget` objektet i [pipeline-steget](#steps).

> [!IMPORTANT]
> Utföra hanteringsåtgärder på beräkningsmål stöds inte inifrån fjärrjobb. Eftersom machine learning-pipelines skickas som ett fjärrjobb ska du inte använda hanteringsåtgärder på beräkningsmål inifrån pipelinen.

Nedan följer exempel på hur du skapar och kopplar beräkningsmål för:

* Azure Machine Learning-beräkning
* Azure Databricks 
* Azure Data Lake Analytics

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning-beräkning

Du kan skapa en Azure Machine Learning-beräkning för att köra dina steg.

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

Azure Databricks är en Apache Spark-baserad miljö i Azure-molnet. Den kan användas som ett beräkningsmål med en Azure Machine Learning-pipeline.

Skapa en Azure Databricks-arbetsyta innan du använder den. Om du vill skapa en arbetsytas resurs läser du [fältet Kör ett Spark-jobb på Azure Databricks-dokumentet.](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)

Om du vill bifoga Azure Databricks som ett beräkningsmål anger du följande information:

* __Databricks beräkningsnamn__: Det namn som du vill tilldela till den här beräkningsresursen.
* __Databricks arbetsytans namn__: Namnet på Arbetsytan Azure Databricks.
* __Databricks åtkomsttoken__: Åtkomsttoken som används för att autentisera till Azure Databricks. Om du vill generera en åtkomsttoken läser du [autentiseringsdokumentet.](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html)

Följande kod visar hur du bifogar Azure Databricks som ett beräkningsmål med Azure Machine Learning SDK (__Databricks-arbetsytan måste finnas i samma prenumeration som din AML-arbetsyta):__

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

Ett mer detaljerat exempel finns i en [exempelanteckningsbok](https://aka.ms/pl-databricks) på GitHub.

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics är en plattform för analys av stordata i Azure-molnet. Den kan användas som ett beräkningsmål med en Azure Machine Learning-pipeline.

Skapa ett Azure Data Lake Analytics-konto innan du använder det. Om du vill skapa den här resursen läser du dokumentet [Kom igång med Azure Data Lake Analytics.](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal)

Om du vill koppla DataSjöanalys som ett beräkningsmål måste du använda Azure Machine Learning SDK och ange följande information:

* __Beräkningsnamn__: Det namn som du vill tilldela den här beräkningsresursen.
* __Resursgrupp__: Resursgruppen som innehåller DataSjöanalyskontot.
* __Kontonamn__: Kontonamnet DataSjöanalys.

Följande kod visar hur du bifogar Data Lake Analytics som ett beräkningsmål:

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

Ett mer detaljerat exempel finns i en [exempelanteckningsbok](https://aka.ms/pl-adla) på GitHub.

> [!TIP]
> Azure Machine Learning-pipelines kan bara fungera med data som lagras i standarddatalagret för Data Lake Analytics-kontot. Om de data du behöver arbeta med finns i ett [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) icke-standardlager kan du använda en för att kopiera data före träningen.

## <a name="construct-your-pipeline-steps"></a><a id="steps"></a>Konstruera dina pipeline-steg

När du har skapat och bifogat ett beräkningsmål till arbetsytan är du redo att definiera ett pipeline-steg. Det finns många inbyggda steg som är tillgängliga via Azure Machine Learning SDK. De mest grundläggande av dessa steg är en [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py), som kör ett Python-skript i ett angivet beräkningsmål:

```python
from azureml.pipeline.steps import PythonScriptStep

trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", blob_input_data, "--output", output_data1],
    inputs=[blob_input_data],
    outputs=[output_data1],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Återanvändning av tidigare`allow_reuse`resultat ( ) är nyckeln när du använder rörledningar i en samarbetsmiljö eftersom eliminera onödiga repriser erbjuder smidighet. Återanvändning är standardbeteendet när script_name, indata och parametrarna för ett steg förblir desamma. När utdata för steget återanvänds skickas inte jobbet till beräkningen, utan resultaten från föregående körning är omedelbart tillgängliga för nästa stegs körning. Om `allow_reuse` är inställd på false, genereras alltid en ny körning för det här steget under pipelinekörning. 

När du har definierat dina steg skapar du pipelinen med hjälp av några eller alla av dessa steg.

> [!NOTE]
> Ingen fil eller data överförs till Azure Machine Learning när du definierar stegen eller skapar pipelinen.

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

from azureml.pipeline.core import Pipeline

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

I följande exempel används beräkningsmålet för Azure Databricks som skapats tidigare: 

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

### <a name="use-a-dataset"></a>Använda en datauppsättning 

Om du `TabularDataset` vill `FileDataset` använda antingen ett eller i pipelinen måste du göra det till ett [DatasetConsumptionConfig-objekt](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_consumption_config.datasetconsumptionconfig?view=azure-ml-py) genom att anropa [as_named_input(namn)](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#as-named-input-name-). Du skickar `DatasetConsumptionConfig` det här `inputs` objektet som ett av till pipeline-steget. 

Datauppsättningar som skapats från Azure Blob storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database och Azure Database for PostgreSQL kan användas som indata till alla pipeline-steg. Med undantag för att skriva utdata till en [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py) eller [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)kan utdata[(PipelineData)](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)endast skrivas till Azure Blob- och Azure File-resursdatalager.

```python
dataset_consuming_step = PythonScriptStep(
    script_name="iris_train.py",
    inputs=[iris_tabular_dataset.as_named_input("iris_data")],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Du hämtar sedan datauppsättningen i pipelinen med hjälp av ordlistan [Run.input_datasets.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#input-datasets)

```python
# iris_train.py
from azureml.core import Run, Dataset

run_context = Run.get_context()
iris_dataset = run_context.input_datasets['iris_data']
dataframe = iris_dataset.to_pandas_dataframe()
```

Mer information finns i [azure-pipeline-steps-paketet](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) och [pipeline-klassreferensen.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py)

## <a name="submit-the-pipeline"></a>Skicka pipelinen

När du skickar pipelinen kontrollerar Azure Machine Learning beroenden för varje steg och överför en ögonblicksbild av källkatalogen som du angav. Om ingen källkatalog har angetts överförs den aktuella lokala katalogen. Ögonblicksbilden lagras också som en del av experimentet på arbetsytan.

> [!IMPORTANT]
> Om du vill förhindra att filer inkluderas i ögonblicksbilden skapar du en [.gitignore](https://git-scm.com/docs/gitignore) eller-fil `.amlignore` i katalogen och lägger till filerna i den. Filen `.amlignore` använder samma syntax och mönster som [gitignore-filen.](https://git-scm.com/docs/gitignore) Om det finns `.amlignore` båda filerna har filen företräde.
>
> Mer information finns i [Ögonblicksbilder](concept-azure-machine-learning-architecture.md#snapshots).

```python
from azureml.core import Experiment

# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

När du kör en pipeline första gången azure machine learning:

* Hämtar projektögonblicksbilden till beräkningsmålet från Blob-lagringen som är associerad med arbetsytan.
* Skapar en Docker-avbildning som motsvarar varje steg i pipelinen.
* Hämtar Docker-avbildningen för varje steg till beräkningsmålet från behållarregistret.
* Monterar datalagret `DataReference` om ett objekt anges i ett steg. Om montering inte stöds kopieras data i stället till beräkningsmålet.
* Kör steget i beräkningsmålet som anges i stegdefinitionen. 
* Skapar artefakter, till exempel loggar, stdout och stderr, mått och utdata som anges av steget. Dessa artefakter överförs sedan och förvaras i användarens standarddatalager.

![Diagram över hur du kör ett experiment som en pipeline](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

Mer information finns i klassen Experiment-referensen. [Experiment class](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)

### <a name="view-results-of-a-pipeline"></a>Visa resultat av en pipeline

Se listan över alla dina pipelines och deras körinformation i studion:

1. Logga in på [Azure Machine Learning studio](https://ml.azure.com).

1. [Visa arbetsytan](how-to-manage-workspace.md#view).

1. Till vänster väljer du **Pipelines** för att se alla dina pipeline-körningar.
 ![lista över pipelines för maskininlärning](./media/how-to-create-your-first-pipeline/pipelines.png)
 
1. Välj en specifik pipeline för att se körningsresultaten.

## <a name="git-tracking-and-integration"></a>Git spårning och integration

När du startar en utbildningskörning där källkatalogen är en lokal Git-databas lagras information om databasen i körningshistoriken. Mer information finns i [Git-integrering för Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="publish-a-pipeline"></a>Publicera en pipeline

Du kan publicera en pipeline för att köra den med olika indata senare. För att REST-slutpunkten för en redan publicerad pipeline ska acceptera parametrar måste du parameterisera pipelinen innan du publicerar.

1. Om du vill skapa en pipeline-parameter använder du ett [PipelineParameter-objekt](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) med ett standardvärde.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Lägg `PipelineParameter` till det här objektet som en parameter i något av stegen i pipelinen enligt följande:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. Publicera den här pipelinen som accepterar en parameter när den anropas.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

### <a name="run-a-published-pipeline"></a>Köra en publicerad pipeline

Alla publicerade pipelines har en REST-slutpunkt. Den här slutpunkten anropar körningen av pipelinen från externa system, till exempel icke-Python-klienter. Den här slutpunkten möjliggör "hanterad repeterbarhet" i batchbedömnings- och omskolningsscenarier.

Om du vill anropa körningen av föregående pipeline behöver du en Azure Active Directory-autentiseringshuvudtoken, enligt beskrivningen i [azurecliauthentication-klassreferensen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py) eller få mer information i [notebooken Autentisering i Azure Machine Learning.](https://aka.ms/pl-restep-auth)

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

## <a name="create-a-versioned-pipeline-endpoint"></a>Skapa en versionsversion av pipelineslutpunkten
Du kan skapa en Pipeline-slutpunkt med flera publicerade pipelines bakom. Detta kan användas som en publicerad pipeline men ger dig en fast REST-slutpunkt när du itererar på och uppdaterar dina ML-pipelines.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PublishedPipeline.get(workspace="ws", name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

### <a name="submit-a-job-to-a-pipeline-endpoint"></a>Skicka ett jobb till en pipeline-slutpunkt
Du kan skicka ett jobb till standardversionen av en pipeline-slutpunkt:
```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```
Du kan också skicka ett jobb till en viss version:
```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

Detsamma kan åstadkommas med REST API:
```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

### <a name="use-published-pipelines-in-the-studio"></a>Använda publicerade pipelines i studion

Du kan också köra en publicerad pipeline från studion:

1. Logga in på [Azure Machine Learning studio](https://ml.azure.com).

1. [Visa arbetsytan](how-to-manage-workspace.md#view).

1. Till vänster väljer du **Slutpunkter**.

1. Välj **Pipeline-slutpunkter**högst upp .
 ![lista över maskininlärningspubliceerad pipelines](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. Välj en specifik pipeline för att köra, förbruka eller granska resultat från tidigare körningar av pipeline-slutpunkten.


### <a name="disable-a-published-pipeline"></a>Inaktivera en publicerad pipeline

Om du vill dölja en pipeline från listan över publicerade pipelines inaktiverar du den, antingen i studion eller från SDK:

```
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

Du kan aktivera `p.enable()`den igen med . Mer information finns i [Klassen PublishedPipeline-referens.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py)


## <a name="caching--reuse"></a>Cachelagring & återanvändning  

För att optimera och anpassa beteendet hos dina pipelines kan du göra några saker kring cachelagring och återanvändning. Du kan till exempel välja att:
+ **Inaktivera standardåteranvändningen för stegkörningsutdata** genom att ställa in `allow_reuse=False` under [stegdefinition](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py). Återanvändning är nyckeln när du använder pipelines i en samarbetsmiljö eftersom eliminera onödiga körningar erbjuder smidighet. Du kan dock välja bort återanvändning.
+ **Kraftutdataregenerering för alla steg i en körning** med`pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

Som standard `allow_reuse` är för steg `source_directory` aktiverat och den angivna i stegdefinitionen hashed. Så om skriptet för ett visst`script_name`steg förblir detsamma ( , indata` source_directory` och parametrarna), och inget annat i har ändrats, används utdata för en tidigare stegkörning, jobbet skickas inte till beräkningen och resultaten från föregående körning är omedelbart tillgängliga för nästa steg i stället.

```python
step = PythonScriptStep(name="Hello World",
                        script_name="hello_world.py",
                        compute_target=aml_compute,
                        source_directory=source_directory,
                        allow_reuse=False,
                        hash_paths=['hello_world.ipynb'])
```

## <a name="next-steps"></a>Nästa steg

- Använd [dessa Jupyter-anteckningsböcker på GitHub](https://aka.ms/aml-pipeline-readme) för att utforska pipelines för maskininlärning ytterligare.
- Se SDK-referenshjälpen för [azureml-pipelines-core-paketet](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) och [azureml-pipelines-steps-steps-steps-paketet.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)
- Mer [om hur du aktiverar](how-to-debug-pipelines.md) felsökning och felsökning av pipelines finns i tipsen om felsökning och felsökning.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]
