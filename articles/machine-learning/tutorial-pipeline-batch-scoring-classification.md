---
title: 'Självstudiekurs: ML-pipelines för batchbedömning'
titleSuffix: Azure Machine Learning
description: I den här självstudien skapar du en pipeline för maskininlärning för att utföra batchbedömning på en bildklassificeringsmodell. Azure Machine Learning kan du fokusera på maskininlärning i stället för infrastruktur och automatisering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: laobri
ms.date: 03/11/2020
ms.openlocfilehash: 1ccd7a7f33c6ee5cab8b7173d8eb93365b6cb587
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79472228"
---
# <a name="tutorial-build-an-azure-machine-learning-pipeline-for-batch-scoring"></a>Självstudiekurs: Skapa en Azure Machine Learning-pipeline för batchbedömning

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Lär dig hur du skapar en pipeline i Azure Machine Learning för att köra ett batchbedömningsjobb. Machine learning-pipelines optimerar arbetsflödet med snabbhet, portabilitet och återanvändning, så att du kan fokusera på maskininlärning i stället för infrastruktur och automatisering. När du har byggt och publicerat en pipeline konfigurerar du en REST-slutpunkt som du kan använda för att utlösa pipelinen från ett HTTP-bibliotek på valfri plattform. 

I exemplet används en förtränad [inception-V3](https://arxiv.org/abs/1512.00567) faltning neural nätverksmodell implementerad i Tensorflow för att klassificera omärkta bilder. [Läs mer om pipelines för maskininlärning](concept-ml-pipelines.md).

I den här självstudien slutför du följande uppgifter:

> [!div class="checklist"]
> * Konfigurera arbetsyta 
> * Ladda ned och lagra exempeldata
> * Skapa datauppsättningsobjekt för att hämta och mata ut data
> * Ladda ner, förbereda och registrera modellen på arbetsytan
> * Etablera beräkningsmål och skapa ett bedömningsskript
> * Använd `ParallelRunStep` klassen för bedömning av asynkrona batch
> * Skapa, köra och publicera en pipeline
> * Aktivera en REST-slutpunkt för pipelinen

Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

## <a name="prerequisites"></a>Krav

* Om du inte redan har en azure machine learning-arbetsyta eller virtuell anteckningsbok fyller du [i del 1 av installationshandledningen](tutorial-1st-experiment-sdk-setup.md).
* När du är klar med installationshandledningen använder du samma bärbara datorserver för att öppna den *dator som använder självstudier/maskininlärningspipellines-advanced/tutorial-pipeline-batch-scoring-classification.ipynb.*

Om du vill köra installationshandledningen i din egen [lokala miljö](how-to-configure-environment.md#local)kan du komma åt självstudien på [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials). Kör `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-contrib-pipeline-steps pandas requests` för att hämta de nödvändiga paketen.

## <a name="configure-workspace-and-create-a-datastore"></a>Konfigurera arbetsyta och skapa ett datalager

Skapa ett arbetsyteobjekt från den befintliga Azure Machine Learning-arbetsytan.

- En [arbetsyta](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) är en klass som accepterar din Azure-prenumeration och resursinformation. Arbetsytan skapar också en molnresurs som du kan använda för att övervaka och spåra dina modellkörningar. 
- `Workspace.from_config()`läser `config.json` filen och läser sedan in autentiseringsinformationen i ett objekt med namnet `ws`. Objektet `ws` används i koden i hela den här självstudien.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

## <a name="create-a-datastore-for-sample-images"></a>Skapa ett datalager för exempelbilder

På `pipelinedata` kontot får du exemplet med offentliga `sampledata` data för ImageNet-utvärdering från den offentliga blob-behållaren. Anrop `register_azure_blob_container()` för att göra data tillgängliga `images_datastore`för arbetsytan under namnet . Ange sedan standarddatalagret för arbetsytan som utdatalager. Använd utdatalagret för att få utdata i pipelinen.

```python
from azureml.core.datastore import Datastore

batchscore_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="images_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata", 
                      overwrite=True)

def_data_store = ws.get_default_datastore()
```

## <a name="create-dataset-objects"></a>Skapa datauppsättningsobjekt

När du skapar `Dataset` pipelines används objekt för att läsa `PipelineData` data från arbetsytedatalager och objekt används för att överföra mellanliggande data mellan pipeline-steg.

> [!Important]
> Exempel på batchbedömning i den här självstudien använder bara ett pipeline-steg. I användningsfall som har flera steg innehåller det typiska flödet följande steg:
>
> 1. Använd `Dataset` objekt som *indata* för att hämta rådata, `PipelineData` utföra en viss omformning och sedan mata *ut* ett objekt.
>
> 2. `PipelineData` Använd *utdataobjektet* i föregående steg som *indataobjekt*. Upprepa det för efterföljande steg.

I det här `Dataset` fallet skapar du objekt som motsvarar datalagerkatalogerna för både indatabilderna och klassificeringsetiketterna (y-testvärden). Du kan `PipelineData` också skapa ett objekt för batchbedömningsutdata.

```python
from azureml.core.dataset import Dataset
from azureml.pipeline.core import PipelineData

input_images = Dataset.File.from_files((batchscore_blob, "batchscoring/images/"))
label_ds = Dataset.File.from_files((batchscore_blob, "batchscoring/labels/*.txt"))
output_dir = PipelineData(name="scores", 
                          datastore=def_data_store, 
                          output_path_on_compute="batchscoring/results")
```

Registrera sedan datauppsättningarna till arbetsytan.

```python

input_images = input_images.register(workspace = ws, name = "input_images")
label_ds = label_ds.register(workspace = ws, name = "label_ds")
```

## <a name="download-and-register-the-model"></a>Ladda ner och registrera modellen

Hämta den förtränade Tensorflow-modellen för att använda den för batchbedömning i en pipeline. Skapa först en lokal katalog där du lagrar modellen. Ladda sedan ner och extrahera modellen.

```python
import os
import tarfile
import urllib.request

if not os.path.isdir("models"):
    os.mkdir("models")
    
response = urllib.request.urlretrieve("http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz", "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall("models")
```

Registrera sedan modellen på din arbetsyta, så att du enkelt kan hämta modellen i pipeline-processen. I `register()` den statiska `model_name` funktionen är parametern nyckeln du använder för att hitta din modell i hela SDK.

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-the-remote-compute-target"></a>Skapa och koppla fjärrberäkningsmålet

Machine learning-pipelines kan inte köras lokalt, så du kör dem på molnresurser eller *fjärrberäkningsmål*. Ett fjärrberäkningsmål är en återanvändbar virtuell beräkningsmiljö där du kör experiment och maskininlärningsarbetsflöden. 

Kör följande kod för att skapa [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) ett GPU-aktiverat mål och bifoga den sedan till arbetsytan. Mer information om beräkningsmål finns i den [begreppsmässiga artikeln](https://docs.microsoft.com/azure/machine-learning/concept-compute-target).


```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.exceptions import ComputeTargetException
compute_name = "gpu-cluster"

# checks to see if compute target already exists in workspace, else create it
try:
    compute_target = ComputeTarget(workspace=ws, name=compute_name)
except ComputeTargetException:
    config = AmlCompute.provisioning_configuration(vm_size="STANDARD_NC6",
                                                   vm_priority="lowpriority", 
                                                   min_nodes=0, 
                                                   max_nodes=1)

    compute_target = ComputeTarget.create(workspace=ws, name=compute_name, provisioning_configuration=config)
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

## <a name="write-a-scoring-script"></a>Skriva ett bedömningsskript

Om du vill göra bedömning, `batch_scoring.py`skapa en batch bedömning skript som heter och sedan skriva den till den aktuella katalogen. Skriptet tar indataavbildningar, tillämpar klassificeringsmodellen och matar sedan ut förutsägelserna till en resultatfil.

Skriptet `batch_scoring.py` tar följande parametrar, som `ParallelRunStep` skickas från du skapar senare:

- `--model_name`: Namnet på den modell som används.
- `--labels_name`: Namnet på `Dataset` filen `labels.txt` som innehåller den.

Pipeline-infrastrukturen `ArgumentParser` använder klassen för att skicka parametrar till pipeline-steg. I följande kod får det första `--model_name` argumentet egenskapsidentifieraren `model_name`. I `init()` funktionen `Model.get_model_path(args.model_name)` används för att komma åt den här egenskapen.


```python
%%writefile batch_scoring.py

import os
import argparse
import datetime
import time
import tensorflow as tf
from math import ceil
import numpy as np
import shutil
from tensorflow.contrib.slim.python.slim.nets import inception_v3

from azureml.core import Run
from azureml.core.model import Model
from azureml.core.dataset import Dataset

slim = tf.contrib.slim

image_size = 299
num_channel = 3


def get_class_label_dict():
    label = []
    proto_as_ascii_lines = tf.gfile.GFile("labels.txt").readlines()
    for l in proto_as_ascii_lines:
        label.append(l.rstrip())
    return label


def init():
    global g_tf_sess, probabilities, label_dict, input_images

    parser = argparse.ArgumentParser(description="Start a tensorflow model serving")
    parser.add_argument('--model_name', dest="model_name", required=True)
    parser.add_argument('--labels_name', dest="labels_name", required=True)
    args, _ = parser.parse_known_args()

    workspace = Run.get_context(allow_offline=False).experiment.workspace
    label_ds = Dataset.get_by_name(workspace=workspace, name=args.labels_name)
    label_ds.download(target_path='.', overwrite=True)

    label_dict = get_class_label_dict()
    classes_num = len(label_dict)

    with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
        input_images = tf.placeholder(tf.float32, [1, image_size, image_size, num_channel])
        logits, _ = inception_v3.inception_v3(input_images,
                                              num_classes=classes_num,
                                              is_training=False)
        probabilities = tf.argmax(logits, 1)

    config = tf.ConfigProto()
    config.gpu_options.allow_growth = True
    g_tf_sess = tf.Session(config=config)
    g_tf_sess.run(tf.global_variables_initializer())
    g_tf_sess.run(tf.local_variables_initializer())

    model_path = Model.get_model_path(args.model_name)
    saver = tf.train.Saver()
    saver.restore(g_tf_sess, model_path)


def file_to_tensor(file_path):
    image_string = tf.read_file(file_path)
    image = tf.image.decode_image(image_string, channels=3)

    image.set_shape([None, None, None])
    image = tf.image.resize_images(image, [image_size, image_size])
    image = tf.divide(tf.subtract(image, [0]), [255])
    image.set_shape([image_size, image_size, num_channel])
    return image


def run(mini_batch):
    result_list = []
    for file_path in mini_batch:
        test_image = file_to_tensor(file_path)
        out = g_tf_sess.run(test_image)
        result = g_tf_sess.run(probabilities, feed_dict={input_images: [out]})
        result_list.append(os.path.basename(file_path) + ": " + label_dict[result[0]])
    return result_list
```

> [!TIP]
> Pipelinen i den här självstudien har bara ett steg och skriver utdata till en fil. För pipeline-pipelines i flera `ArgumentParser` steg använder du också för att definiera en katalog för att skriva utdata för indata till efterföljande steg. Ett exempel på att överföra data mellan `ArgumentParser` flera pipeline-steg med hjälp av designmönstret finns i [anteckningsboken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb).

## <a name="build-the-pipeline"></a>Bygg pipelinen

Innan du kör pipelinen skapar du ett objekt som definierar Python-miljön och skapar de beroenden som `batch_scoring.py` skriptet kräver. Det huvudsakliga beroendet som krävs är `azureml-defaults` Tensorflow, men du installerar också för bakgrundsprocesser. Skapa `RunConfiguration` ett objekt med hjälp av beroenden. Ange också stöd för Docker och Docker-GPU.

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.13.1", "azureml-defaults"])
env = Environment(name="parallelenv")
env.python.conda_dependencies = cd
env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="create-the-configuration-to-wrap-the-script"></a>Skapa konfigurationen för att radbrytas skriptet

Skapa pipeline-steget med skript, miljökonfiguration och parametrar. Ange det beräkningsmål som du redan är kopplad till arbetsytan.

```python
from azureml.contrib.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    environment=env,
    entry_script="batch_scoring.py",
    source_directory=".",
    output_action="append_row",
    mini_batch_size="20",
    error_threshold=1,
    compute_target=compute_target,
    process_count_per_node=2,
    node_count=1
)
```

### <a name="create-the-pipeline-step"></a>Skapa pipeline-steget

Ett pipeline-steg är ett objekt som kapslar in allt du behöver för att köra en pipeline, inklusive:

* Inställningar för miljö och beroende
* Beräkningsresursen som ska köras på
* In- och utdata och eventuella anpassade parametrar
* Referens till ett skript eller en SDK-logik som ska köras under steget

Flera klasser ärver [`PipelineStep`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py)från den överordnade klassen . Du kan välja klasser för att använda specifika ramverk eller stackar för att skapa ett steg. I det här exemplet `ParallelRunStep` använder du klassen för att definiera din steglogik med hjälp av ett anpassat Python-skript. Om ett argument till skriptet antingen är en indata till steget eller en `arguments` utdata för steget, måste argumentet definieras *både* i matrisen *respektive* i `input` `output` parametern eller parametern. 

I scenarier där det finns mer än ett `outputs` steg blir en objektreferens i matrisen tillgänglig som *indata* för ett efterföljande pipeline-steg.

```python
from azureml.contrib.pipeline.steps import ParallelRunStep

batch_score_step = ParallelRunStep(
    name="parallel-step-test",
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    models=[model],
    arguments=["--model_name", "inception",
               "--labels_name", "label_ds"],
    parallel_run_config=parallel_run_config,
    allow_reuse=False
)
```

En lista över alla klasser som du kan använda för olika stegtyper finns i [stegpaketet](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py).

## <a name="submit-the-pipeline"></a>Skicka pipelinen

Kör pipelinen. Skapa först `Pipeline` ett objekt med hjälp av arbetsytans referens och det pipeline-steg som du skapade. Parametern `steps` är en matris med steg. I det här fallet finns det bara ett steg för batchbedömning. Om du vill skapa pipelines som har flera steg placerar du stegen i ordning i den här matrisen.

Använd sedan `Experiment.submit()` funktionen för att skicka pipelinen för körning. Du kan också `param_batch_size`ange den anpassade parametern . Funktionen `wait_for_completion` matar ut loggar under pipeline-byggprocessen. Du kan använda loggarna för att se aktuella förlopp.

> [!IMPORTANT]
> Den första pipeline-körningen tar ungefär *15 minuter.* Alla beroenden måste hämtas, en Docker-avbildning skapas och Python-miljön etableras och skapas. Köra pipelinen igen tar betydligt mindre tid eftersom dessa resurser återanvänds i stället för skapas. Den totala körtiden för pipelinen beror dock på arbetsbelastningen för skripten och de processer som körs i varje pipeline-steg.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline)
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>Ladda ner och granska utdata

Kör följande kod för att hämta utdatafilen `batch_scoring.py` som har skapats från skriptet. Utforska sedan poängresultaten.

```python
import pandas as pd

batch_run = next(pipeline_run.get_children())
batch_output = batch_run.get_output_data("scores")
batch_output.download(local_path="inception_results")

for root, dirs, files in os.walk("inception_results"):
    for file in files:
        if file.endswith("parallel_run_step.txt"):
            result_file = os.path.join(root, file)

df = pd.read_csv(result_file, delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
print("Prediction has ", df.shape[0], " rows")
df.head(10)
```

## <a name="publish-and-run-from-a-rest-endpoint"></a>Publicera och köra från en REST-slutpunkt

Kör följande kod för att publicera pipelinen till arbetsytan. På arbetsytan i Azure Machine Learning studio kan du se metadata för pipelinen, inklusive körningshistorik och varaktigheter. Du kan också köra pipelinen manuellt från studion.

Genom att publicera pipelinen kan du använda en REST-slutpunkt som du kan använda för att köra pipelinen från ett HTTP-bibliotek på valfri plattform.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

Om du vill köra pipelinen från REST-slutpunkten behöver du ett autentiseringshuvud av OAuth2-bärare. I följande exempel används interaktiv autentisering (för illustrationsändamål), men för de flesta produktionsscenarier som kräver automatisk eller huvudlös autentisering använder du tjänstens huvudautentisering [enligt beskrivningen i den här artikeln](how-to-setup-authentication.md).

Tjänsthuvudnamnsautentisering innebär att skapa en *appregistrering* i *Azure Active Directory*. Först genererar du en klienthemlighet och sedan ger du tjänstens *huvudroll åtkomst* till din maskininlärningsarbetsyta. Använd [`ServicePrincipalAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) klassen för att hantera ditt autentiseringsflöde. 

Både [`InteractiveLoginAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.interactiveloginauthentication?view=azure-ml-py) `ServicePrincipalAuthentication` och `AbstractAuthentication`ärva från . I båda fallen [`get_authentication_header()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.abstractauthentication?view=azure-ml-py#get-authentication-header--) använder du funktionen på samma sätt för att hämta huvudet:

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

Hämta REST-URL:en från egenskapen `endpoint` för det publicerade pipeline-objektet. Du kan också hitta REST-URL:en på arbetsytan i Azure Machine Learning studio. 

Skapa en HTTP POST-begäran till slutpunkten. Ange autentiseringshuvudet i begäran. Lägg till ett JSON-nyttolastobjekt som har experimentnamnet och parametern batchstorlek. Som nämnts tidigare `param_batch_size` i självstudien `batch_scoring.py` skickas vidare till `PipelineParameter` skriptet eftersom du definierade det som ett objekt i stegkonfigurationen.

Gör begäran om att utlösa körningen. Inkludera kod för `Id` att komma åt nyckeln från svarsordlistan för att hämta värdet för körnings-ID: et.

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})
run_id = response.json()["Id"]
```

Använd körnings-ID:et för att övervaka status för den nya körningen. Det nya åket tar ytterligare 10-15 min till. 

Den nya körningen kommer att se ut ungefär som den pipeline du körde tidigare i självstudien. Du kan välja att inte visa hela utdata.

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>Rensa resurser

Fyll inte i det här avsnittet om du planerar att köra andra Azure Machine Learning-självstudier.

### <a name="stop-the-compute-instance"></a>Stoppa beräkningsinstansen

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Ta bort allt

Om du inte planerar att använda de resurser som du skapade, tar du bort dem så att du inte debiteras:

1. Välj **Resursgrupper**i Azure-portalen på den vänstra menyn .
1. Markera resursgruppen som du skapade i listan över resursgrupper.
1. Välj **Ta bort resursgrupp**.
1. Ange resursgruppsnamnet. Välj sedan **Ta bort**.

Du kan också behålla resursgruppen men ta bort en enstaka arbetsyta. Visa egenskaperna för arbetsytan och välj sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här datorinlärningspipelinens självstudiekurs gjorde du följande uppgifter:

> [!div class="checklist"]
> * Byggde en pipeline med miljöberoenden för att köras på en fjärr-GPU-beräkningsresurs.
> * Skapade ett bedömningsskript för att köra batchförutsägelser med hjälp av en förtränad Tensorflow-modell.
> * Publicerade en pipeline och gjorde det möjligt att köra den från en REST-slutpunkt.

Fler exempel på hur du skapar pipelines med hjälp av machine learning SDK finns i [databasen för anteckningsboken](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines).
