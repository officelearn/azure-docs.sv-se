---
title: Köra batch-förutsägelser för Big data
titleSuffix: Azure Machine Learning
description: Lär dig hur du får inferences asynkront på stora mängder data med hjälp av batch-härledning i Azure Machine Learning. Batch-härledning innehåller parallella bearbetnings möjligheter och optimerar för hög genom strömning, Fire-och-glömma-härledning för stora data användnings fall.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: vaidyas
author: vaidya-s
ms.date: 11/04/2019
ms.custom: Ignite2019
ms.openlocfilehash: 62a2c3324df70c7ccdbbac273d314ff94cbb7b9a
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671567"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>Kör batch-härledning på stora mängder data med hjälp av Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här instruktionen får du lära dig hur du får inferences på stora mängder data asynkront och parallellt med hjälp av Azure Machine Learning. Den funktion för batch-härledning som beskrivs här finns i offentlig för hands version. Det är ett högpresterande och högt genomflöde sätt att generera inferences och bearbeta data. Den ger asynkrona funktioner från lådan.

Med batch-härledning är det enkelt att skala offline-inferences till stora kluster datorer i terabyte av produktions data, vilket resulterar i förbättrad produktivitet och optimerad kostnad.

I den här instruktionen får du lära dig följande uppgifter:

> * Skapa en fjärran sluten beräknings resurs.
> * Skriv ett anpassat härlednings skript.
> * Skapa en [pipeline för maskin inlärning](concept-ml-pipelines.md) för att registrera en förtränad bild klassificerings modell baserad på [MNIST](https://publicdataset.azurewebsites.net/dataDetail/mnist/) -datauppsättningen. 
> * Använd modellen för att köra batch-härledning på exempel avbildningar som är tillgängliga i ditt Azure Blob Storage-konto. 

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

* För en guidad snabb start slutför du [installations självstudien](tutorial-1st-experiment-sdk-setup.md) om du inte redan har en Azure Machine Learning arbets yta eller en virtuell dator. 

* Information om hur du hanterar din egen miljö och beroenden finns i [instruktionen så här](how-to-configure-environment.md) konfigurerar du din egen miljö. Kör `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-contrib-pipeline-steps` i din miljö för att ladda ned nödvändiga beroenden.

## <a name="set-up-machine-learning-resources"></a>Konfigurera Machine Learning-resurser

Följande åtgärder konfigurerar de resurser som du behöver för att köra en pipeline för batch-härledning:

- Skapa ett data lager som pekar på en BLOB-behållare som har avbildningar som ska överläggas.
- Konfigurera data referenser som indata och utdata för pipeline-steget för batch-härledning.
- Konfigurera ett beräknings kluster för att köra steget för batch-härledning.

### <a name="create-a-datastore-with-sample-images"></a>Skapa ett data lager med exempel bilder

Hämta utvärderings versionen av MNIST från den offentliga BLOB-behållaren `sampledata` på ett konto med namnet `pipelinedata`. Skapa ett data lager med namnet `mnist_datastore`, som pekar på den här behållaren. I följande anrop till `register_azure_blob_container`anger flaggan `overwrite` till `True` skriva över alla data lager som skapats tidigare med det namnet. 

Du kan ändra det här steget så att det pekar på din BLOB-behållare genom att ange dina egna värden för `datastore_name`, `container_name`och `account_name`.

```python
from azureml.core import Datastore
from azureml.core import Workspace

# Load workspace authorization details from config.json
ws = Workspace.from_config()

mnist_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="mnist_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata",
                      overwrite=True)
```

Ange sedan standard data lagret för arbets ytan som utdata-datalager. Du använder den för att få en utmatnings effekt.

När du skapar din arbets yta är [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) och [Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) kopplade till arbets ytan som standard. Azure Files är standard data lagret för en arbets yta, men du kan också använda Blob Storage som ett data lager. Mer information finns i [alternativ för Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-inputs-and-outputs"></a>Konfigurera indata och utdata

Nu måste du konfigurera indata och utdata, inklusive:

- Den katalog som innehåller de inmatade bilderna.
- Katalogen där den förtränade modellen lagras.
- Den katalog som innehåller etiketterna.
- Katalogen för utdata.

`Dataset` är en klass för att utforska, transformera och hantera data i Azure Machine Learning. Den här klassen har två typer: `TabularDataset` och `FileDataset`. I det här exemplet ska du använda `FileDataset` som indata för pipeline-steget för batch-härledning. 

> [!NOTE] 
> `FileDataset` stöd i batch-härledning är begränsat till Azure Blob Storage för tillfället. 

Du kan också referera till andra data uppsättningar i ditt anpassade härlednings skript. Du kan till exempel använda den för att få åtkomst till etiketter i skriptet för att märka bilder med hjälp av `Dataset.register` och `Dataset.get_by_name`.

Mer information om Azure Machine Learning data uppsättningar finns i [skapa och komma åt data uppsättningar (för hands version)](https://docs.microsoft.com/azure/machine-learning/service/how-to-create-register-datasets).

`PipelineData` objekt används för att överföra mellanliggande data mellan pipeline-steg. I det här exemplet använder du den för utgångs-och utgångs störningar.

```python
from azureml.core.dataset import Dataset

mnist_ds_name = 'mnist_sample_data'

path_on_datastore = mnist_blob.path('mnist/')
input_mnist_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
registered_mnist_ds = input_mnist_ds.register(ws, mnist_ds_name, create_new_version=True)
named_mnist_ds = registered_mnist_ds.as_named_input(mnist_ds_name)

output_dir = PipelineData(name="inferences", 
                          datastore=def_data_store, 
                          output_path_on_compute="mnist/results")
```

### <a name="set-up-a-compute-target"></a>Konfigurera ett beräknings mål

I Azure Machine Learning refererar *beräkning* (eller *beräknings mål*) till de datorer eller kluster som utför beräknings stegen i din Machine Learning-pipeline. Kör följande kod för att skapa ett CPU-baserat [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) -mål.

```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

## <a name="prepare-the-model"></a>Förbereda modellen

[Hämta den förtränade avbildnings klassificerings modellen](https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz)och extrahera den sedan till `models`-katalogen.

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url="https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

Registrera sedan modellen med arbets ytan så att den är tillgänglig för fjärrberäknings resursen.

```python
from azureml.core.model import Model

# Register the downloaded model 
model = Model.register(model_path="models/",
                       model_name="mnist",
                       tags={'pretrained': "mnist"},
                       description="Mnist trained tensorflow model",
                       workspace=ws)
```

## <a name="write-your-inference-script"></a>Skriv ditt härlednings skript

>[!Warning]
>Följande kod är bara ett exempel som används av [exempel antecknings boken](https://aka.ms/batch-inference-notebooks) . Du måste skapa ditt eget skript för ditt scenario.

Skriptet *måste innehålla* två funktioner:
- `init()`: Använd den här funktionen för eventuell kostsam eller vanlig förberedelse för senare härledning. Använd till exempel den för att läsa in modellen i ett globalt objekt.
-  `run(mini_batch)`: funktionen kommer att köras för varje `mini_batch`-instans.
    -  `mini_batch`: batch-härledning kommer att anropa körnings metoden och skicka antingen en lista eller Pandas DataFrame som ett argument till metoden. Varje post i min_batch blir en fil Sök väg om inmatningen är en FileDataset, en Pandas-DataFrame om inmatningen är en TabularDataset.
    -  `response`: Run ()-metoden ska returnera en Pandas-DataFrame eller en matris. För append_row output_action läggs dessa returnerade element till i den gemensamma utdatafilen. För summary_only ignoreras innehållet i elementen. För alla utdata-åtgärder anger varje returnerat utdata en lyckad härledning av indata-element i mini-batch för indata. Användaren bör se till att tillräckligt med data ingår i utgångs resultatet för att mappa indata till härledning. Utmatnings utmatningen skrivs i utdatafilen och är inte garanterat i ordning. användaren bör använda viss nyckel i utdata för att mappa den till indata.

```python
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://aka.ms/batch-inference-notebooks)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

## <a name="build-and-run-the-batch-inference-pipeline"></a>Skapa och kör pipeline för batch-härledning

Nu har du allt du behöver för att bygga pipelinen.

### <a name="prepare-the-run-environment"></a>Förbereda körnings miljön

Börja med att ange beroenden för skriptet. Du använder det här objektet senare när du skapar pipeline-steget.

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

batch_conda_deps = CondaDependencies.create(pip_packages=["tensorflow==1.13.1", "pillow"])

batch_env = Environment(name="batch_environment")
batch_env.python.conda_dependencies = batch_conda_deps
batch_env.docker.enabled = True
batch_env.docker.base_image = DEFAULT_GPU_IMAGE
batch_env.spark.precache_packages = False
```

### <a name="specify-the-parameters-for-your-batch-inference-pipeline-step"></a>Ange parametrar för pipeline-steget för batch-härledning

`ParallelRunConfig` är den viktigaste konfigurationen för den nyligen introducerade batch-härledningen `ParallelRunStep` instansen i Azure Machine Learning pipelinen. Du använder den för att omsluta ditt skript och konfigurera nödvändiga parametrar, inklusive alla följande:
- `entry_script`: ett användar skript som en lokal fil Sök väg som ska köras parallellt på flera noder. Om `source_directly` finns använder du en relativ sökväg. Annars använder du valfri sökväg som är tillgänglig på datorn.
- `mini_batch_size`: storleken på den mini-batch som skickades till ett enda `run()`-anrop. (Valfritt; standardvärdet är `1`.)
    - För `FileDataset`är det antalet filer med ett minsta värde för `1`. Du kan kombinera flera filer i en mini-batch.
    - För `TabularDataset`är det data storleken. Exempel värden är `1024`, `1024KB`, `10MB`och `1GB`. Det rekommenderade värdet är `1MB`. Observera att mini-batch från `TabularDataset` aldrig kommer att överskrida fil gränser. Om du till exempel har CSV-filer med olika storlekar är den minsta filen 100 KB och störst är 10 MB. Om du anger `mini_batch_size = 1MB`kommer filer med en storlek som är mindre än 1 MB att behandlas som en mini-batch. Filer med en storlek som är större än 1 MB delas upp i flera mini-batchar.
- `error_threshold`: antalet felaktiga poster för `TabularDataset` och fil haverier för `FileDataset` som ska ignoreras under bearbetningen. Om antalet fel för hela inflödet hamnar ovanför det här värdet kommer jobbet att stoppas. Fel tröskeln är för alla indatatyper och inte för enskilda mini-batchar som skickas till `run()`-metoden. Intervallet är `[-1, int.max]`. `-1` delen indikerar att ignorera alla avbrott under bearbetningen.
- `output_action`: något av följande värden anger hur utdata ska organiseras:
    - `summary_only`: användar skriptet kommer att lagra utdata. `ParallelRunStep` kommer bara att använda utdata för fel tröskel beräkningen.
    - `append_row`: för alla indatafiler skapas bara en fil i mappen utdata för att lägga till alla utdata avgränsade med rad. Fil namnet kommer att parallel_run_step. txt.
- `source_directory`: sökvägar till mappar som innehåller alla filer som ska köras på beräknings målet (valfritt).
- `compute_target`: endast `AmlCompute` stöds.
- `node_count`: antalet beräknade datornoder som ska användas för att köra användar skriptet.
- `process_count_per_node`: antalet processer per nod.
- `environment`: python-miljö definitionen. Du kan konfigurera den att använda en befintlig python-miljö eller konfigurera en tillfällig miljö för experimentet. Definitionen är också ansvarig för att ange nödvändiga program beroenden (valfritt).
- `logging_level`: utförlig loggning. Värden i ökande utförlighet är: `WARNING`, `INFO`och `DEBUG`. Standardvärdet är `INFO` (valfritt).
- `run_invocation_timeout`: tids gränsen för anrop av `run()`s metoden i sekunder. Standardvärdet är `60`.

```python
from azureml.contrib.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    source_directory=scripts_folder,
    entry_script="digit_identification.py",
    mini_batch_size="5",
    error_threshold=10,
    output_action="append_row",
    environment=batch_env,
    compute_target=compute_target,
    node_count=4)
```

### <a name="create-the-pipeline-step"></a>Skapa pipeline-steget

Skapa pipeline-steget med hjälp av skriptet, miljö konfigurationen och parametrarna. Ange det beräknings mål som du redan har kopplat till din arbets yta som mål för körning av skriptet. Använd `ParallelRunStep` för att skapa pipeline-steget för batch-härledning, som tar alla följande parametrar:
- `name`: namnet på steget med följande namngivnings begränsningar: unika, 3-32 tecken och regex ^\[a-z\]([-a-Z0-9] * [a-Z0-9])? $.
- `models`: noll eller flera modell namn har redan registrerats i Azure Machine Learning Model-registret.
- `parallel_run_config`: ett `ParallelRunConfig`-objekt, som definieras tidigare.
- `inputs`: en eller flera Azure Machine Learning data uppsättningar med enkel typ.
- `output`: ett `PipelineData`-objekt som motsvarar utdata-katalogen.
- `arguments`: en lista över argument som skickas till användar skriptet (valfritt).
- `allow_reuse`: om steget ska återanvända tidigare resultat när de körs med samma inställningar/indata. Om den här parametern är `False`skapas alltid en ny körning för det här steget under pipeline-körningen. (Valfritt; standardvärdet är `True`.)

```python
from azureml.contrib.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="batch-mnist",
    models=[model],
    parallel_run_config=parallel_run_config,
    inputs=[named_mnist_ds],
    output=output_dir,
    arguments=[],
    allow_reuse=True
)
```

### <a name="run-the-pipeline"></a>Köra en pipeline

Kör nu pipelinen. Skapa först ett `Pipeline`-objekt med hjälp av din arbets ytans referens och det steg för pipeline som du skapade. Parametern `steps` är en matris med steg. I det här fallet finns det bara ett steg för batch-poäng. Placera stegen i den här matrisen för att bygga pipeliner som har flera steg.

Använd sedan `Experiment.submit()`-funktionen för att skicka pipelinen för körning.

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
pipeline_run = Experiment(ws, 'digit_identification').submit(pipeline)
```

## <a name="monitor-the-batch-inference-job"></a>Övervaka jobbets härlednings jobb

Det kan ta lång tid att slutföra en batch-härledning. Det här exemplet övervakar förloppet med hjälp av en Jupyter-widget. Du kan också hantera jobbets förlopp genom att använda:

* Azure Machine Learning Studio. 
* Konsol utdata från [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py) -objektet.

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="next-steps"></a>Nästa steg

Om du vill se den här processens arbets slut till slut punkt, kan du testa den [bärbara datorn](https://aka.ms/batch-inference-notebooks). 

För fel sökning och fel söknings vägledning för pipelines, se [instruktionen instruktion](how-to-debug-pipelines.md).

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

