---
title: Kör batch förutsägelser på stora mängder data
titleSuffix: Azure Machine Learning service
description: Lär dig mer om att göra förutsägelser för batch asynkront på stora mängder data med hjälp av Azure Machine Learning-tjänsten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens, garye
ms.author: jordane
author: jpe316
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 4c38230c1d5a4dcad9a10b82d810f82515980076
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55245879"
---
# <a name="run-batch-predictions-on-large-data-sets-with-azure-machine-learning-service"></a>Kör batch förutsägelser på stora datauppsättningar med Azure Machine Learning-tjänsten

I den här artikeln lär du dig att göra förutsägelser på stora mängder data asynkront, med hjälp av Azure Machine Learning-tjänsten.

Batch-förutsägelse (eller batchbedömning) ger kostnadseffektiv inferens med oöverträffad dataflöde för asynkron program. Batch förutsägelse pipelines kan skala för att utföra inferens på terabyte av produktionsdata. Batch-förutsägelse är optimerat för stora dataflöden och fire-and-forget förutsägelser för en stor mängd data.

>[!TIP]
> Om datorn kräver låg latens bearbetning (för att bearbeta ett enda dokument eller en liten uppsättning dokument snabbt), använder [i realtid bedömning](how-to-consume-web-service.md) i stället för batch-förutsägelse.

I följande steg ska du skapa en [maskininlärningspipeline](concept-ml-pipelines.md) att registrera en tränats modellen för visuellt innehåll ([Start-V3](https://arxiv.org/abs/1512.00567)). Sedan använder du pretrained modellen för att batch-bedömning på avbildningar som är tillgängliga i Azure Blob storage-kontot. Dessa avbildningar som används för bedömning är utan etikett avbildningar från den [ImageNet](http://image-net.org/) datauppsättning.

## <a name="prerequisites"></a>Förutsättningar

- Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfri eller betald version av Azure Machine Learning-tjänsten](http://aka.ms/AMLFree).

- Konfigurera din utvecklingsmiljö för att installera Azure Machine Learning-SDK. Mer information finns i [konfigurera en utvecklingsmiljö för Azure Machine Learning](how-to-configure-environment.md).

- Skapa en Azure Machine Learning-arbetsyta som ska innehålla alla pipeline-resurser. Du kan använda följande kod eller fler alternativ finns i [skapar en konfigurationsfil för arbetsytan](how-to-configure-environment.md#workspace).

  ```python
  ws = Workspace.create(
     name = '<workspace-name>',
     subscription_id = '<subscription-id>',
     resource_group = '<resource-group>',
     location = '<workspace_region>',
     exist_ok = True)
  ```

## <a name="set-up-machine-learning-resources"></a>Konfigurera machine learning-resurser

Följande steg konfigurerar de resurser du behöver för att köra en pipeline:

- Få åtkomst till databasen som redan har tränats modellen, inkommande etiketter och bilder för att bedöma (detta är redan inställd för dig).
- Konfigurera ett datalager för att lagra dina utdata.
- Konfigurera `DataReference` objekt så att den pekar till data i föregående datalagringen.
- Ställ in beräkning datorer eller kluster där kör pipeline-stegen.

### <a name="access-the-datastores"></a>Komma åt datalagringen

Först få åtkomst till databasen med modellen, etiketter och bilder.

Du använder en offentlig blob-behållare med namnet *sampledata*i den *pipelinedata* kontot som innehåller bilder från uppsättningen ImageNet utvärdering. Datastore-namnet för den här offentliga behållare är *images_datastore*. Registrera den här databasen med din arbetsyta:

```python
# Public blob container details
account_name = "pipelinedata"
datastore_name="images_datastore"
container_name="sampledata"
 
batchscore_blob = Datastore.register_azure_blob_container(ws,
                      datastore_name=datastore_name,
                      container_name= container_name,
                      account_name=account_name,
                      overwrite=True)
```

Konfigurera sedan för att använda standard-datalager för utdata.

När du skapar arbetsytan och [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) och [Blob-lagring](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) är kopplade till arbetsytan som standard. Azure Files är standard-databasen för en arbetsyta, men du kan också använda Blob storage som ett datalager. Mer information finns i [Azure-lagringsalternativen](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-references"></a>Konfigurera datareferenser

Nu kan referera till data i din pipeline som indata till steg för pipeline.

En datakälla i en pipeline representeras av en [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) objekt. Den `DataReference` objekt som pekar på data som finns i eller kan nås från ett datalager. Du behöver `DataReference`  objekt för den katalog som används för indatabilder, den katalog där lagras pretrained modellen, katalogen för etiketter och katalogen.

```python
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

I Azure Machine Learning *compute* (eller *beräkningsmålet*) refererar till datorer eller kluster som utför beräkningssteg i din pipeline för machine learning. Du kan till exempel skapa en `Azure Machine Learning compute`.

```python
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
                     vm_size = vm_size, # NC6 is GPU-enabled
                     vm_priority = 'lowpriority', # optional
                     min_nodes = compute_min_nodes, 
                     max_nodes = compute_max_nodes)

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

Ladda ned tränats modellen för visuellt innehåll (InceptionV3) från <http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz>. Extrahera det till den `models` undermappen.

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url="http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

### <a name="register-the-model"></a>Registrera modellen

Nedan visas hur du registrera modellen:

```python
import shutil
from azureml.core.model import Model

# register downloaded model 
model = Model.register(
        model_path = "models/inception_v3.ckpt",
        model_name = "inception", # This is the name of the registered model
        tags = {'pretrained': "inception"},
        description = "Imagenet trained tensorflow inception",
        workspace = ws)
```

## <a name="write-your-scoring-script"></a>Skriv din bedömningsskriptet

>[!Warning]
>Följande kod är bara ett exempel på vad som finns i den [batch_score.py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/batch_scoring.py) används av den [exempel notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/pipeline-batch-scoring.ipynb). Du måste skapa din egen bedömningsskriptet för ditt scenario.

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

Du har allt du behöver för att skapa pipelinen, så nu lägga ihop.

### <a name="prepare-the-run-environment"></a>Förbereda körningsmiljön

Ange conda-beroenden för skriptet. Du behöver det här objektet senare, när du skapar det pipeline-steget.

```python
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.10.0", "azureml-defaults"])

# Runconfig
amlcompute_run_config = RunConfiguration(conda_dependencies=cd)
amlcompute_run_config.environment.docker.enabled = True
amlcompute_run_config.environment.docker.gpu_support = True
amlcompute_run_config.environment.docker.base_image = DEFAULT_GPU_IMAGE
amlcompute_run_config.environment.spark.precache_packages = False
```

### <a name="specify-the-parameter-for-your-pipeline"></a>Ange parametern för din pipeline

Skapa en pipeline-parameter med hjälp av en [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) objekt med ett standardvärde.

```python
batch_size_param = PipelineParameter(
                    name="param_batch_size", 
                    default_value=20)
```

### <a name="create-the-pipeline-step"></a>Skapa pipeline-steg

Skapa pipeline-steg med hjälp av skript, miljökonfiguration och parametrar. Ange beräkningsmål som du redan har kopplat till din arbetsyta som mål för körningen av skriptet. Använd [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) att skapa pipeline-steget.

```python
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

Nu kör pipelinen och granska utdata som skapats. Utdata har en riskpoäng som motsvarar varje inmatad bild.

```python
# Run the pipeline
pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline, pipeline_params={"param_batch_size": 20})

# Wait for the run to finish (this might take several minutes)
pipeline_run.wait_for_completion(show_output=True)

# Download and review the output
step_run = list(pipeline_run.get_children())[0]
step_run.download_file("./outputs/result-labels.txt")

import pandas as pd
df = pd.read_csv("result-labels.txt", delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
df.head()
```

## <a name="publish-the-pipeline"></a>Publicera pipelinen

När du är nöjd med resultatet av körningen kan du publicera pipelinen så att du kan köra den med olika värden senare. När du publicerar en pipeline kan få du en REST-slutpunkt. Den här slutpunkten accepterar anropar pipeline med uppsättningen parametrar som du redan har införlivat med hjälp av [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py).

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", 
    description="Batch scoring using Inception v3 model", 
    version="1.0")
```

## <a name="rerun-the-pipeline-by-using-the-rest-endpoint"></a>Kör pipelinen igen med hjälp av REST-slutpunkt

Om du vill köra pipelinen du behöver ett Azure Active Directory-huvud autentiseringstoken, enligt beskrivningen i [AzureCliAuthentication klass](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py).

```python
from azureml.pipeline.core import PublishedPipeline

rest_endpoint = published_pipeline.endpoint
# specify batch size when running the pipeline
response = requests.post(rest_endpoint, 
        headers=aad_token, 
        json={"ExperimentName": "batch_scoring",
               "ParameterAssignments": {"param_batch_size": 50}})

# Monitor the run
from azureml.pipeline.core.run import PipelineRun
published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)

RunDetails(published_pipeline_run).show()
```

## <a name="next-steps"></a>Nästa steg

Om du vill se den här fungerande slutpunkt till slutpunkt, försök batchbedömnings-anteckningsboken i [GitHub](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). 

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

