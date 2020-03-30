---
title: Visualisera experiment med TensorBoard
titleSuffix: Azure Machine Learning
description: Starta TensorBoard för att visualisera experimentkörningshistorik och identifiera potentiella områden för hyperparameterjustering och omskolning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: maxluk
ms.author: maxluk
ms.date: 02/27/2020
ms.openlocfilehash: b6b7e47acdbc5bd059e17e512731bd09c8580798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78195387"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>Visualisera experimentkörningar och mått med TensorBoard och Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I [den `tensorboard` ](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py) här artikeln får du lära dig hur du visar experimentkörningar och mått i TensorBoard med hjälp av paketet i huvud-Azure Machine Learning SDK. När du har inspekterat experimentkörningarna kan du bättre ställa in och träna om dina maskininlärningsmodeller.

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) är en uppsättning webbprogram för att inspektera och förstå din experimentstruktur och prestanda.

Hur du startar TensorBoard med Azure Machine Learning-experiment beror på typen av experiment:
+ Om experimentet utdatautdatarar loggfiler som kan förbrukningsbara av TensorBoard, till exempel PyTorch-, Chainer- och TensorFlow-experiment, kan du [starta TensorBoard direkt](#direct) från experimentets körningshistorik. 

+ För experiment som inte utdataredarfiler för TensorBoard, till exempel Scikit-learn eller Azure Machine Learning-experiment, använder du [ `export_to_tensorboard()` metoden](#export) för att exportera körningshistoriken som TensorBoard-loggar och starta TensorBoard därifrån. 

> [!TIP]
> Informationen i det här dokumentet är främst till datavetare och utvecklare som vill övervaka modellutbildningsprocessen. Om du är administratör som är intresserad av att övervaka resursanvändning och händelser från Azure Machine learning, till exempel kvoter, slutförda utbildningskörningar eller slutförda modelldistributioner, läser [du Övervaka Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Krav

* Om du vill starta TensorBoard och visa experimentkörningshistoriken måste experimenten ha aktiverat loggning tidigare för att spåra dess mått och prestanda.  

* Koden i det här dokumentet kan köras i någon av följande miljöer: 

    * Azure Machine Learning-beräkningsinstans – inga hämtningar eller installation nödvändiga

        * Slutför [självstudiekursen: Installationsmiljö och arbetsyta](tutorial-1st-experiment-sdk-setup.md) för att skapa en dedikerad anteckningsboksserver som är förinstallerad med SDK och exempeldatabasen.

        * Leta reda på två slutförda och expanderade anteckningsböcker i exempelmappen på anteckningsboksservern genom att navigera till följande kataloger:
            * **how-to-use-azureml > training-with-deep-learning > export-run-history-to-tensorboard > export-run-history-to-tensorboard.ipynb**

            * **how-to-use-azureml > track-and-monitor-experiments > tensorboard.ipynb**

    * Din egen Juptyer bärbar server
       * [Installera Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) med den `tensorboard` extra
        * [Skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).  
        * [Skapa en konfigurationsfil för arbetsytan](how-to-configure-environment.md#workspace).
  
<a name="direct"></a>

## <a name="option-1-directly-view-run-history-in-tensorboard"></a>Alternativ 1: Direkt visa körhistorik i TensorBoard

Det här alternativet fungerar för experiment som inbyggt matar ut loggfiler som kan förbrukningsvaror av TensorBoard, till exempel PyTorch-, Chainer- och TensorFlow-experiment. Om så inte är fallet med [ `export_to_tensorboard()` experimentet](#export) använder du metoden i stället.

Följande exempelkod använder [MNIST-demoexperimentet](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) från TensorFlows databas i ett fjärrberäkningsmål, Azure Machine Learning Compute. Därefter tränar vi vår modell med SDK:s anpassade [TensorFlow-kalkylator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)och startar sedan TensorBoard mot detta TensorFlow-experiment, det vill än ett experiment som inbyggt matar ut TensorBoard-händelsefiler.

### <a name="set-experiment-name-and-create-project-folder"></a>Ange experimentnamn och skapa projektmapp

Här namnger vi experimentet och skapar dess mapp. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>Ladda ner TensorFlow demoexperimentkod

TensorFlows databas har en MNIST-demo med omfattande TensorBoard-instrumentering. Vi behöver inte, eller behöver, ändra någon av den här demons kod för att den ska fungera med Azure Machine Learning. I följande kod laddar vi ner MNIST-koden och sparar den i vår nyskapade experimentmapp.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
I hela MNIST-kodfilen, mnist_with_summaries.py, märker att `tf.summary.scalar()`det `tf.summary.histogram()` `tf.summary.FileWriter()` finns rader som anropar , etc. Dessa metoder grupperar, loggar och taggar viktiga mått för dina experiment i körningshistorik. Det `tf.summary.FileWriter()` är särskilt viktigt eftersom det serialiserar data från dina loggade experimentmått, vilket gör att TensorBoard kan generera visualiseringar bort av dem.

 ### <a name="configure-experiment"></a>Konfigurera experiment

I det följande konfigurerar vi vårt experiment och konfigurerar kataloger för loggar och data. Dessa loggar kommer att överföras till Artifact Service, som TensorBoard kommer åt senare.

>[!Note]
> För det här TensorFlow-exemplet måste du installera TensorFlow på din lokala dator. Vidare måste TensorBoard-modulen (det vill säga den som ingår i TensorFlow) vara tillgänglig för den här anteckningsbokens kärna, eftersom den lokala datorn är det som kör TensorBoard.

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
Vi skapar ett AmlCompute-kluster för det här experimentet, men dina experiment kan skapas i alla miljöer och du kan fortfarande starta TensorBoard mot experimentkörningshistoriken. 

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

### <a name="submit-run-with-tensorflow-estimator"></a>Skicka körning med TensorFlow-uppskattning

TensorFlow-uppskattningen är ett enkelt sätt att starta ett TensorFlow-utbildningsjobb på ett beräkningsmål. Det implementeras genom den [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) allmänna klassen, som kan användas för att stödja alla ramverk. Mer information om träningsmodeller med hjälp av den allmänna uppskattningen finns i [tågmodeller med Azure Machine Learning med uppskattning](how-to-train-ml-models.md)

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

Du kan starta TensorBoard under körningen eller när den är klar. I det följande skapar vi en TensorBoard-objektförekomst, `tb`som tar `run`experimentkörningshistoriken som läses in i och sedan startar TensorBoard med `start()` metoden. 
  
[TensorBoard-konstruktorn](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py) tar en rad körningar, så se till att den skickas in som en matris med ett element.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

>[!Note]
 Även om det här exemplet används TensorFlow, tensorboard kan användas lika enkelt med PyTorch eller Chainer modeller. TensorFlow måste finnas tillgängligt på maskinen som kör TensorBoard, men är inte nödvändigt på maskinen gör PyTorch eller Chainer beräkningar. 


<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>Alternativ 2: Exportera historik som logg att visa i TensorBoard

Följande kod ställer in ett exempelexperiment, startar loggningsprocessen med azure machine learning-körningshistorik API:er och exporterar experimentkörningshistoriken till loggar som kan förbrukningsvaror av TensorBoard för visualisering. 

### <a name="set-up-experiment"></a>Konfigurera experiment

Följande kod ställer in ett nytt experiment `root_run`och namnger körkatalogen . 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

Här laddar vi diabetesdatauppsättningen - en inbyggd liten datauppsättning som kommer med scikit-learn, och delar upp den i test- och träningsuppsättningar.

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

### <a name="run-experiment-and-log-metrics"></a>Kör experiment- och loggmått

För den här koden tränar vi en linjär regressionsmodell och loggnyckelmått, alfakoefficienten `alpha`och medelkvadratfel, `mse`i körningshistorik.

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

### <a name="export-runs-to-tensorboard"></a>Export körs till TensorBoard

Med SDK:s [export_to_tensorboard()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py) metod kan vi exportera körningshistoriken för vårt Azure machine learning-experiment till TensorBoard-loggar, så att vi kan visa dem via TensorBoard.  

I följande kod skapar vi `logdir` mappen i vår nuvarande arbetskatalog. Den här mappen är där vi kommer att `root_run` exportera vår experimentkörningshistorik och loggar från och sedan markera den som körs som slutförd. 

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
 Du kan också exportera en viss körning till TensorBoard genom att ange namnet på körningen`export_to_tensorboard(run_name, logdir)`

### <a name="start-and-stop-tensorboard"></a>Starta och stoppa TensorBoard
När vår körhistorik för det här experimentet har exporterats kan vi starta TensorBoard med [metoden start().](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-) 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

När du är klar, se till att anropa [stop()-metoden](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--) för TensorBoard-objektet. Annars fortsätter TensorBoard att köras tills du stänger av anteckningsbokskärnan. 

```python
tb.stop()
```

## <a name="next-steps"></a>Nästa steg

I den här inställningen skapade du två experiment och lärde sig att starta TensorBoard mot deras körhistorik för att identifiera områden för potentiell justering och omskolning. 

* Om du är nöjd med din modell, gå över till vår [Hur du distribuerar en modell](how-to-deploy-and-where.md) artikel. 
* Läs mer om [hyperparameterjustering](how-to-tune-hyperparameters.md). 
