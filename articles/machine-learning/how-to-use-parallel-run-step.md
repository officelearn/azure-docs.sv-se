---
title: Kör batchprognoser för stordata
titleSuffix: Azure Machine Learning
description: Lär dig hur du får slutsatser asynkront på stora mängder data med hjälp av ParallelRunStep i Azure Machine Learning. ParallelRunStep ger parallella bearbetningsfunktioner ur lådan och optimerar för hög genomströmning, brand-och-glömma inferens för stordata användningsfall.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: vaidyas
author: vaidya-s
ms.date: 01/15/2020
ms.custom: Ignite2019
ms.openlocfilehash: 3d283d1094336b928869aa281b4a640d7a62dd94
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79477195"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>Kör batchinferens på stora mängder data med hjälp av Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Lär dig hur du bearbetar stora mängder data asynkront och parallellt med hjälp av Azure Machine Learning. Funktionen ParallelRunStep som beskrivs här är i offentlig förhandsversion. Det är ett högpresterande och högpresterande sätt att generera slutsatser och bearbetning av data. Det ger asynkrona funktioner ur lådan.

Med ParallelRunStep är det enkelt att skala offlineinferenser till stora kluster av datorer på terabyte produktionsdata, vilket resulterar i förbättrad produktivitet och optimerad kostnad.

I den här artikeln får du lära dig följande uppgifter:

> * Skapa en fjärrberäkningsresurs.
> * Skriv ett anpassat inferensskript.
> * Skapa en pipeline för [maskininlärning](concept-ml-pipelines.md) för att registrera en förtränad bildklassificeringsmodell baserat på [MNIST-datauppsättningen.](https://publicdataset.azurewebsites.net/dataDetail/mnist/) 
> * Använd modellen för att köra batchuppfruktning på exempelavbildningar som är tillgängliga i ditt Azure Blob-lagringskonto. 

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

* För en guidad snabbstart slutför du [installationshandledningen](tutorial-1st-experiment-sdk-setup.md) om du inte redan har en Azure Machine Learning-arbetsyta eller virtuell anteckningsbok. 

* Om du vill hantera din egen miljö och dina egna beroenden läser du [hur du konfigurerar](how-to-configure-environment.md) din egen miljö. Kör `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-contrib-pipeline-steps` i din miljö för att hämta nödvändiga beroenden.

## <a name="set-up-machine-learning-resources"></a>Konfigurera maskininlärningsresurser

Följande åtgärder ställer in de resurser som du behöver för att köra en kommandopipeline:

- Skapa ett datalager som pekar på en blob-behållare som har bilder att dra slutsatser om.
- Ställ in datareferenser som indata och utdata för batchinferenspipelssteget.
- Konfigurera ett beräkningskluster för att köra batchupptråhetssteget.

### <a name="create-a-datastore-with-sample-images"></a>Skapa ett datalager med exempelbilder

Hämta MNIST-utvärderingsuppsättningen från `sampledata` den offentliga `pipelinedata`blob-behållaren på ett konto med namnet . Skapa ett datalager `mnist_datastore`med namnet , som pekar på den här behållaren. I följande anrop `register_azure_blob_container`till `overwrite` skriver `True` du om flaggan så att alla datalager som har skapats tidigare med det namnet skrivs över. 

Du kan ändra det här steget så att det `datastore_name`pekar `container_name`på `account_name`blob-behållaren genom att ange egna värden för , och .

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

Ange sedan standarddatalagret för arbetsyta som utdatalager. Du använder den för slutminuter.

När du skapar arbetsytan är [Azure-filer](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) och [Blob-lagring](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) som standard kopplade till arbetsytan. Azure Files är standarddatalager för en arbetsyta, men du kan också använda Blob-lagring som ett datalager. Mer information finns i [Azure-lagringsalternativ](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-inputs-and-outputs"></a>Konfigurera indata och utdata

Nu måste du konfigurera dataindata och utdata, inklusive:

- Katalogen som innehåller indataavbildningarna.
- Katalogen där den förutbildade modellen lagras.
- Katalogen som innehåller etiketterna.
- Katalogen för utdata.

[`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)är en klass för att utforska, omvandla och hantera data i Azure Machine Learning. Den här klassen [`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) har [`FileDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py)två typer: och . I det här exemplet `FileDataset` ska du använda som indata till batchinferens pipeline-steget. 

> [!NOTE] 
> `FileDataset`support i batchinferens är begränsad till Azure Blob-lagring för tillfället. 

Du kan också referera till andra datauppsättningar i ditt anpassade inferensskript. Du kan till exempel använda den för att komma åt `Dataset.register` `Dataset.get_by_name`etiketter i skriptet för att märka bilder med hjälp av och .

Mer information om Azure Machine Learning-datauppsättningar finns i [Skapa och komma åt datauppsättningar (förhandsversion)](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets).

[`PipelineData`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)objekt används för att överföra mellanliggande data mellan rörledningssteg. I det här exemplet använder du den för slutminuter.

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

### <a name="set-up-a-compute-target"></a>Ställ in ett beräkningsmål

I Azure Machine Learning refererar *beräkning* (eller *beräkningsmål)* till datorer eller kluster som utför beräkningsstegen i din machine learning-pipeline. Kör följande kod för att skapa ett CPU-baserat [AmlCompute-mål.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py)

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

## <a name="prepare-the-model"></a>Förbered modellen

[Hämta den förtränade bildklassificeringsmodellen](https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz)och `models` extrahera den sedan till katalogen.

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

Registrera sedan modellen med arbetsytan så att den är tillgänglig för fjärrberäkningsresursen.

```python
from azureml.core.model import Model

# Register the downloaded model 
model = Model.register(model_path="models/",
                       model_name="mnist",
                       tags={'pretrained': "mnist"},
                       description="Mnist trained tensorflow model",
                       workspace=ws)
```

## <a name="write-your-inference-script"></a>Skriv ditt inferensskript

>[!Warning]
>Följande kod är bara ett exempel som [exempel anteckningsboken](https://aka.ms/batch-inference-notebooks) använder. Du måste skapa ett eget skript för ditt scenario.

Skriptet *måste innehålla* två funktioner:
- `init()`: Använd den här funktionen för kostsamma eller vanliga förberedelser för senare inferens. Använd den till exempel för att läsa in modellen i ett globalt objekt. Den här funktionen anropas bara en gång i början av processen.
-  `run(mini_batch)`: Funktionen körs för `mini_batch` varje instans.
    -  `mini_batch`: Parallellt körningssteg anropar körningsmetoden och skickar antingen en lista eller Pandas DataFrame som ett argument till metoden. Varje post i min_batch kommer att vara - en filsökväg om indata är en FileDataset, en Pandas DataFrame om indata är en TabularDataset.
    -  `response`: metoden run() ska returnera en Pandas DataFrame eller en matris. För append_row output_action läggs dessa returnerade element till i den gemensamma utdatafilen. För summary_only ignoreras innehållet i elementen. För alla utdataåtgärder anger varje returnerat utdataelement en lyckad körning av indataelementet i minibatchen för indata. Du bör se till att tillräckligt med data ingår i körningsresultatet för att mappa indata för att köra resultatet. Kör utdata kommer att skrivas i utdatafilen och inte garanterat vara i ordning, bör du använda några viktiga i utdata för att mappa den till indata.

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

### <a name="how-to-access-other-files-in-source-directory-in-entry_script"></a>Så här kommer du åt andra filer i källkatalogen i entry_script

Om du har en annan fil eller mapp i samma katalog som ditt postskript kan du referera till den genom att hitta den aktuella arbetskatalogen.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

## <a name="build-and-run-the-pipeline-containing-parallelrunstep"></a>Skapa och kör pipelinen som innehåller ParallelRunStep

Nu har du allt du behöver för att bygga rörledningen.

### <a name="prepare-the-run-environment"></a>Förbered körningsmiljön

Ange först beroenden för skriptet. Du använder det här objektet senare när du skapar pipeline-steget.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

batch_conda_deps = CondaDependencies.create(pip_packages=["tensorflow==1.13.1", "pillow"])

batch_env = Environment(name="batch_environment")
batch_env.python.conda_dependencies = batch_conda_deps
batch_env.docker.enabled = True
batch_env.docker.base_image = DEFAULT_GPU_IMAGE
batch_env.spark.precache_packages = False
```

### <a name="specify-the-parameters-for-your-batch-inference-pipeline-step"></a>Ange parametrarna för ditt pipeline-steg för inferens för batch

`ParallelRunConfig`är huvudkonfigurationen för den nyligen introducerade `ParallelRunStep` batchinferensinstansen i Azure Machine Learning-pipelinen. Du använder den för att radbrytas i skriptet och konfigurera nödvändiga parametrar, inklusive alla följande parametrar:
- `entry_script`: Ett användarskript som en lokal filsökväg som ska köras parallellt på flera noder. Om `source_directory` det finns, använd en relativ sökväg. Annars kan du använda valfri bana som är tillgänglig på datorn.
- `mini_batch_size`: Minibatchens storlek skickas till `run()` ett enda samtal. (valfritt; standardvärdet `10` är filer för `1MB` FileDataset och för Tabelldatauppsättning.)
    - För `FileDataset`är det antalet filer med ett `1`minimivärde på . Du kan kombinera flera filer i en minibatch.
    - För `TabularDataset`är det storleken på data. Exempelvärden `1024`är `1024KB` `10MB`, `1GB`, och . Det rekommenderade `1MB`värdet är . Minibatchen från `TabularDataset` kommer aldrig att korsa filgränser. Om du till exempel har CSV-filer med olika storlekar är den minsta filen 100 KB och den största är 10 MB. Om du `mini_batch_size = 1MB`anger behandlas filer med en storlek som är mindre än 1 MB som en minibatch. Filer med en storlek som är större än 1 MB kommer att delas upp i flera minibatch.
- `error_threshold`: Antalet postfel för `TabularDataset` och filfel `FileDataset` för det bör ignoreras under bearbetningen. Om felantalet för hela indata går över det här värdet avbryts jobbet. Feltröskeln gäller för hela indata och inte `run()` för enskilda minibatchar som skickas till metoden. Intervallet är `[-1, int.max]`. Delen `-1` anger att ignorera alla fel under bearbetningen.
- `output_action`: Ett av följande värden anger hur utdata ska ordnas:
    - `summary_only`: Användarskriptet lagrar utdata. `ParallelRunStep`använder utdata endast för beräkningen av feltröskel.
    - `append_row`: För alla indatafiler skapas bara en fil i utdatamappen för att lägga till alla utdata avgränsade efter rad. Filnamnet kommer `parallel_run_step.txt`att vara .
- `source_directory`: Sökvägar till mappar som innehåller alla filer som ska köras på beräkningsmålet (valfritt).
- `compute_target`: `AmlCompute` Endast stöds.
- `node_count`: Antalet beräkningsnoder som ska användas för att köra användarskriptet.
- `process_count_per_node`: Antalet processer per nod.
- `environment`: Python-miljödefinitionen. Du kan konfigurera den för att använda en befintlig Python-miljö eller för att konfigurera en tillfällig miljö för experimentet. Definitionen är också ansvarig för att ange nödvändiga programberoenden (valfritt).
- `logging_level`: Logga verbosity. Värden i ökande verbositet är: `WARNING`, `INFO`och `DEBUG`. (valfritt, standardvärdet `INFO`är)
- `run_invocation_timeout`: `run()` Tidsgränsen för metodanrop på några sekunder. (valfritt; standardvärdet är `60`)

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

Skapa pipeline-steget med hjälp av skript, miljökonfiguration och parametrar. Ange beräkningsmålet som du redan har kopplat till arbetsytan som mål för körningen för skriptet. Används `ParallelRunStep` för att skapa batch inferenspipelspipelet, som tar alla följande parametrar:
- `name`: Namnet på steget, med följande namngivningsbegränsningar: unika, 3-32 tecken och regex ^\[a-z\]([-a-z0-9]*[a-z0-9])?$.
- `models`: Noll eller fler modellnamn som redan registrerats i Azure Machine Learning-modellregistret.
- `parallel_run_config`: `ParallelRunConfig` Ett objekt, enligt definitionen tidigare.
- `inputs`: En eller flera single-typed Azure Machine Learning-datauppsättningar.
- `output`: `PipelineData` Ett objekt som motsvarar utdatakatalogen.
- `arguments`: En lista med argument som skickas till användarskriptet (valfritt).
- `allow_reuse`: Om steget ska återanvända tidigare resultat när det körs med samma inställningar/ingångar. Om den `False`här parametern är, genereras alltid en ny körning för det här steget under pipelinekörning. (valfritt, standardvärdet `True`är .)

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

>[!Note]
> Ovanstående steg beror `azureml-contrib-pipeline-steps`på , enligt beskrivningen i [Förutsättningar](#prerequisites). 

### <a name="submit-the-pipeline"></a>Skicka pipelinen

Kör pipelinen. Skapa först [`Pipeline`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) ett objekt med hjälp av arbetsytans referens och det pipeline-steg som du skapade. Parametern `steps` är en matris med steg. I det här fallet finns det bara ett steg för batchbedömning. Om du vill skapa pipelines som har flera steg placerar du stegen i ordning i den här matrisen.

Använd sedan `Experiment.submit()` funktionen för att skicka pipelinen för körning.

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
pipeline_run = Experiment(ws, 'digit_identification').submit(pipeline)
```

## <a name="monitor-the-parallel-run-job"></a>Övervaka det parallella körningsjobbet

Ett batchupptråligen kan ta lång tid att slutföra. I det här exemplet övervakas förloppet med hjälp av en Jupyter-widget. Du kan också hantera jobbets förlopp med hjälp av:

* Azure Machine Learning Studio. 
* Konsolutdata [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py) från objektet.

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="next-steps"></a>Nästa steg

Om du vill se den här processen fungerar från till provar du [den batchanledningsanteckningsbok](https://aka.ms/batch-inference-notebooks). 

Felsökning och felsökningsvägledning för ParallelRunStep finns i [guiden för hur du söker](how-to-debug-parallel-run-step.md).

Felsökning och felsökning av vägledning för pipelines finns i instruktioner för [hur du kan styra](how-to-debug-pipelines.md).

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

