---
title: Köra batch-förutsägelser på stora data med pipelines
titleSuffix: Azure Machine Learning
description: Lär dig hur du gör batch förutsägelse asynkront på stora mängder data med hjälp av Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens, garye
ms.author: jordane
author: jpe316
ms.date: 07/12/2019
ms.openlocfilehash: 3997f327bd6512eeee2cb5e7a0af802f12d1727a
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034295"
---
# <a name="run-batch-predictions-on-large-data-sets-with-azure-machine-learning-pipelines"></a>Köra batch-förutsägelser på stora data mängder med Azure Machine Learning pipelines

I den här artikeln får du lära dig hur du gör förutsägelser för stora mängder data asynkront med hjälp av ML-pipelines med Azure Machine Learning.

Batch förutsägelse (eller batch-Poäng) tillhandahåller kostnads effektiv härledning, med oöverträffat data flöde för asynkrona program. Batch förutsägelse pipelines kan skala för att utföra inferens på terabyte av produktionsdata. Batch förutsägelse är optimerad för hög genom strömning, Fire-och-glömma förutsägelser för en stor data insamling.

>[!TIP]
> Om systemet kräver bearbetning med låg latens (för att bearbeta ett enskilt dokument eller litet antal dokument snabbt) använder du [resultat i real tid](how-to-consume-web-service.md) i stället för batch-förutsägelse.

I följande steg skapar du en [maskin inlärnings pipeline](concept-ml-pipelines.md) för att registrera en förtränad modell för visuellt innehåll (INSTART[-v3](https://arxiv.org/abs/1512.00567)). Sedan använder du den förtränade modellen för att göra en batch-bedömning av tillgängliga avbildningar i ditt Azure Blob Storage-konto. Dessa avbildningar som används för bedömning är utan etikett avbildningar från den [ImageNet](http://image-net.org/) datauppsättning.

## <a name="prerequisites"></a>Förutsättningar

- Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

- Konfigurera din utvecklingsmiljö för att installera Azure Machine Learning-SDK. Mer information finns i [konfigurera en utvecklingsmiljö för Azure Machine Learning](how-to-configure-environment.md).

- Skapa en Azure Machine Learning-arbetsyta som ska innehålla alla pipeline-resurser. Du kan använda följande kod eller fler alternativ finns i [skapar en konfigurationsfil för arbetsytan](how-to-configure-environment.md#workspace).

  ```python
  from azureml.core import Workspace
  ws = Workspace.create(name = '<workspace-name>',
                        subscription_id = '<subscription-id>',
                        resource_group = '<resource-group>',
                        location = '<workspace_region>',
                        exist_ok = True
                        )
  ```

## <a name="set-up-machine-learning-resources"></a>Konfigurera machine learning-resurser

Följande steg konfigurerar de resurser du behöver för att köra en pipeline:

- Få åtkomst till databasen som redan har tränats modellen, inkommande etiketter och bilder för att bedöma (detta är redan inställd för dig).
- Konfigurera ett datalager för att lagra dina utdata.
-  `DataReference`Konfigurera objekt så att de pekar på data i föregående data lager.
- Ställ in beräkning datorer eller kluster där kör pipeline-stegen.

### <a name="access-the-datastores"></a>Komma åt datalagringen

Först få åtkomst till databasen med modellen, etiketter och bilder.

Använd en offentlig BLOB-behållare med namnet *sampleData*i *pipelinedata* -kontot som innehåller bilder från utvärderings uppsättningen för ImageNet. Datastore-namnet för den här offentliga behållare är *images_datastore*. Registrera den här databasen med din arbetsyta:

```python
from azureml.core import Datastore

account_name = "pipelinedata"
datastore_name = "images_datastore"
container_name = "sampledata"

batchscore_blob = Datastore.register_azure_blob_container(ws,
                                                          datastore_name=datastore_name,
                                                          container_name=container_name,
                                                          account_name=account_name,
                                                          overwrite=True)
```

Konfigurera sedan för att använda standard data lagret för utdata.

När du skapar din arbets yta är [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) och [blob-lagringen](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) kopplade till arbets ytan som standard. Azure Files är standard data lagret för en arbets yta, men du kan också använda Blob Storage som ett data lager. Mer information finns i [alternativ för Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-references"></a>Konfigurera datareferenser

Nu kan referera till data i din pipeline som indata till steg för pipeline.

En datakälla i en pipeline representeras av en [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) objekt.  `DataReference`Objektetpekarpådatasomfinnsiellerkannås från ett data lager. Du behöver `DataReference`  objekt för katalogen som används för inmatnings avbildningar, katalogen där den förtränade modellen lagras, katalogen för etiketter och katalogen utdata.

```python
from azureml.data.data_reference import DataReference

input_images = DataReference(datastore=batchscore_blob,
                             data_reference_name="input_images",
                             path_on_datastore="batchscoring/images",
                             mode="download")

model_dir = DataReference(datastore=batchscore_blob,
                          data_reference_name="input_model",
                          path_on_datastore="batchscoring/models",
                          mode="download")

label_dir = DataReference(datastore=batchscore_blob,
                          data_reference_name="input_labels",
                          path_on_datastore="batchscoring/labels",
                          mode="download")

output_dir = PipelineData(name="scores",
                          datastore=def_data_store,
                          output_path_on_compute="batchscoring/results")
```

### <a name="set-up-compute-target"></a>Konfigurera beräkningsmål

I Azure Machine Learning refererar *beräkning* (eller *beräknings mål*) till de datorer eller kluster som utför beräknings stegen i din Machine Learning-pipeline. Du kan till exempel skapa en Azure Machine Learning-beräkning med klassen [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute%28class%29?view=azure-ml-py) .

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget

compute_name = "gpucluster"
compute_min_nodes = 0
compute_max_nodes = 4
vm_size = "STANDARD_NC6"

if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target. just use it. ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(
        vm_size=vm_size,  # NC6 is GPU-enabled
        vm_priority='lowpriority',  # optional
        min_nodes=compute_min_nodes,
        max_nodes=compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws,
                                          compute_name,
                                          provisioning_config)

    compute_target.wait_for_completion(
        show_output=True,
        min_node_count=None,
        timeout_in_minutes=20)
```

## <a name="prepare-the-model"></a>Förbereda modellen

Innan du kan använda pretrained modellen, måste du ladda ned modellen och registrera den med din arbetsyta.

### <a name="download-the-pretrained-model"></a>Ladda ned pretrained modellen

Ladda ned tränats modellen för visuellt innehåll (InceptionV3) från <http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz>. Extrahera den sedan till `models` undermappen.

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url = "http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

### <a name="register-the-model"></a>Registrera modellen

Så här registrerar du modellen:

```python
import shutil
from azureml.core.model import Model

# register downloaded model
model = Model.register(
    model_path="models/inception_v3.ckpt",
    model_name="inception",  # This is the name of the registered model
    tags={'pretrained': "inception"},
    description="Imagenet trained tensorflow inception",
    workspace=ws)
```

## <a name="write-your-scoring-script"></a>Skriv din bedömningsskriptet

>[!Warning]
>Följande kod är bara ett exempel på vad som finns i [batch_score. py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/batch_scoring.py) som används av [exempel antecknings boken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/pipeline-batch-scoring.ipynb). Du måste skapa ett eget bedömnings skript för ditt scenario.

Den `batch_score.py` skriptet tar indatabilder *dataset_path*, tränats modeller i *model_dir,* och matar ut *resultat label.txt* till *output_dir*.

```python
# Snippets from a sample scoring script
# Refer to the accompanying batch-scoring Notebook
# https://github.com/Azure/MachineLearningNotebooks/blob/master/pipeline/pipeline-batch-scoring.ipynb
# for the implementation script

# Get labels
def get_class_label_dict(label_file):
  label = []
  proto_as_ascii_lines = tf.gfile.GFile(label_file).readlines()
  for l in proto_as_ascii_lines:
    label.append(l.rstrip())
  return label

class DataIterator:
  # Definition of the DataIterator here

def main(_):
    # Refer to batch-scoring Notebook for implementation.
    label_file_name = os.path.join(args.label_dir, "labels.txt")
    label_dict = get_class_label_dict(label_file_name)
    classes_num = len(label_dict)
    test_feeder = DataIterator(data_dir=args.dataset_path)
    total_size = len(test_feeder.labels)

    # get model from model registry
    model_path = Model.get_model_path(args.model_name)
    with tf.Session() as sess:
        test_images = test_feeder.input_pipeline(batch_size=args.batch_size)
        with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
            input_images = tf.placeholder(tf.float32, [args.batch_size, image_size, image_size, num_channel])
            logits, _ = inception_v3.inception_v3(input_images,
                                                        num_classes=classes_num,
                                                        is_training=False)
            probabilities = tf.argmax(logits, 1)

        sess.run(tf.global_variables_initializer())
        sess.run(tf.local_variables_initializer())
        coord = tf.train.Coordinator()
        threads = tf.train.start_queue_runners(sess=sess, coord=coord)
        saver = tf.train.Saver()
        saver.restore(sess, model_path)
        out_filename = os.path.join(args.output_dir, "result-labels.txt")

        # copy the file to artifacts
        shutil.copy(out_filename, "./outputs/")
```

## <a name="build-and-run-the-batch-scoring-pipeline"></a>Skapa och köra batchbedömnings-pipeline

### <a name="prepare-the-run-environment"></a>Förbereda körningsmiljön

Ange conda-beroenden för skriptet. Du behöver det här objektet senare när du skapar pipeline-steget.

```python
from azureml.core.runconfig import DEFAULT_GPU_IMAGE
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

cd = CondaDependencies.create(
    pip_packages=["tensorflow-gpu==1.10.0", "azureml-defaults"])

# Runconfig
amlcompute_run_config = RunConfiguration(conda_dependencies=cd)
amlcompute_run_config.environment.docker.enabled = True
amlcompute_run_config.environment.docker.gpu_support = True
amlcompute_run_config.environment.docker.base_image = DEFAULT_GPU_IMAGE
amlcompute_run_config.environment.spark.precache_packages = False
```

### <a name="specify-the-parameter-for-your-pipeline"></a>Ange parametern för din pipeline

Skapa en pipeline-parameter genom att använda ett [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) -objekt med ett standardvärde.

```python
from azureml.pipeline.core.graph import PipelineParameter
batch_size_param = PipelineParameter(
    name="param_batch_size",
    default_value=20)
```

### <a name="create-the-pipeline-step"></a>Skapa pipeline-steg

Skapa pipeline-steget med hjälp av skriptet, miljö konfigurationen och parametrarna. Ange beräkningsmål som du redan har kopplat till din arbetsyta som mål för körningen av skriptet. Använd [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) att skapa pipeline-steget.

```python
from azureml.pipeline.steps import PythonScriptStep
inception_model_name = "inception_v3.ckpt"

batch_score_step = PythonScriptStep(
    name="batch_scoring",
    script_name="batch_score.py",
    arguments=["--dataset_path", input_images,
               "--model_name", "inception",
               "--label_dir", label_dir,
               "--output_dir", output_dir,
               "--batch_size", batch_size_param],
    compute_target=compute_target,
    inputs=[input_images, label_dir],
    outputs=[output_dir],
    runconfig=amlcompute_run_config,
    source_directory=scripts_folder
```

### <a name="run-the-pipeline"></a>Köra en pipeline

Kör nu pipelinen och undersök utdata som den har producerat. Utdata har en poäng som motsvarar varje inmatnings bild.

```python
import pandas as pd
from azureml.pipeline.core import Pipeline

# Run the pipeline
pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(
    pipeline, pipeline_params={"param_batch_size": 20})

# Wait for the run to finish (this might take several minutes)
pipeline_run.wait_for_completion(show_output=True)

# Download and review the output
step_run = list(pipeline_run.get_children())[0]
step_run.download_file("./outputs/result-labels.txt")

df = pd.read_csv("result-labels.txt", delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
df.head()
```

## <a name="publish-the-pipeline"></a>Publicera pipelinen

När du är nöjd med resultatet av körningen kan du publicera pipelinen så att du kan köra den med olika ingångs värden senare. När du publicerar en pipeline får du en REST-slutpunkt. Den här slut punkten accepterar anrop av pipelinen med den uppsättning parametrar som du redan har inkluderat med hjälp av [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py).

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring",
    description="Batch scoring using Inception v3 model",
    version="1.0")
```

## <a name="rerun-the-pipeline-by-using-the-rest-endpoint"></a>Kör pipelinen igen med hjälp av REST-slutpunkten

Om du vill köra pipelinen igen behöver du en Azure Active Directory Authentication Head-token, enligt beskrivningen i [AzureCliAuthentication-klassen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py).

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.pipeline.core import PublishedPipeline

rest_endpoint = published_pipeline.endpoint
# specify batch size when running the pipeline
response = requests.post(rest_endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})

# Monitor the run
published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)

RunDetails(published_pipeline_run).show()
```

## <a name="next-steps"></a>Nästa steg

Om du vill se den här arbets delen från slut punkt till slut punkt kan du testa den bärbara datorn för batch-Poäng i [GitHub](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)eller gå till [Azure Architecture Center](/azure/architecture/reference-architectures/ai/batch-scoring-python) och se en exempel lösnings arkitektur.
