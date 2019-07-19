---
title: Skapa, kör & spåra ML-pipelines
titleSuffix: Azure Machine Learning service
description: Skapa och kör en machine learning-pipeline med Azure Machine Learning-SDK för Python. Du använder pipelines för att skapa och hantera arbets flöden som häftar ihop Machine Learning-faser (ML). I de här faserna ingår förberedelse av data, modell utbildning, modell distribution och härledning/poängsättning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: b1e45f89cd557281399c86ae75898d99e0d4d381
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68327011"
---
# <a name="create-and-run-a-machine-learning-pipeline-by-using-azure-machine-learning-sdk"></a>Skapa och köra en pipeline för maskin inlärning med hjälp av Azure Machine Learning SDK

I den här artikeln får du lära dig hur du skapar, publicerar, kör och spårar en [pipeline för maskin inlärning](concept-ml-pipelines.md) med hjälp av [Azure Machine Learning SDK](https://aka.ms/aml-sdk).  Dessa pipelines att skapa och hantera de arbetsflöden som ihop olika faser av machine learning. Varje fas i en pipeline som förberedelse av data och modellträning, kan innehålla ett eller flera steg.

Pipelines som du skapar är synliga för medlemmar i din Azure Machine Learning-tjänsten [arbetsytan](how-to-manage-workspace.md). 

Pipelines Använd remote beräkningsmål för beräkning och lagring av mellanliggande och slutlig data som är associerade med denna pipeline. Pipelines kan läsa och skriva data till och från [Azure Storage](https://docs.microsoft.com/azure/storage/) platser som stöds.

Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine learnings tjänsten](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Förutsättningar

* [Konfigurera utvecklingsmiljön](how-to-configure-environment.md) att installera Azure Machine Learning-SDK.

* Skapa en [Azure Machine Learning-arbetsyta](how-to-configure-environment.md#workspace) att lagra alla dina pipeline-resurser. 

  ```python
  from azureml.core import Workspace
  
  ws = Workspace.create(
     name = '<workspace-name>',
     subscription_id = '<subscription-id>',
     resource_group = '<resource-group>',
     location = '<workspace_region>',
     exist_ok = True)
  ```

## <a name="set-up-machine-learning-resources"></a>Konfigurera machine learning-resurser

Skapa de resurser som krävs för att köra en pipeline:

* Ställ in ett datalager som används för att komma åt data som behövs i pipeline-stegen.

* Konfigurera ett `DataReference` objekt så att det pekar på data som finns i, eller är tillgängliga i, ett data lager.

* Konfigurera den [beräkningsmål](concept-azure-machine-learning-architecture.md#compute-targets) som din pipeline-stegen körs.

### <a name="set-up-a-datastore"></a>Konfigurera ett datalager
Ett datalager lagrar data för att få åtkomst till pipelinen. Varje arbetsyta har ett standard-datalager. Du kan registrera ytterligare datalager. 

När du skapar din arbets yta kopplas [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) och [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) till arbets ytan som standard. Azure File Storage är standard data lagret för en arbets yta, men du kan också använda Blob Storage som ett data lager. Mer information finns i [bestämma när du ska använda Azure Files, Azure-blobbar eller Azure-diskar](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks). 

```python
# Default datastore (Azure blob storage)
def_data_store = ws.get_default_datastore() 

# The above call is equivalent to this 
def_data_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefileblobstore")
```

Ladda upp filer eller kataloger till databasen för att de ska vara tillgängliga från dina pipelines. I det här exemplet används Blob Storage-versionen av data lagret:

```python
def_blob_store.upload_files(
    ["./data/20news.pkl"],
    target_path="20newsgroups", 
    overwrite=True)
```

En pipeline består av en eller flera steg. Ett steg är en enhet som kör på ett beräkningsmål. Anvisningarna kan använda datakällor och producerar de data som ”mellanliggande”. Ett steg kan skapa data, till exempel en modell, en katalog med modellen och beroende filer eller tillfälliga data. Dessa data är sedan tillgänglig för andra steg senare i pipelinen.

### <a name="configure-data-reference"></a>Konfigurera data-referens

Du skapade en datakälla som kan refereras i en pipeline som indata till ett steg. En datakälla i en pipeline representeras av en [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) objekt. Den `DataReference` objekt som pekar på data som finns i eller kan nås från ett datalager.

```python
blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

Mellanliggande data (eller utdata från ett steg) representeras av en [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) objekt. `output_data1`skapas som utdata från ett steg och används som indata för ett eller flera framtida steg. `PipelineData`introducerar ett data beroende mellan stegen och skapar en implicit körnings ordning i pipelinen.

```python
output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

## <a name="set-up-compute-target"></a>Konfigurera beräkningsmål

I Azure Machine Learning syftar termen __Compute__ (eller __Compute Target__) på de datorer eller kluster som utför beräknings stegen i din Machine Learning-pipeline.   Se [Compute-mål för modell utbildning](how-to-set-up-training-targets.md) för en fullständig lista över beräknings mål och hur du skapar och kopplar dem till din arbets yta.  Processen för att skapa och eller koppla ett beräknings mål är detsamma oavsett om du tränar en modell eller kör ett pipeline-steg. När du har skapat och kopplat ditt beräknings mål använder `ComputeTarget` du objektet i ditt [pipeline-steg](#steps).

> [!IMPORTANT]
> Det finns inte stöd för att utföra hanterings åtgärder på beräknings mål inifrån Fjärrjobb. Eftersom Machine Learning-pipelines skickas som ett Fjärrjobb använder du inte hanterings åtgärder på beräknings mål inifrån pipelinen.

Nedan visas exempel på hur du skapar och kopplar beräknings mål för:

* Azure Machine Learning-beräkning
* Azure Databricks 
* Azure Data Lake Analytics

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning-beräkning

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
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size, # STANDARD_NC6 is GPU-enabled
                                                                min_nodes = 0, 
                                                                max_nodes = 4)
     # create the compute target
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # Can poll for a minimum number of nodes and for a specific timeout. 
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current cluster status, use the 'status' property    
    print(compute_target.status.serialize())
```

### <a id="databricks"></a>Azure Databricks

Azure Databricks är en Apache Spark-baserad miljö i Azure-molnet. Den kan användas som ett beräknings mål med en Azure Machine Learning pipeline.

Skapa en Azure Databricks arbets yta innan du använder den. Om du vill skapa dessa resurs den [kör ett Spark-jobb på Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) dokumentet.

Om du vill bifoga Azure Databricks som ett beräknings mål anger du följande information:

* __Databricks Compute-namn__: Det namn som du vill tilldela till den här beräknings resursen.
* __Namn på Databricks-arbetsyta__: Namnet på Azure Databricks arbets ytan.
* __Databricks__-åtkomsttoken: Den åtkomsttoken som används för att autentisera till Azure Databricks. Generera en åtkomsttoken genom att se den [autentisering](https://docs.azuredatabricks.net/api/latest/authentication.html) dokumentet.

Följande kod visar hur du kopplar Azure Databricks som ett beräknings mål med Azure Machine Learning SDK:

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get("AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get("AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get("AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get("AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group = databricks_resource_group,
                                                           workspace_name = databricks_workspace_name,
                                                           access_token = databricks_access_token)
    databricks_compute = ComputeTarget.attach(
             ws,
             databricks_compute_name,
             attach_config
         )
    
    databricks_compute.wait_for_completion(True)
```

Ett mer detaljerat exempel finns i en [exempel antecknings bok](https://aka.ms/pl-databricks) på GitHub.

### <a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics är en analysplattform med stordata i Azure-molnet. Den kan användas som ett beräknings mål med en Azure Machine Learning pipeline.

Skapa ett Azure Data Lake Analytics konto innan du använder det. Om du vill skapa den här resursen, den [Kom igång med Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) dokumentet.

Om du vill koppla Data Lake Analytics som beräkningsmål du använder Azure Machine Learning SDK och ange följande information:

* __Compute-namn__: Det namn som du vill tilldela till den här beräknings resursen.
* __Resursgrupp__: Resurs gruppen som innehåller Data Lake Analytics kontot.
* __Kontonamn__: Namnet på Data Lake Analyticss kontot.

Följande kod visar hur du kopplar Data Lake Analytics som beräkningsmål:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get("AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get("AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get("AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group = adla_resource_group,
                                                     account_name = adla_account_name)
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
> Azure Machine Learning pipelines fungerar bara med data som lagras i datalagret standard för Data Lake Analytics-kontot. Om data som du vill arbeta med är i en icke-standard-store kan du använda en [ `DataTransferStep` ](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) att kopiera data innan utbildning.

## <a id="steps"></a>Skapa dina pipeline-steg

När du har skapat och kopplat ett beräknings mål till din arbets yta är du redo att definiera ett pipeline-steg. Det finns många inbyggda steg som är tillgängliga via Azure Machine Learning SDK. De mest grundläggande stegen är en [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py), som kör ett Python-skript i ett angivet beräknings mål:

```python
trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", blob_input_data, "--output", processed_data1],
    inputs=[blob_input_data],
    outputs=[processed_data1],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Åter användning av tidigare resultat`allow_reuse`() är nyckel när du använder pipelines i en samarbets miljö eftersom det är smidigare att ta bort onödiga återkörningar. Detta är standard beteendet när script_name, indata och parametrarna för ett steg är desamma. När utdata från steget återanvänds skickas inte jobbet till data bearbetningen, i stället för att resultaten från föregående körning är omedelbart tillgängliga för nästa stegs körning. Om det är inställt på falskt skapas alltid en ny körning för det här steget under pipeline-körningen. 

När du har definierat stegen skapar du pipelinen med hjälp av några eller samtliga av dessa steg.

> [!NOTE]
> Ingen fil eller några data överförs till Azure Machine Learning tjänsten när du definierar stegen eller skapar pipelinen.

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

I följande exempel används Azure Databricks Compute Target som skapats tidigare: 

```python
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

Mer information finns i referens för [Azure-pipeline-steg-paketet](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) och [pipeline-klassen](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) .

## <a name="submit-the-pipeline"></a>Skicka pipelinen

När du skickar pipelinen kontrollerar Azure Machine Learning tjänsten beroenden för varje steg och överför en ögonblicks bild av käll katalogen som du har angett. Om ingen källkatalog har angetts, laddas den aktuella lokala katalogen. Ögonblicks bilden lagras också som en del av experimentet i din arbets yta.

> [!IMPORTANT]
> Om du vill förhindra att filer tas med i ögonblicks bilden skapar du en `.amlignore` [. gitignore](https://git-scm.com/docs/gitignore) -eller-fil i katalogen och lägger till filerna i den. Filen använder samma syntax och mönster som [. gitignore](https://git-scm.com/docs/gitignore) -filen. `.amlignore` Om båda filerna finns `.amlignore` prioriteras filen.
>
> Mer information finns i [ögonblicks bilder](concept-azure-machine-learning-architecture.md#snapshots).

```python
# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

När du kör en pipeline första gången Azure Machine Learning:

* Hämtar ögonblicks bilden av projektet till beräknings målet från blob-lagringen som är kopplad till arbets ytan.
* Skapar en Docker-avbildning som motsvarar varje steg i pipelinen.
* Laddar ned Docker-avbildningen för varje steg till beräknings målet från behållar registret.
* Monterar data lagret, om ett `DataReference` objekt anges i ett steg. Om monteringspunkten inte stöds, i stället kopieras data till målet för beräkning.
* Kör steget i beräknings målet som anges i steg definitionen. 
* Skapar artefakter, till exempel loggar, STDOUT och stderr, mått och utdata som anges i steget. Dessa artefakter överförs och behålls i användarens standard data lager.

![Diagram över att köra ett experiment som en pipeline](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

Mer information finns i referensen till [experiment klassen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) .

## <a name="github-tracking-and-integration"></a>GitHub spårning och integrering

När du startar en utbildning som kör där käll katalogen är en lokal git-lagringsplats, lagras information om lagrings platsen i körnings historiken. Till exempel loggas det aktuella inchecknings-ID: t för databasen som en del av historiken.

## <a name="publish-a-pipeline"></a>Publicera en pipeline

Du kan publicera en pipeline kan köras med olika indata senare. För REST-slutpunkten för en redan publicerad pipeline för att godkänna parametrar måste du Parameterisera pipelinen innan du publicerar den. 

1. Använd för att skapa en pipeline-parameter, en [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) objekt med ett standardvärde.

   ```python
   pipeline_param = PipelineParameter(
     name="pipeline_arg", 
     default_value=10)
   ```

2. Lägg till denna `PipelineParameter` objekt som en parameter till något av stegen i pipelinen enligt följande:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],    
     target=compute_target, 
     source_directory=project_folder)
   ```

3. Publicera den här pipelinen som accepterar en parameter när anropas.

   ```python
   published_pipeline1 = pipeline1.publish(
       name="My_Published_Pipeline", 
       description="My Published Pipeline Description")
   ```

## <a name="run-a-published-pipeline"></a>Kör en publicerad pipeline

Alla publicerade pipeliner har en REST-slutpunkt. Den här slut punkten anropar körningen av pipelinen från externa system, till exempel icke-python-klienter. Den här slut punkten aktiverar "hanterad repeterbarhet" i batch-poängsättning och ominlärnings scenarier.

Om du vill anropa körningen av föregående pipeline behöver du en Azure Active Directory Authentication Head-token, enligt beskrivningen i [AzureCliAuthentication-klassen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py) eller få mer information vid [autentisering i Azure Machine Learning](https://aka.ms/pl-restep-auth) Notebook.

```python
response = requests.post(published_pipeline1.endpoint, 
    headers=aad_token, 
    json={"ExperimentName": "My_Pipeline",
        "ParameterAssignments": {"pipeline_arg": 20}})
```

## <a name="view-results"></a>Visa resultat

Se en lista över alla dina pipelines och deras körningsinformation:
1. Logga in på [Azure Portal](https://portal.azure.com/).  

1. [Visa arbetsytan](how-to-manage-workspace.md#view) att hitta listan över pipeliner.
 ![lista över machine learning pipelines](./media/how-to-create-your-first-pipeline/list_of_pipelines.png)
 
1. Välj en specifik pipeline för att visa Körningsresultat.

## <a name="caching--reuse"></a>Cachelagring & återanvända  

För att optimera och anpassa beteendet för dina pipeliner kan du göra några saker runt cachelagring och åter användning. Du kan till exempel välja att:
+ **Inaktivera standard åter användning av steget Kör utdata** genom att ställa in `allow_reuse=False` under [steg definition](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py). Åter användning är nyckel när du använder pipelines i en samarbets miljö eftersom du tar bort onödiga körningar ger flexibilitet. Du kan dock välja mellan detta.
+ **Utöka hashing bortom skriptet**och ta även med en absolut sökväg eller relativa sökvägar till source_directory till andra filer och kataloger med hjälp av`hash_paths=['<file or directory']` 
+ **Framtvinga generering av utdata för alla steg i en körning** med`pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

Som standard `allow-reuse` har steg Aktiver ATS och endast huvud skript filen är hash-kodad. Om skriptet för ett specifikt steg däremot är detsamma (`script_name`, indata och parametrar), så kommer utdata från ett föregående steg att återanvändas, jobbet skickas inte till beräkningen och resultatet från föregående körning är omedelbart tillgängligt för nästa steg i stället .  

```python
step = PythonScriptStep(name="Hello World", 
                        script_name="hello_world.py",  
                        compute_target=aml_compute,  
                        source_directory= source_directory, 
                        allow_reuse=False, 
                        hash_paths=['hello_world.ipynb']) 
```
 

## <a name="next-steps"></a>Nästa steg
- Använd [dessa Jupyter-anteckningsböcker på GitHub](https://aka.ms/aml-pipeline-readme) att utforska machine learning-ytterligare pipelines.
- Läsa hjälpen för SDK-referens för den [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) paketet och [azureml-pipelines-steg](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) paketet.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
