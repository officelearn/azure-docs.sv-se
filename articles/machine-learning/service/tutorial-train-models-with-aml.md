---
title: 'Självstudie: Träna en modell för avbildningsklassificering med Azure Machine Learning'
description: Den här självstudien visar hur du använder Azure Machine Learning-tjänsten för att träna en modell för avbildningsklassificering med scikit-learn i en Python Jupyter-anteckningsbok. Den här självstudien är del ett i en serie med två delar.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: tutorial
author: hning86
ms.author: haining
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 6fbca5e83d8ab4b3c34c6448c7a2303697da623b
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47181406"
---
# <a name="tutorial-1-train-an-image-classification-model-with-azure-machine-learning"></a>Självstudie 1: Träna en modell för avbildningsklassificering med Azure Machine Learning

I den här självstudien ska du träna en maskininlärningsmodell, både lokalt och på fjärranslutna beräkningsresurser. Du ska använda tränings- och distributionsarbetsflödet för Azure Machine Learning-tjänsten (förhandsversion) i en Python Jupyter-anteckningsbok.  Du kan sedan använda anteckningsboken som en mall för att träna din egen maskininlärningsmodell med egna data. Den här självstudien är **del ett i en självstudieserie i två delar**.  

Den här självstudien tränar en enkel logistikregression med hjälp av [MNIST](http://yann.lecun.com/exdb/mnist/)-datauppsättningen och [scikit-learn](http://scikit-learn.org) med Azure Machine Learning.  MNIST är en populär datauppsättning som består av 70 000 gråskalebilder. Varje bild är en handskriven siffra på 28 × 28 pixlar, som representerar ett tal från 0 till 9. Målet är att skapa en klassificerare för flera klasser som identifierar siffran som en viss bild representerar. 

Lär dig att:

> [!div class="checklist"]
> * Ställ in din utvecklingsmiljö
> * Komma åt och utforska data
> * Träna en enkel logistikregression lokalt med hjälp av det populära maskininlärningsbiblioteket scikit-learn 
> * Träna flera modeller i ett fjärrkluster
> * Granska träningsresultatet och registrera den bästa modellen

Du lär dig hur du väljer en modell och distribuerar den i [del två av de här självstudierna](tutorial-deploy-models-with-aml.md). 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="get-the-notebook"></a>Hämta anteckningsboken

Den här självstudien är tillgänglig som en Jupyter-anteckningsbok. Använd någon av de två metoderna nedan för att klona [GitHub-databasen med exempelanteckningsböckerna för maskininlärning](https://github.com/Azure/MachineLearningNotebooks) och kör `tutorials/01.train-models.ipynb`-anteckningsboken:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

## <a name="set-up-your-development-environment"></a>Ställ in din utvecklingsmiljö

All konfiguration under utvecklingsarbetet kan utföras i en Python-anteckningsbok.  Konfigurationen omfattar följande steg:

* Importera Python-paket
* Anslut till en arbetsyta för att upprätta kommunikation mellan den lokala datorn och fjärranslutna resurser
* Skapa ett experiment för att spåra alla körningar
* Skapa ett fjärrberäkningsmål som ska användas för träning

### <a name="import-packages"></a>Importera paket

Importera Python-paket som du behöver i den här sessionen. Verifiera även Azure Machine Learning SDK-versionen.

```python
%matplotlib inline
import numpy as np
import matplotlib
import matplotlib.pyplot as plt

import azureml
from azureml.core import Workspace, Run

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-workspace"></a>Ansluta till arbetsytan

Skapa ett arbetsyteobjekt från den befintliga arbetsytan. `Workspace.from_config()` läser filen **config.json** och läser in informationen i ett objekt med namnet `ws`.

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, ws.location, sep = '\t')
```

### <a name="create-experiment"></a>Skapa experiment

Skapa ett experiment för att spåra körningarna på arbetsytan. En arbetsyta kan ha flera experiment. 

```python
experiment_name = 'sklearn-mnist'

from azureml.core import Experiment
exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-remote-compute-target"></a>Skapa ett fjärrberäkningsmål

Azure Batch AI är en hanterad tjänst som gör det möjligt för dataexperter att träna maskininlärningsmodeller i kluster med virtuella Azure-datorer, inklusive virtuella datorer med GPU-stöd.  I den här självstudien ska du skapa ett Azure Batch AI-kluster som din träningsmiljö. Den här koden skapar ett kluster om det inte redan finns på din arbetsyta. 

 **Det tar cirka fem minuter att skapar klustret.** Om klustret redan finns på arbetsytan använder den här koden det klustret och hoppar över genereringsprocessen.


```python
from azureml.core.compute import ComputeTarget, BatchAiCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
batchai_cluster_name = "traincluster"

try:
    # look for the existing cluster by name
    compute_target = ComputeTarget(workspace=ws, name=batchai_cluster_name)
    if type(compute_target) is BatchAiCompute:
        print('found compute target {}, just use it.'.format(batchai_cluster_name))
    else:
        print('{} exists but it is not a Batch AI cluster. Please choose a different name.'.format(batchai_cluster_name))
except ComputeTargetException:
    print('creating a new compute target...')
    compute_config = BatchAiCompute.provisioning_configuration(vm_size="STANDARD_D2_V2", # small CPU-based VM
                                                                #vm_priority='lowpriority', # optional
                                                                autoscale_enabled=True,
                                                                cluster_min_nodes=0, 
                                                                cluster_max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, batchai_cluster_name, compute_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it uses the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
    # Use the 'status' property to get a detailed status for the current cluster. 
    print(compute_target.status.serialize())
```

Nu har du de nödvändiga paketen och beräkningsresurserna för att träna en modell i molnet. 

## <a name="explore-data"></a>Utforska data

Innan du tränar en modell måste du förstå de data som du använder för att träna tjänsten.  Du måste också kopiera data till molnet så att de kan användas av molnträningsmiljön.  I det här avsnittet lär du dig att:

* Ladda ned MNIST-datauppsättningen
* Visa några exempelbilder
* Ladda upp data till molnet

### <a name="download-the-mnist-dataset"></a>Ladda ned MNIST-datauppsättningen

Ladda ned MNIST-datauppsättningen och spara filerna i en `data`-katalog lokalt.  Bilder och etiketter för både träning och testning laddas ned.  


```python
import os
import urllib.request

os.makedirs('./data', exist_ok = True)

urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename='./data/train-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename='./data/train-labels.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename='./data/test-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename='./data/test-labels.gz')
```

### <a name="display-some-sample-images"></a>Visa några exempelbilder

Läs in de komprimerade filerna i `numpy`-matriser. Använd sedan `matplotlib` för att rita 30 slumpmässiga bilder från datauppsättningen med tillhörande etiketter ovanför dem. Observera att det här steget kräver en `load_data`-funktion som ingår i `util.py`-filen. Den här filen finns med i exempelmappen. Kontrollera att den finns i samma mapp som den här anteckningsboken. Funktionen `load_data` parsar de komprimerade filerna till numpy-matriser.



```python
# make sure utils.py is in the same directory as this code
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data('./data/train-images.gz', False) / 255.0
y_train = load_data('./data/train-labels.gz', True).reshape(-1)

X_test = load_data('./data/test-images.gz', False) / 255.0
y_test = load_data('./data/test-labels.gz', True).reshape(-1)

# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize = (16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x=10, y=-10, s=y_train[i], fontsize=18)
    plt.imshow(X_train[i].reshape(28, 28), cmap=plt.cm.Greys)
plt.show()
```

Ett slumpmässigt urval av bilder visas:

![slumpmässigt urval av bilder](./media/tutorial-train-models-with-aml/digits.png)

Nu har du en uppfattning om hur dessa bilder ser ut och det förväntade förutsägelseresultatet.

### <a name="upload-data-to-the-cloud"></a>Ladda upp data till molnet

Nu ska du göra data tillgängliga via en fjärranslutning genom att överföra dem från den lokala datorn till Azure så att de kan användas för fjärrträning. Datalagret är en praktisk konstruktion för upp- och nedladdning av data som är kopplad till din arbetsyta och som du kan interagera med från ditt fjärrberäkningsmål. Det använder ditt Azure Blob Storage-konto.

MNIST-filerna överförs till en katalog med namnet `mnist` i datalagrets rot.

```python
ds = ws.get_default_datastore()
print(ds.datastore_type, ds.account_name, ds.container_name)

ds.upload(src_dir='./data', target_path='mnist', overwrite=True, show_progress=True)
```
Nu har du allt du behöver för att börja träna en modell. 

## <a name="train-a-model-locally"></a>Träna en modell lokalt

Träna en enkel logistikregressionsmodell från scikit-learn lokalt.

**Det tar en minut eller två att träna lokalt**, beroende på din datorkonfiguration.

```python
%%time
from sklearn.linear_model import LogisticRegression

clf = LogisticRegression()
clf.fit(X_train, y_train)
```

När du är klar gör du förutsägelser med hjälp av testuppsättningen och beräknar precisionen. 

```python
y_hat = clf.predict(X_test)
print(np.average(y_hat == y_test))
```

Den lokala modellens precision visas:

`0.9202`

Med bara några rader kod har du en precision på 92 %.

## <a name="train-on-a-remote-cluster"></a>Träna i ett fjärrkluster

Nu kan du utöka den här enkla modellen genom att skapa en modell med en annan regulariseringshastighet. Den här gången ska du träna modellen på en fjärransluten resurs.  

I den här aktiviteten skickar du jobbet till det fjärranslutna träningsklustret som du skapade tidigare.  Du skickar ett jobb genom att:
* Skapa en katalog
* Skapa ett träningsskript
* Skapa ett beräkningsobjekt
* Skicka jobbet 

### <a name="create-a-directory"></a>Skapa en katalog

Skapa en katalog för att leverera den nödvändiga koden från din dator till fjärresursen.

```python
import os
script_folder = './sklearn-mnist'
os.makedirs(script_folder, exist_ok=True)
```

### <a name="create-a-training-script"></a>Skapa ett träningsskript

Innan du skickar jobbet till klustret skapar du ett träningsskript. Kör följande kod för att skapa ett träningsskript med namnet `train.py` i katalogen som du nyss skapade. Den här träningen lägger till en regulariseringshastighet i träningsalgoritmen, och skapar därför en något annorlunda modell än den lokala versionen.

```python
%%writefile $script_folder/train.py

import argparse
import os
import numpy as np

from sklearn.linear_model import LogisticRegression
from sklearn.externals import joblib

from azureml.core import Run
from utils import load_data

# let user feed in 2 parameters, the location of the data files (from datastore), and the regularization rate of the logistic regression model
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = os.path.join(args.data_folder, 'mnist')
print('Data folder:', data_folder)

# load train and test set into numpy arrays
# note we scale the pixel intensity values to 0-1 (by dividing it with 255.0) so the model can converge faster.
X_train = load_data(os.path.join(data_folder, 'train-images.gz'), False) / 255.0
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_train = load_data(os.path.join(data_folder, 'train-labels.gz'), True).reshape(-1)
y_test = load_data(os.path.join(data_folder, 'test-labels.gz'), True).reshape(-1)
print(X_train.shape, y_train.shape, X_test.shape, y_test.shape, sep = '\n')

# get hold of the current run
run = Run.get_submitted_run()

print('Train a logistic regression model with regularizaion rate of', args.reg)
clf = LogisticRegression(C=1.0/args.reg, random_state=42)
clf.fit(X_train, y_train)

print('Predict the test set')
y_hat = clf.predict(X_test)

# calculate accuracy on the prediction
acc = np.average(y_hat == y_test)
print('Accuracy is', acc)

run.log('regularization rate', np.float(args.reg))
run.log('accuracy', np.float(acc))

os.makedirs('outputs', exist_ok=True)
# note file saved in the outputs folder is automatically uploaded into experiment record
joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')
```

Observera hur skriptet hämtar data och sparar modeller:

+ Träningsskriptet läser ett argument för att hitta katalogen som innehåller data.  När du skickar jobbet senare pekar du på datalagret för det här argumentet: `parser.add_argument('--data-folder', type = str, dest = 'data_folder', help = 'data directory mounting point')`

    
+ Träningsskriptet sparar din modell i en katalog med namnet outputs. <br/>
`joblib.dump(value = clf, filename = 'outputs/sklearn_mnist_model.pkl')`<br/>
Allt som skrivs i den här katalogen överförs automatiskt till din arbetsyta. Du ska komma åt din modell från den här katalogen senare i självstudien.

Träningsskriptet måste referera till filen `utils.py` för att datauppsättningen ska läsas in korrekt.  Kopiera det här skriptet till skriptmappen så att det kan nås tillsammans med träningsskriptet på den fjärranslutna resursen.


```python
import shutil
shutil.copy('utils.py', script_folder)
```


### <a name="create-an-estimator"></a>Skapa ett beräkningsobjekt

Ett beräkningsobjekt används för att skicka körningen.  Skapa beräkningsobjektet genom att köra följande kod för att definiera:

* Namnet på beräkningsobjektet, `est`
* Katalogen som innehåller dina skript. Alla filer i den här katalogen laddas upp till klusternoderna för körning. 
* Beräkningsmålet.  I det här fallet ska du använda Batch AI-klustret som du skapade
* Träningsskriptets namn, train.py
* Parametrar som krävs från träningsskriptet 
* Python-paket som behövs för träning

I den här självstudien är det här målet Batch AI-klustret. Alla filer i projektkatalogen laddas upp till klusternoderna för körning. Data-folder konfigureras att använda datalagret (`ds.as_mount()`).

```python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

est = Estimator(source_directory=script_folder,
                script_params=script_params,
                compute_target=compute_target,
                entry_script='train.py',
                conda_packages=['scikit-learn'])
```


### <a name="submit-the-job-to-the-cluster"></a>Skicka jobbet till klustret

Kör experimentet genom att skicka beräkningsobjektet.

```python
run = exp.submit(config=est)
run
```

Eftersom anropet är asynkront returneras statusen **Förbereder** eller **Körs** när jobbet har startats.

## <a name="monitor-a-remote-run"></a>Övervaka en fjärrkörning

Den första körningen tar **cirka tio minuter**. Men för efterföljande körningar, under förutsättning att skriptberoendena inte ändras, så återanvänds samma avbildning vilket gör att containern startar mycket snabbare.

Detta händer medan du väntar:

- **Avbildningsgenerering**: En Docker-avbildning skapas som matchar Python-miljön som anges av beräkningsobjektet. Avbildningen laddas upp till arbetsytan. Det tar **cirka fem minuter** att skapa och ladda upp avbildningen. 

  Den här fasen sker en gång för varje Python-miljö eftersom containern cachelagras för efterföljande körningar.  När avbildningen skapas strömmas loggar till körningshistoriken. Du kan övervaka avbildningsgenereringen med hjälp av dessa loggar.

- **Skalning**: Om fjärrklustret kräver fler noder för att köra körningen än vad som är tillgängligt för tillfället, läggs ytterligare noder till automatiskt. Skalningen tar normalt **cirka fem minuter.**

- **Körning**: I det här steget skickas de nödvändiga skripten och filerna till beräkningsmålet. Därefter monteras/kopieras datalager varefter entry_script körs. När jobbet körs strömmas stdout och ./logs-katalogen till körningshistoriken. Du kan övervaka körningsförloppet med hjälp av dessa loggar.

- **Efterbearbetning**: Katalogen ./outputs för körningen kopieras till körningshistoriken på din arbetsyta så att du kan komma åt resultaten.


Du kan kontrollera statusen för ett jobb som körs på flera olika sätt. I den här självstudien används en Jupyter-widget samt en `wait_for_completion`-metod. 

### <a name="jupyter-widget"></a>Jupyter-widget

Följ körningsförloppet med en Jupyter-widget.  Precis som körningsöverföringen är widgeten asynkron och tillhandahåller liveuppdateringar var 10:e till var 15:e sekund tills jobbet har slutförts.


```python
from azureml.train.widgets import RunDetails
RunDetails(run).show()
```

Här är en stillbild av widgeten som visas i slutet av träningen:

![notebook-widget](./media/tutorial-train-models-with-aml/widget.png)

### <a name="get-log-results-upon-completion"></a>Hämta loggresultat när åtgärden har slutförts

Modellträningen och övervakningen sker i bakgrunden. Vänta tills modellen har slutfört träningen innan du kör mer kod. Använd `wait_for_completion` för att visa när modellträningen är klar. 


```python
run.wait_for_completion(show_output=False) # specify True for a verbose log
```

### <a name="display-run-results"></a>Visa körningsresultat

Nu har du en modell som har tränats på ett fjärrkluster.  Hämta modellens precision:

```python
print(run.get_metrics())
```
Resultatet visar att fjärrmodellen har en något högre precision än den lokala modellen tack vare regulariseringshastigheten som lades till under träningen.  

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

I självstudien om distribution ska du analysera den här modellen i detalj.

## <a name="register-model"></a>Registrera modellen

Det sista steget i träningsskriptet skrev filen `outputs/sklearn_mnist_model.pkl` i en katalog med namnet `outputs` på den virtuella datorn i klustret där jobbet körs. `outputs` är en särskild katalog i det avseende att allt innehåll i den här katalogen överförs automatiskt till din arbetsyta.  Det här innehållet visas i körningsposten i experimentet under din arbetsyta. Därför är modellfilen nu även tillgänglig på din arbetsyta.

Du kan se filer som hör till den körningen.

```python
print(run.get_file_names())
```

Registrera modellen på arbetsytan så att du (eller andra medarbetare) senare kan fråga, utforska och distribuera modellen.

```python
# register model 
model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep = '\t')
```

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Du kan också välja att bara ta bort det Azure-hanterade beräkningsklustret. Men eftersom automatisk skalning är aktiverat och klustret lägsta gräns är 0, så debiteras inga ytterligare beräkningsavgifter för den här resursen när den inte används.


```python
# optionally, delete the Azure Managed Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien för Azure Machine Learning har du använt Python för att:

> [!div class="checklist"]
> * Ställ in din utvecklingsmiljö
> * Komma åt och utforska data
> * Träna en enkel logistikregression lokalt med hjälp av det populära maskininlärningsbiblioteket scikit-learn
> * Träna flera modeller i ett fjärrkluster
> * Granska träningsinformationen och registrera den bästa modellen

Nu är du redo att distribuera den registrerade modellen genom att följa anvisningarna i nästa del av den här självstudieserien:

> [!div class="nextstepaction"]
> [Självstudie 2 – Distribuera modeller](tutorial-deploy-models-with-aml.md)
