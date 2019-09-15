---
title: 'Självstudier: Azure ML-pipelines för batch-Poäng'
titleSuffix: Azure Machine Learning service
description: Bygg en ML-pipeline för att köra batch-Poäng i en bild klassificerings modell. Maskin inlärnings pipeliner optimerar arbets flödet med hastighet, portabilitet och åter användning så att du kan fokusera på din expertis, maskin inlärning i stället för infrastruktur och automatisering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 09/05/2019
ms.openlocfilehash: 039dbc435cbffed9fe2a9e9be0053d5eb663e9bb
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997357"
---
# <a name="use-azure-machine-learning-pipelines-for-batch-scoring"></a>Använd Azure Machine Learning pipelines för batch-Poäng

I den här självstudien använder du Azure Machine Learning tjänst pipelines för att köra en batch-poäng eller en härledning, jobb. I det här exempel jobbet används den förtränade InDesign [-v3-](https://arxiv.org/abs/1512.00567) (convolutional neurala Network Tensorflow Model för att klassificera bilder som inte har etiketter. När du har skapat och publicerat en pipeline konfigurerar du en REST-slutpunkt så att du kan utlösa pipelinen från alla HTTP-bibliotek på vilken plattform som helst.

Maskin inlärnings pipeliner optimerar arbets flödet med hastighet, portabilitet och åter användning så att du kan fokusera på din expertis, maskin inlärning i stället för infrastruktur och automatisering. [Läs mer om ml-pipelines](concept-ml-pipelines.md).

I den här självstudien får du lära dig följande uppgifter:

> [!div class="checklist"]
> * Konfigurera arbets ytan och hämta exempel data
> * Skapa data objekt för att hämta och mata ut data
> * Ladda ned, Förbered och registrera modellen på din arbets yta
> * Etablera beräknings mål och skapa ett bedömnings skript
> * Skapa, köra och publicera en pipeline
> * Aktivera en REST-slutpunkt för pipelinen

Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

## <a name="prerequisites"></a>Förutsättningar

* Slutför [del 1 av installations självstudien](tutorial-1st-experiment-sdk-setup.md) om du inte redan har en Azure Machine Learning tjänst arbets yta eller en virtuell dator.
* När du har slutfört installations guiden öppnar du **självstudierna/tutorial-pipeline-batch-scoring-Classification. ipynb** Notebook med samma Notebook-Server.

Den här själv studie kursen finns också på [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) om du vill köra den i din egen [lokala miljö](how-to-configure-environment.md#local). Kör `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-pipeline-steps pandas requests` för att hämta de nödvändiga paketen.

## <a name="configure-workspace-and-create-datastore"></a>Konfigurera arbets yta och skapa data lager

Skapa ett objekt för arbets ytan från den befintliga Azure Machine Learning-arbetsytan. 
+ En [arbets yta](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) är en klass som godkänner din Azure-prenumeration och resursinformation. Den skapar också en molnresurs för att övervaka och spåra dina körningar i modellen. 

+ `Workspace.from_config()`läser filen **config. JSON** och läser in autentiseringsinformationen till ett objekt med namnet `ws`. `ws` används i resten av koden i den här självstudien.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

### <a name="create-a-datastore-for-sample-images"></a>Skapa ett data lager för exempel bilder

Hämta exemplet på offentliga data för utvärdering av ImageNet från den offentliga `sampledata` BLOB-behållaren `pipelinedata`på kontot. Anrop `register_azure_blob_container()` gör data tillgängliga för arbets ytan under namnet `images_datastore`. Ange sedan standard data lagret för arbets ytan som utdata-datalager, som du använder för resultat av utdata i pipelinen.

```python
from azureml.core.datastore import Datastore

batchscore_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="images_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata", 
                      overwrite=True)

def_data_store = ws.get_default_datastore()
```

## <a name="create-data-objects"></a>Skapa data objekt

När du `DataReference` skapar pipeliner används objekt för att läsa data från data lager för arbets ytor `PipelineData` och objekt används för att överföra mellanliggande data mellan pipeline-steg.

> [!Important]
> Det här batch-bedömnings exemplet använder bara ett pipeline-steg, men i användnings fall med flera steg innehåller det typiska flödet:
>
> 1. Att `DataReference` använda objekt som **indata** för att hämta rå data, utföra några transformeringar och sedan placera `PipelineData` ett objekt.
>
> 2. Använd föregående stegs `PipelineData` **utdatafil** som ett *indata-objekt*, upprepas för efterföljande steg.

I det här scenariot `DataReference` skapar du objekt som motsvarar data lager katalogerna för både indata och klassificerings etiketter (y-test-värden). Du skapar också ett `PipelineData` objekt för utdata från batch-poängen.

```python
from azureml.data.data_reference import DataReference
from azureml.pipeline.core import PipelineData

input_images = DataReference(datastore=batchscore_blob, 
                             data_reference_name="input_images",
                             path_on_datastore="batchscoring/images",
                             mode="download"
                            )

label_dir = DataReference(datastore=batchscore_blob, 
                          data_reference_name="input_labels",
                          path_on_datastore="batchscoring/labels",
                          mode="download"                          
                         )

output_dir = PipelineData(name="scores", 
                          datastore=def_data_store, 
                          output_path_on_compute="batchscoring/results")
```

## <a name="download-and-register-the-model"></a>Hämta och registrera modellen

Hämta den förtränade Tensorflow-modellen och Använd den för batch-Poäng i pipelinen. Skapa först en lokal katalog där du lagrar modellen och ladda ned och extrahera den.

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

Nu registrerar du modellen på din arbets yta, vilket gör att du enkelt kan hämta den i pipeline-processen. I den `register()` statiska funktionen `model_name` är parametern den nyckel som du använder för att hitta din modell i SDK: n.

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-remote-compute-target"></a>Skapa och koppla fjärrberäknings mål

Eftersom ML-pipelines inte kan köras lokalt måste du köra dem på moln resurser. Vi refererar till detta som fjärrberäknings mål, som är återanvändbara virtuella beräknings miljöer där du kör experiment och ML-arbetsflöden. Kör följande kod för att skapa ett GPU-aktiverat [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) mål och koppla det till din arbets yta. I den [konceptuella artikeln](https://docs.microsoft.com/azure/machine-learning/service/concept-compute-target) finns mer information om Compute-mål.


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

## <a name="write-a-scoring-script"></a>Skriv ett bedömnings skript

För att utföra poängen skapar du ett kommando bedömnings skript `batch_scoring.py`och skriver det till den aktuella katalogen. Skriptet använder inmatnings bilder, använder klassificerings modellen och matar ut förutsägelserna till en resultat fil.

Skriptet `batch_scoring.py` använder följande parametrar, som skickas från pipeline-steget som du skapar senare i den här självstudien:

- `--model_name`: namnet på den modell som används
- `--label_dir`: katalogen som innehåller `labels.txt` filen 
- `--dataset_path`: katalogen som innehåller de inmatade bilderna
- `--output_dir`: skriptet kör modellen för data och utdata `results-label.txt` till den här katalogen
- `--batch_size`: batchstorleken som används för att köra modellen

Pipelines-infrastrukturen använder `ArgumentParser` klassen för att skicka parametrar till pipeline-steg. I koden nedan tilldelas till exempel egenskaps-ID `--model_name` `model_name`: t för det första argumentet. I funktionen öppnas den här egenskapen med hjälp av `Model.get_model_path(args.model_name)`. `main()`


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
from azureml.core.model import Model

slim = tf.contrib.slim

parser = argparse.ArgumentParser(description="Start a tensorflow model serving")
parser.add_argument('--model_name', dest="model_name", required=True)
parser.add_argument('--label_dir', dest="label_dir", required=True)
parser.add_argument('--dataset_path', dest="dataset_path", required=True)
parser.add_argument('--output_dir', dest="output_dir", required=True)
parser.add_argument('--batch_size', dest="batch_size", type=int, required=True)

args = parser.parse_args()

image_size = 299
num_channel = 3

# create output directory if it does not exist
os.makedirs(args.output_dir, exist_ok=True)


def get_class_label_dict(label_file):
    label = []
    proto_as_ascii_lines = tf.gfile.GFile(label_file).readlines()
    for l in proto_as_ascii_lines:
        label.append(l.rstrip())
    return label


class DataIterator:
    def __init__(self, data_dir):
        self.file_paths = []
        image_list = os.listdir(data_dir)
        self.file_paths = [data_dir + '/' + file_name.rstrip() for file_name in image_list]
        self.labels = [1 for file_name in self.file_paths]

    @property
    def size(self):
        return len(self.labels)

    def input_pipeline(self, batch_size):
        images_tensor = tf.convert_to_tensor(self.file_paths, dtype=tf.string)
        labels_tensor = tf.convert_to_tensor(self.labels, dtype=tf.int64)
        input_queue = tf.train.slice_input_producer([images_tensor, labels_tensor], shuffle=False)
        labels = input_queue[1]
        images_content = tf.read_file(input_queue[0])

        image_reader = tf.image.decode_jpeg(images_content, channels=num_channel, name="jpeg_reader")
        float_caster = tf.cast(image_reader, tf.float32)
        new_size = tf.constant([image_size, image_size], dtype=tf.int32)
        images = tf.image.resize_images(float_caster, new_size)
        images = tf.divide(tf.subtract(images, [0]), [255])

        image_batch, label_batch = tf.train.batch([images, labels], batch_size=batch_size, capacity=5 * batch_size)
        return image_batch


def main(_):
    label_file_name = os.path.join(args.label_dir, "labels.txt")
    label_dict = get_class_label_dict(label_file_name)
    classes_num = len(label_dict)
    test_feeder = DataIterator(data_dir=args.dataset_path)
    total_size = len(test_feeder.labels)
    count = 0

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
        with open(out_filename, "w") as result_file:
            i = 0
            while count < total_size and not coord.should_stop():
                test_images_batch = sess.run(test_images)
                file_names_batch = test_feeder.file_paths[i * args.batch_size:
                                                          min(test_feeder.size, (i + 1) * args.batch_size)]
                results = sess.run(probabilities, feed_dict={input_images: test_images_batch})
                new_add = min(args.batch_size, total_size - count)
                count += new_add
                i += 1
                for j in range(new_add):
                    result_file.write(os.path.basename(file_names_batch[j]) + ": " + label_dict[results[j]] + "\n")
                result_file.flush()
            coord.request_stop()
            coord.join(threads)

        shutil.copy(out_filename, "./outputs/")

if __name__ == "__main__":
    tf.app.run()

```

> [!TIP]
> Pipelinen i den här självstudien har bara ett steg och skriver utdata till en fil, men för pipeliner i flera steg använder `ArgumentParser` du också för att definiera en katalog för att skriva utdata som indata för efterföljande steg. Se [antecknings boken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) för ett exempel på hur du `ArgumentParser` skickar data mellan flera pipeline-steg med design mönstret.

## <a name="build-and-run-the-pipeline"></a>Skapa och kör pipelinen

Innan du kör pipelinen skapar du ett-objekt som definierar den python-miljö och de beroenden som `batch_scoring.py`krävs av ditt skript. Det nödvändiga huvud beroendet är Tensorflow, men du kan även `azureml-defaults` installera för bakgrunds processer från SDK: n. Skapa ett `RunConfiguration` objekt med hjälp av beroenden och ange Docker och Docker-GPU-stöd.

```python
from azureml.core.runconfig import DEFAULT_GPU_IMAGE
from azureml.core.runconfig import CondaDependencies, RunConfiguration

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.13.1", "azureml-defaults"])

amlcompute_run_config = RunConfiguration(conda_dependencies=cd)
amlcompute_run_config.environment.docker.enabled = True
amlcompute_run_config.environment.docker.base_image = DEFAULT_GPU_IMAGE
amlcompute_run_config.environment.spark.precache_packages = False
```

### <a name="parameterize-the-pipeline"></a>Parameterisera pipelinen

Definiera en anpassad parameter för pipelinen för att styra batchstorleken. När pipelinen har publicerats och exponerats via en REST-slutpunkt exponeras även alla konfigurerade parametrar och kan anges i JSON-nyttolasten när pipelinen körs igen med en HTTP-begäran.

Skapa ett `PipelineParameter` objekt för att aktivera det här beteendet och definiera ett namn och standardvärde.

```python
from azureml.pipeline.core.graph import PipelineParameter
batch_size_param = PipelineParameter(name="param_batch_size", default_value=20)
```

### <a name="create-the-pipeline-step"></a>Skapa pipeline-steg

Ett pipeline-steg är ett objekt som kapslar in allt du behöver för att köra en pipeline, inklusive:

* miljö-och beroende inställningar
* beräknings resursen som ska köra pipelinen på
* indata och utdata och eventuella anpassade parametrar
* referens till ett skript eller SDK-logik som ska köras under steget

Det finns flera klasser som ärver från den överordnade klassen [`PipelineStep`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) för att hjälpa till med att skapa ett steg med vissa ramverk och stackar. I det här exemplet använder [`PythonScriptStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) du klassen för att definiera din steg logik med ett anpassat Python-skript. Observera att om ett argument i skriptet antingen är indata till steget eller utdata i steget, måste det definieras **både** i `arguments` matrisen, `input` `output` **samt i respektive** -parameter. 

En objekt referens i `outputs` matrisen blir tillgänglig som **inmatad** för ett efterföljande pipeline-steg för scenarier där det finns mer än ett steg.

```python
from azureml.pipeline.steps import PythonScriptStep

batch_score_step = PythonScriptStep(
    name="batch_scoring",
    script_name="batch_scoring.py",
    arguments=["--dataset_path", input_images, 
               "--model_name", "inception",
               "--label_dir", label_dir, 
               "--output_dir", output_dir, 
               "--batch_size", batch_size_param],
    compute_target=compute_target,
    inputs=[input_images, label_dir],
    outputs=[output_dir],
    runconfig=amlcompute_run_config
)
```

En lista över alla klasser för olika steg typer finns i [steg paketet](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py).

### <a name="run-the-pipeline"></a>Köra en pipeline

Nu kör du pipelinen. Skapa först ett `Pipeline` objekt med din arbets yta referens och det pipeline-steg som du skapade. `steps` Parametern är en matris med steg och i det här fallet finns det bara ett steg för batch-poäng. Om du vill bygga pipeliner med flera steg placerar du stegen i ordning i den här matrisen.

Använd `Experiment.submit()` sedan funktionen för att skicka pipelinen för körning. Du anger också den anpassade parametern `param_batch_size`. `wait_for_completion` Funktionen genererar loggar under pipeline-Bygg processen, vilket gör att du kan se aktuell status.

> [!IMPORTANT]
> Den första pipeline-körningen tar ungefär **15 minuter**, eftersom alla beroenden måste hämtas, en Docker-avbildning skapas och python-miljön skapas/skapas. Att köra den igen tar avsevärt längre tid eftersom resurserna återanvänds. Den totala körnings tiden beror dock på arbets belastningen för dina skript och processer som körs i varje pipeline-steg.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline, pipeline_parameters={"param_batch_size": 20})
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>Hämta och granska utdata

Kör följande kod för att ladda ned utdatafilen som skapats `batch_scoring.py` från skriptet och utforska resultatet.

```python
import pandas as pd

step_run = list(pipeline_run.get_children())[0]
step_run.download_file("./outputs/result-labels.txt")

df = pd.read_csv("result-labels.txt", delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
df.head(10)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Filnamn</th>
      <th>förutsägelse</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>ILSVRC2012_val_00000102. JPEG</td>
      <td>Rhodesian ridgeback</td>
    </tr>
    <tr>
      <td>1</td>
      <td>ILSVRC2012_val_00000103. JPEG</td>
      <td>tripod</td>
    </tr>
    <tr>
      <td>2</td>
      <td>ILSVRC2012_val_00000104. JPEG</td>
      <td>Skriv bords tangent bord</td>
    </tr>
    <tr>
      <td>3</td>
      <td>ILSVRC2012_val_00000105. JPEG</td>
      <td>silke terrier</td>
    </tr>
    <tr>
      <td>4</td>
      <td>ILSVRC2012_val_00000106. JPEG</td>
      <td>Windsor-förbindelse</td>
    </tr>
    <tr>
      <td>5</td>
      <td>ILSVRC2012_val_00000107. JPEG</td>
      <td>harvestman</td>
    </tr>
    <tr>
      <td>6</td>
      <td>ILSVRC2012_val_00000108. JPEG</td>
      <td>violin</td>
    </tr>
    <tr>
      <td>7</td>
      <td>ILSVRC2012_val_00000109. JPEG</td>
      <td>loudspeaker</td>
    </tr>
    <tr>
      <td>8</td>
      <td>ILSVRC2012_val_00000110. JPEG</td>
      <td>apron</td>
    </tr>
    <tr>
      <td>9</td>
      <td>ILSVRC2012_val_00000111. JPEG</td>
      <td>Den amerikanska kräfta</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="publish-and-run-from-rest-endpoint"></a>Publicera och kör från REST-slutpunkt

Kör följande kod för att publicera pipelinen till din arbets yta. I din arbets yta i portalen kan du se metadata för pipelinen, inklusive körnings historik och varaktighet. Du kan också köra pipelinen manuellt från portalen.

Dessutom ger publicering av pipelinen en REST-slutpunkt för att köra pipelinen igen från alla HTTP-bibliotek på vilken plattform som helst.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

Om du vill köra pipelinen från REST-slutpunkten behöver du först ett OAuth2 Bearer-huvud. I det här exemplet används interaktiv autentisering i illustrations syfte, men för de flesta produktions scenarier som kräver automatisk eller automatisk autentisering, använder du autentisering för tjänst princip enligt [beskrivningen i den här antecknings boken](https://aka.ms/pl-restep-auth).

Autentisering med tjänst princip innebär att du skapar en **app-registrering** i **Azure Active Directory**, genererar en klient hemlighet och sedan beviljar rollen som tjänst huvud **administratör åtkomst** till din Machine Learning-arbetsyta. Sedan använder [`ServicePrincipalAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) du klassen för att hantera ditt auth-flöde. 

Både `InteractiveLoginAuthentication` och `ServicePrincipalAuthentication` ärver från `AbstractAuthentication` ,ochibådafallenanvänderdufunktionenpåsammasättföratt`get_authentication_header()` Hämta sidhuvudet.

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

Hämta rest-URL: en `endpoint` från egenskapen för det publicerade pipeline-objektet. Du kan också hitta REST-URL: en i din arbets yta i portalen. Bygg en HTTP POST-begäran till slut punkten och ange ditt Authentication-huvud. Lägg också till ett JSON-nyttolast-objekt med experiment namnet och batch-storleks parametern. Som en påminnelse `param_batch_size` skickas skriptet till ditt `batch_scoring.py` skript eftersom du har definierat det som ett `PipelineParameter` objekt i steg konfigurationen.

Gör begäran för att utlösa körningen. Hämta värdet för körnings-ID genom att komma åt nyckelnfrånsvarsordlistan.`Id`

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})
run_id = response.json()["Id"]
```

Använd körnings-ID: t för att övervaka statusen för den nya körningen. Detta tar en till 10-15 min att köra och ser ut ungefär som föregående pipeline-körning, så om du inte behöver se en annan pipeline-körning kan du hoppa över hela utdata.

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>Rensa resurser

Slutför inte det här avsnittet om du planerar att köra andra Azure Machine Learning service-självstudier.

### <a name="stop-the-notebook-vm"></a>Stoppa den virtuella Notebook-datorn

Om du har använt en molnbaserad Notebook-Server stoppar du den virtuella datorn när du inte använder den för att minska kostnaderna.

1. I arbets ytan väljer du **Notebook VM**: ar.
1. Välj den virtuella datorn i listan.
1. Välj **stoppa**.
1. När du är redo att använda servern igen väljer du **Starta**.

### <a name="delete-everything"></a>Ta bort allt

Om du inte planerar att använda de resurser som du har skapat tar du bort dem, så du debiteras inte några avgifter.

1. I Azure-portalen väljer du **Resursgrupper** längst till vänster.
1. Välj den resursgrupp i listan som du har skapat.
1. Välj **Ta bort resursgrupp**.
1. Ange resursgruppsnamnet. Välj sedan **Ta bort**.

Du kan också behålla resursgruppen men ta bort en enstaka arbetsyta. Visa arbetsytans egenskaper och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien om maskin inlärning har du följande uppgifter:

> [!div class="checklist"]
> * Skapa en pipeline med miljö beroenden som ska köras på en fjärran sluten GPU Compute-resurs
> * Skapade ett bedömnings skript för att köra batch-förutsägelser med en förtränad Tensorflow-modell
> * Publicerade en pipeline och har aktiverat den för att köras från en REST-slutpunkt

I [Notebook-lagringsplatsen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines) finns fler exempel på hur du skapar pipeliner med Machine Learning SDK.
