---
title: Skapa och köra pipelines för ML - Azure Machine Learning-tjänsten
description: Skapa och kör en machine learning-pipeline med Azure Machine Learning-SDK för Python.  Pipelines används för att skapa och hantera arbetsflöden som sadelhäftning tillsammans maskininlärning (ML) faser, till exempel dataförberedelser, modellinlärning, distribution av modeller och inferensjobb.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 12/04/2018
ms.custom: seodec12
ms.openlocfilehash: 0119e60a6493ed13b04b36fc5fccc7227503fe0a
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2018
ms.locfileid: "53015083"
---
# <a name="create-and-run-a-machine-learning-pipeline-using-azure-machine-learning-sdk"></a>Skapa och köra en machine learning-pipeline med hjälp av Azure Machine Learning-SDK

I den här artikeln får du lära dig hur du skapar, publicera, köra och spåra en [maskininlärningspipeline](concept-ml-pipelines.md) med hjälp av den [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).  Dessa pipelines att skapa och hantera de arbetsflöden som ihop olika faser av machine learning. Varje fas i en pipeline som förberedelse av data och modellträning, kan innehålla ett eller flera steg.

Pipelines som du skapar är synliga för medlemmar i din Azure Machine Learning-tjänsten [arbetsytan](how-to-manage-workspace.md). 

Pipelines Använd remote beräkningsmål för beräkning och lagring av mellanliggande och slutlig data som är associerade med denna pipeline.  Pipelines kan läsa och skrivdata till och från stöds [Azure storage](https://docs.microsoft.com/azure/storage/) platser.


## <a name="prerequisites"></a>Förutsättningar

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://aka.ms/AMLfree) innan du börjar.

* [Konfigurera utvecklingsmiljön](how-to-configure-environment.md) att installera Azure Machine Learning-SDK.

* Skapa en [Azure Machine Learning-arbetsyta](how-to-configure-environment.md#workspace) att lagra alla dina pipeline-resurser. 

 ```python
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

* Konfigurera en `DataReference` objekt så att den pekar till data som finns i eller är tillgänglig i ett datalager.

* Konfigurera den [beräkningsmål](concept-azure-machine-learning-architecture.md#compute-target) som din pipeline-stegen körs.

### <a name="set-up-a-datastore"></a>Konfigurera ett datalager
Ett datalager lagrar data för att få åtkomst till pipelinen.  Varje arbetsyta har ett standard-datalager. Du kan registrera ytterligare datalager. 

När du skapar arbetsytan och en [Azure-fillagring](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) och en [blob-lagring](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) är kopplade till arbetsytan som standard.  Azure file storage är ”standard datalager” för en arbetsyta, men du kan också använda blob storage som ett datalager.  Läs mer om [Azure-lagringsalternativen](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks). 

```python
# Default datastore (Azure file storage)
def_data_store = ws.get_default_datastore() 

# The above call is equivalent to this 
def_data_store = Datastore(ws, "workspacefilestore")

# Get blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")
```

Ladda upp filer eller kataloger till databasen för att de ska vara tillgängliga från dina pipelines.  Det här exemplet använder blob storage-version av databasen:

```python
def_blob_store.upload_files(
    ["./data/20news.pkl"],
    target_path="20newsgroups", 
    overwrite=True)
```

En pipeline består av en eller flera steg.  Ett steg är en enhet som kör på ett beräkningsmål.  Anvisningarna kan använda datakällor och producerar de data som ”mellanliggande”. Ett steg kan skapa data, till exempel en modell, en katalog med modellen och beroende filer eller tillfälliga data.  Dessa data är sedan tillgänglig för andra steg senare i pipelinen.

### <a name="configure-data-reference"></a>Konfigurera data-referens

Du skapade en datakälla som kan refereras i en pipeline som indata till ett steg. En datakälla i en pipeline representeras av en [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) objekt. Den `DataReference` objekt som pekar på data som finns i eller kan nås från ett datalager.

```python
blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

Mellanliggande data (eller utdata från ett steg) representeras av en [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) objekt. `output_data1` framställs som utdata för ett steg och används som indata för en eller flera senare steg.  `PipelineData` introducerar ett databeroende mellan steg och skapar en implicit körningsordning i pipelinen.

```python
output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

### <a name="set-up-compute"></a>Konfigurera beräkning

I Azure Machine Learning refererar beräkning (eller beräkningsmål) till datorer eller kluster som utför beräkningssteg i din pipeline för machine learning. Exempel: du kan skapa en Azure beräkning av Machine Learning för att köra dina steg.

```python
compute_name = "aml-compute"
 if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size, # NC6 is GPU-enabled
                                                                min_nodes = 1, 
                                                                max_nodes = 4)
     # create the compute target
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # Can poll for a minimum number of nodes and for a specific timeout. 
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current cluster status, use the 'status' property    
    print(compute_target.status.serialize())
```

## <a name="construct-your-pipeline-steps"></a>Skapa pipeline-steg

Nu är det dags att definiera en pipeline-steget. Det finns många inbyggda steg som är tillgängliga via Azure Machine Learning SDK. Den mest grundläggande av de här stegen är en `PythonScriptStep` som kör ett pythonskript i en angiven beräkningsmål.

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

När du har definierat dina steg skapar du pipelinen med hjälp av en eller flera av de här stegen.

>[!NOTE]
>Ingen fil eller data har överförts till Azure Machine Learning-tjänsten när du definierar stegen eller skapa pipelinen.

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

## <a name="submit-the-pipeline"></a>Skicka pipelinen

När du skickar pipelinen kontrolleras beroenden för varje steg och en ögonblicksbild av den mapp som angetts som källkatalogen har överförts till Azure Machine Learning-tjänsten.  Om ingen källkatalog har angetts, laddas den aktuella lokala katalogen.

```python
# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
```

Första gången du kör en pipeline:

* Ögonblicksbilden projekt laddas ned till beräkningsmål från blob storage som är kopplade till arbetsytan.
* En docker-avbildning skapas för varje steg i pipelinen.
* Docker-avbildning för varje steg hämtas till beräkningsmål från container registry.
* Om en `DataReference` objekt har angetts i ett steg datalagringen är monterad. Om monteringspunkten inte stöds, i stället kopieras data till målet för beräkning.
* Steget körs i beräkningsmål som anges i steg definition. 
* Artefakter som loggar, stdout och stderr, mått och utdata som anges av steget skapas. Dessa artefakter överförs sedan och sparas i användarens standard datalager.

![Kör ett experiment som en pipeline](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

## <a name="publish-a-pipeline"></a>Publicera en pipeline

Du kan publicera en pipeline kan köras med olika indata senare. För REST-slutpunkt för ett redan publicerat pipeline att acceptera parametrar måste pipelinen parametriseras innan du publicerar. 

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

Alla publicerade pipelines har en REST-slutpunkt för att anropa körning av pipelinen från externa system, till exempel-Python-klienter. Den här slutpunkten är ett sätt för ”hanterad repeterbarhet” i batch bedömning och träna scenarier.

För att anropa körning av föregående pipelinen, behöver du en rubrik för Azure Active Directory autentiseringstoken som beskrivs i [AzureCliAuthentication-klass](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py)

```python
response = requests.post(published_pipeline1.endpoint, 
    headers=aad_token, 
    json={"ExperimentName": "My_Pipeline",
        "ParameterAssignments": {"pipeline_arg": 20}})
```
## <a name="view-results"></a>Visa resultat

Se en lista över alla dina pipelines och deras körningsinformation:
1. Logga in på [Azure Portal](https://portal.azure.com/).  

1. [Visa arbetsytan](how-to-manage-workspace.md#view-a-workspace) att hitta listan över pipeliner.
 ![lista över machine learning pipelines](./media/how-to-create-your-first-pipeline/list_of_pipelines.png)
 
1. Välj en specifik pipeline för att visa Körningsresultat.

## <a name="next-steps"></a>Nästa steg
- Använd [dessa Jupyter-anteckningsböcker på GitHub](https://aka.ms/aml-pipeline-readme) att utforska machine learning-ytterligare pipelines.
- Läsa hjälpen för SDK-referens för den [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) paketet och [azureml-pipelines-steg](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) paketet.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
