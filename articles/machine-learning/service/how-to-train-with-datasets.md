---
title: Träna med azureml-datauppsättningar
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder data uppsättningar i utbildning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 09/16/2019
ms.openlocfilehash: ceccc515b73bd41c7933889c61617c360c678eb7
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059299"
---
# <a name="train-with-datasets-preview-in-azure-machine-learning"></a>Träna med data uppsättningar (för hands version) i Azure Machine Learning

I den här artikeln får du lära dig de två sätt som du kan använda för att använda [Azure Machine Learning data uppsättningar](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) i träning för fjärrkörning utan att oroa dig om anslutnings strängar eller data Sök vägar.

- Alternativ 1: Skicka data uppsättningar direkt i ditt utbildnings skript.

- Alternativ 2: Använd data uppsättningar för att montera eller ladda ned filer till en fjärrberäkning för utbildning.

## <a name="prerequisites"></a>Förutsättningar

För att skapa och träna med data uppsättningar behöver du:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

* En [Azure Machine Learning service-arbetsyta](how-to-manage-workspace.md).

* [Azure Machine Learning SDK för python installerat](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), som innehåller paketet azureml-DataSets.

> [!Note]
> Vissa data uppsättnings klasser (för hands version) har beroenden för [azureml-nu-](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) paketet. För Linux-användare stöds dessa klasser endast för följande distributioner:  Red Hat Enterprise Linux, Ubuntu, Fedora och CentOS.

## <a name="option-1-pass-datasets-as-inputs-to-training-scripts"></a>Alternativ 1: Skicka data uppsättningar som indata till utbildnings skript

Azure Machine Learning data uppsättningar ger en sömlös integrering med Azure Machine Learning utbildnings produkter som [ScriptRun](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py), [uppskattning](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) och [HyperDrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py). I det här exemplet skapar du en [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) och använder den som inloggad `estimator` på ditt objekt för utbildning. 

### <a name="create-a-tabulardataset"></a>Skapa en TabularDataset

Följande kod skapar ett oregistrerat TabularDataset från en webb-URL. Du kan också skapa data uppsättningar från lokala filer eller sökvägar i data lager. Läs mer om [hur du skapar data uppsättningar](https://aka.ms/azureml/howto/createdatasets).

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

### <a name="configure-the-estimator"></a>Konfigurera uppskattningen

Ett [uppskattnings](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) objekt används för att skicka experimentet. Azure Machine Learning har förkonfigurerade uppskattningar för vanliga ramverk för maskin inlärning, samt en generisk uppskattning.

Den här koden skapar ett generiskt uppskattnings `est`objekt, som anger

* En skript katalog för dina skript. Alla filer i den här katalogen laddas upp till klusternoderna för körning.
* Övnings skriptet *train_titanic. py*.
* Data uppsättningen för indata för `titanic`träning.
* Compute-målet för experimentet.
* Miljö definitionen för experimentet.

```Python
est = Estimator(source_directory=script_folder,
                entry_script='train_titanic.py',
                # pass dataset object as an input with name 'titanic'
                inputs=[titanic_ds.as_named_input('titanic')],
                compute_target=compute_target,
                environment_definition= conda_env)

# Submit the estimator as part of your experiment run
experiment_run = experiment.submit(est)
experiment_run.wait_for_completion(show_output=True)

```

### <a name="access-the-input-dataset-in-your-training-script"></a>Få åtkomst till data uppsättningen data uppsättning i ditt utbildnings skript

TabularDataset-objekt ger möjlighet att läsa in data till en Pandas-eller Spark-DataFrame så att du kan arbeta med välbekanta data förberedelse-och utbildnings bibliotek. För att dra nytta av den här funktionen kan du skicka en TabularDataset som inmatad i din tränings konfiguration och sedan hämta den i skriptet.

Det gör du genom att komma åt data uppsättningen för [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) indata via objektet i ditt utbildnings skript [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#to-pandas-dataframe--) och använda-metoden. 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic']
# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

## <a name="option-2--mount-files-to-a-remote-compute-target"></a>Alternativ 2:  Montera filer till ett fjärran sluten beräknings mål

Om du vill göra dina datafiler tillgängliga på Compute-målet för träning använder du [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) för att montera eller ladda ned filer som det hänvisas till.

När du monterar ett fil system kopplar du fil systemet till en katalog (monterings punkt) och gör det tillgängligt på beräknings målet. Monteringen är direkt, eftersom filer endast läses in under bearbetningen. Montering stöds och rekommenderas för Linux-baserade beräkningar, inklusive Azure Machine Learning Compute, Virtual Machines och HDInsight. För icke-Linux-baserad beräkning stöds endast hämtning.  

>[!WARNING]
> Om din data storlek överskrider lagrings gränsen för beräknings målet kommer nedladdningen att Miss förväntas.

### <a name="create-a-filedataset"></a>Skapa en FileDataset

I följande exempel skapas en oregistrerad FileDataset från webb-URL: er. Lär dig mer om [hur du skapar data uppsättningar](https://aka.ms/azureml/howto/createdatasets) från andra källor.

```Python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
mnist_ds = Dataset.File.from_files(path = web_paths)
```

### <a name="configure-the-estimator"></a>Konfigurera uppskattningen

I stället för att skicka data uppsättningen `inputs` via-parametern i uppskattningen kan du också skicka data uppsättningen via `script_params` och hämta data Sök vägen (monterings punkten) i utbildnings skriptet via argument. På så sätt kan du undvika beroendet av Azure Machine Learning SDK från ditt utbildnings skript.

Ett [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) -behållarobjekt används för att skicka in körningen för scikit-se experiment.

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    # mount the dataset on the remote compute and pass the mounted path as an argument to the training script
    '--data-folder': mnist_ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.5
}

est = SKLearn(source_directory=script_folder,
              script_params=script_params,
              compute_target=compute_target,
              environment_definition=env,
              entry_script='train_mnist.py')

# Run the experiment
run = experiment.submit(est)
run.wait_for_completion(show_output=True)
```

### <a name="retrieve-the-data-in-your-training-script"></a>Hämta data i ditt utbildnings skript

När du har skickat körningen kommer datafiler som refereras `mnist` till data uppsättningen att monteras till beräknings målet. Följande kod visar hur du hämtar data i skriptet.

```Python
%%writefile $script_folder/train_mnist.py

import argparse
import os
import numpy as np
import glob

from utils import load_data

# retrieve the 2 arguments configured through script_params in estimator
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# get the file paths on the compute
X_train_path = glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0]
X_test_path = glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0]
y_train_path = glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0]
y_test = glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0]

# load train and test set into numpy arrays
X_train = load_data(X_train_path, False) / 255.0
X_test = load_data(X_test_path, False) / 255.0
y_train = load_data(y_train_path, True).reshape(-1)
y_test = load_data(y_test, True).reshape(-1)
```

## <a name="notebook-examples"></a>Exempel på bärbara datorer

[Exempel antecknings böckerna](https://aka.ms/dataset-tutorial) demonstrerar och utökar koncepten i den här artikeln, till exempel, med hjälp av data uppsättningar med ScriptRun-och [HyperdDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) -objekt.

## <a name="next-steps"></a>Nästa steg

* [Träna maskin inlärnings modeller automatiskt](how-to-auto-train-remote.md) med TabularDatasets.

* [Träna bild klassificerings modeller](https://aka.ms/filedataset-samplenotebook) med FileDatasets.
