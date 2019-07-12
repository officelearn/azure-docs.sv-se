---
title: Visualisera experiment med TensorBoard
titleSuffix: Azure Machine Learning service
description: Starta TensorBoard om du vill visualisera utlösningshistoriker om körningen av experimentet och identifiera potentiella områden för finjustering justera och träna.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: maxluk
ms.author: maxluk
ms.date: 06/28/2019
ms.openlocfilehash: fde2b6d1d298e89227951c376d584452fbff2679
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707056"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>Visualisera experimentkörningar och mått med TensorBoard och Azure Machine Learning

I den här artikeln får du lära dig hur du visar dina experimentkörningar och mått i TensorBoard med [den `tensorboard` paketet](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py) i den huvudsakliga Azure Machine Learning SDK-tjänsten. När du har inspekterat dina experimentkörningar, kan du bättre finjustera och träna om dina maskininlärningsmodeller.

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) är en svit av webbprogram för att granska och förstå dina experiment struktur och prestanda.

Hur du startar TensorBoard med Azure Machine Learning-experiment beror på vilken typ av experiment:
+ Om ditt experiment internt matar ut loggfiler som domänexperter TensorBoard, till exempel PyTorch, Chainer och TensorFlow experiment, kan du [starta TensorBoard direkt](#direct) från experiment's körningshistorik. 

+ Experiment som inte har inbyggt utdatafiler TensorBoard använda, till exempel som Scikit-Läs- eller Azure Machine Learning-experiment, använda [den `export_to_tensorboard()` metoden](#export) att exportera körhistorik som TensorBoard loggar och starta TensorBoard därifrån. 

## <a name="prerequisites"></a>Förutsättningar

* Om du vill starta TensorBoard och visa ditt experiment som körningshistorik måste experimenten du redan har aktiverat loggning för att spåra dess statistik och.  

* Koden i den här anvisningen kan köras i någon av följande miljöer: 

    * Azure Machine Learning Notebook VM - inga nedladdningar eller installeras

        * Slutför den [molnbaserade notebook Snabbstart](quickstart-run-cloud-notebook.md#create-notebook) att skapa en dedikerad anteckningsboksserver förinstallerade med SDK: N och exempellagringsplatsen.

        * Mappen samples på notebook-server innehåller två har slutförts och expanderas anteckningsböcker genom att navigera till den här katalogen: **How-to-till-användning – azureml > utbildning med deep learning**.
        * export-run-history-to-run-history.ipynb
        * tensorboard.ipynb

    * Egna Juptyer notebook-server
      * Använd den [skapa en arbetsyta artikel](setup-create-workspace.md) till
          * [Installera Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) med den `tensorboard` extra
          * Skapa en arbetsyta och dess konfigurationsfil (config.json)
  
<a name="direct"></a>
## <a name="option-1-directly-view-run-history-in-tensorboard"></a>Alternativ 1: Direkt visa körningshistorik i TensorBoard

Det här alternativet fungerar för försök att internt utdata loggfiler konsumeras av TensorBoard, till exempel PyTorch, Chainer och TensorFlow-experimenten. Om detta inte är fallet med ditt experiment, använda [den `export_to_tensorboard()` metoden](#export) i stället.

Följande exempelkod använder den [MNIST demo experiment](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) från Tensorflows lagringsplatsen i en fjärransluten beräkningsmål beräkning av Azure Machine Learning. Nu ska vi träna vår modell med SDK: s anpassade [TensorFlow kostnadsuppskattning](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py), och starta sedan TensorBoard mot den här TensorFlow experiment, det vill säga ett experiment som internt visar TensorBoard händelsefiler.

### <a name="set-experiment-name-and-create-project-folder"></a>Ange namn på experiment och skapa projektmapp, till exempel

Här vi sätter namn experimentet och skapa en mapp. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>Ladda ned TensorFlow demo experiment kod

Tensorflows databasen har en MNIST-demo med omfattande TensorBoard instrumentation. Vi inte och inte heller måste du ändra någon av den här demon kod att fungera med Azure Machine Learning-tjänsten. I följande kod, vi hämta MNIST-kod och spara den i vår nya experiment-mapp.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
I hela kodfilen MNIST mnist_with_summaries.py, Lägg märke till att det finns rader anropet `tf.summary.scalar()`, `tf.summary.histogram()`, `tf.summary.FileWriter()` osv. Dessa metoder gruppen, loggar och tagga viktiga mått för dina experiment i körningshistoriken. Den `tf.summary.FileWriter()` är särskilt viktigt eftersom det Serialiserar data från dina loggade experiment mått som möjliggör TensorBoard att generera visualiseringar från dem.

 ### <a name="configure-experiment"></a>Konfigurera experiment

Nedan kan vi konfigurera våra experiment och konfigurera kataloger för loggar och data. De här loggarna ska överföras till tjänsten artefakt som TensorBoard har åtkomst till senare.

>[!Note]
> I det här exemplet TensorFlow behöver du installera TensorFlow på den lokala datorn. Dessutom måste modulen TensorBoard (det vill säga det som ingår i TensorFlow) vara tillgänglig för den här anteckningsboken kernel som den lokala datorn är vad går att köra TensorBoard.

```Python
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment

ws = Workspace.from_config()

# create directories for experiment logs and dataset
logs_dir = os.path.join(os.curdir, "logs")
data_dir = os.path.abspath(os.path.join(os.curdir, "mnist_data"))

if not path.exists(data_dir):
    makedirs(data_dir)

os.environ["TEST_TMPDIR"] = data_dir

# Writing logs to ./logs results in their being uploaded to Artifact Service,
# and thus, made accessible to our TensorBoard instance.
arguments_list = ["--log_dir", logs_dir]

# Create an experiment
exp = Experiment(ws, experiment_name)
```

### <a name="create-a-cluster-for-your-experiment"></a>Skapa ett kluster för experimentet
Vi skapar ett AmlCompute-kluster för det här experimentet men dina experiment kan skapas i vilken miljö som helst och du kan fortfarande starta TensorBoard mot experimentet körningshistorik. 

```Python
from azureml.core.compute import ComputeTarget, AmlCompute

cluster_name = "cpucluster"

cts = ws.compute_targets
found = False
if cluster_name in cts and cts[cluster_name].type == 'AmlCompute':
   found = True
   print('Found existing compute target.')
   compute_target = cts[cluster_name]
if not found:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

compute_target.wait_for_completion(show_output=True, min_node_count=None)

# use get_status() to get a detailed status for the current cluster. 
# print(compute_target.get_status().serialize())
```

### <a name="submit-run-with-tensorflow-estimator"></a>Skicka kör med TensorFlow kostnadsuppskattning

TensorFlow-kostnadsuppskattning ger ett enkelt sätt att starta ett jobb med TensorFlow-utbildning på beräkningsmål. Den implementeras via ett allmänt [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) klass, som kan användas för att stödja valfritt ramverk. Läs mer om träna modeller med hjälp av generiska kostnadsuppskattning [träna modeller med Azure Machine Learning med hjälp av kostnadsuppskattning](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import TensorFlow
script_params = {"--log_dir": "./logs"}

tf_estimator = TensorFlow(source_directory=exp_dir,
                          compute_target=compute_target,
                          entry_script='mnist_with_summaries.py',
                          script_params=script_params)

run = exp.submit(tf_estimator)
```

### <a name="launch-tensorboard"></a>Starta TensorBoard

Du kan starta TensorBoard under din körning eller när den är klar. I följande, skapar vi en TensorBoard-objektinstans `tb`, att tar experimentet körningshistorik läses in i den `run`, och sedan öppnas TensorBoard med den `start()` metoden. 
  
Den [TensorBoard konstruktorn](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py) tar en matris över körningar, så se till och skicka den i som en enda element-matris.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>Alternativ 2: Exportera historik som loggen för att visa i TensorBoard

Följande kod konfigurerar en exempelexperimentet, börjar loggning med hjälp av Azure Machine Learning körningshistorik API: er och exporterar experimentet historiken för körning i loggar som konsumeras av TensorBoard för visualisering. 

### <a name="set-up-experiment"></a>Konfigurera experiment

Följande kod ställer in ett nytt experiment och namnger den kör katalogen `root_run`. 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

Här vi läsa in den diabetes-datauppsättning – en inbyggd liten datamängd som medföljer scikit-Läs och dela upp den i test- och utbildning uppsättningar.

```Python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
X, y = load_diabetes(return_X_y=True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
x_train, x_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"x":x_train, "y":y_train},        
    "test":{"x":x_test, "y":y_test}
}
```

### <a name="run-experiment-and-log-metrics"></a>Kör experimentet och logga mått

För den här koden vi träna en linjär regressionsmodell och logga nyckelvärden, alpha koefficienten `alpha` och betyder kvadratfel, `mse`, i historiken för körning.

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>Exportera körs ska TensorBoard

Med SDK: er [export_to_tensorboard()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py) metod, vi kan exportera körningshistoriken för våra Azure machine learning-experiment i TensorBoard loggar så att vi kan visa dem via TensorBoard.  

I följande kod, skapar vi mappen `logdir` i våra aktuella arbetskatalog. Den här mappen är där vi kommer att exportera vår experiment körningshistorik och loggar från `root_run` och markera som körs som slutförd. 

```Python
from azureml.tensorboard.export import export_to_tensorboard
import os

logdir = 'exportedTBlogs'
log_path = os.path.join(os.getcwd(), logdir)
try:
    os.stat(log_path)
except os.error:
    os.mkdir(log_path)
print(logdir)

# export run history for the project
export_to_tensorboard(root_run, logdir)

root_run.complete()
```

>[!Note]
 Du kan också exportera en viss körning till TensorBoard genom att ange namnet på Kör  `export_to_tensorboard(run_name, logdir)`

Starta och stoppa TensorBoard när våra körningshistoriken för det här experimentet exporteras, kan vi starta TensorBoard med den [start()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-) metod. 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

När du är klar, se till att anropa den [stop ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--) -metoden i TensorBoard-objektet. I annat fall fortsätter TensorBoard att köras tills du stänger anteckningsboken kernel. 

```python
tb.stop()
```

## <a name="next-steps"></a>Nästa steg

I det här how-to dig, skapas två experiment och lärt dig hur du startar TensorBoard mot deras körhistorik att identifiera områden för potentiella justering och träna. 

* Om du är nöjd med din modell, gå till vår [hur du distribuerar en modell](how-to-deploy-and-where.md) artikeln. 
* Läs mer om [finjustering av hyperparametrar](how-to-tune-hyperparameters.md). 
