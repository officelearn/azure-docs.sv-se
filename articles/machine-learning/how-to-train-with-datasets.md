---
title: Träna med azureml-datauppsättningar
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder datauppsättningar under utbildning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.openlocfilehash: 401383f2d483836bf725051810d78167869f7b22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283503"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>Träna med datauppsättningar i Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln lär du dig de två sätten att använda [Azure Machine Learning-datauppsättningar](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) i en fjärrexperimentutbildning körs utan att behöva oroa dig för anslutningssträngar eller datasökvägar.

- Alternativ 1: Om du har strukturerad data skapar du en tabelldatauppsättning och använder den direkt i ditt träningsskript.

- Alternativ 2: Om du har ostrukturerade data skapar du en FileDataset och monterar eller hämtar filer till en fjärrberäkning för träning.

Azure Machine Learning-datauppsättningar ger en sömlös integrering med Azure Machine Learning-utbildningsprodukter som [ScriptRun,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py) [Estimator,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) [HyperDrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py) och [Azure Machine Learning pipelines](how-to-create-your-first-pipeline.md).

## <a name="prerequisites"></a>Krav

För att skapa och träna med datauppsättningar behöver du:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

* En [Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

* [Azure Machine Learning SDK för Python installerat](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), som innehåller azureml-dataset-paketet.

> [!Note]
> Vissa Dataset-klasser har beroenden på [azureml-dataprep-paketet.](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) För Linux-användare stöds dessa klasser endast på följande distributioner: Red Hat Enterprise Linux, Ubuntu, Fedora och CentOS.

## <a name="option-1-use-datasets-directly-in-training-scripts"></a>Alternativ 1: Använd datauppsättningar direkt i utbildningsskript

I det här exemplet skapar du en [Tabelldatauppsättning](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) och använder `estimator` den som en direkt indata till objektet för träning. 

### <a name="create-a-tabulardataset"></a>Skapa en tabelldatauppsättning

Följande kod skapar en oregistrerad Tabelldatauppsättning från en webbadress. Du kan också skapa datauppsättningar från lokala filer eller sökvägar i datalager. Läs mer om [hur du skapar datauppsättningar](https://aka.ms/azureml/howto/createdatasets).

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

### <a name="access-the-input-dataset-in-your-training-script"></a>Få tillgång till indatauppsättningen i utbildningsskriptet

TabularDataset-objekt ger möjlighet att läsa in data i en pandas eller gnista DataFrame så att du kan arbeta med välbekanta dataförberedelser och utbildningsbibliotek. Om du vill utnyttja den här funktionen kan du skicka en tabelldatauppsättning som indata i träningskonfigurationen och sedan hämta den i skriptet.

Det gör du genom att komma [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) åt indatauppsättningen via [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) objektet i träningsskriptet och använda metoden. 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic']
# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-estimator"></a>Konfigurera kalkylatorn

Ett [uppskattningorobjekt](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) används för att skicka experimentkörningen. Azure Machine Learning har förkonfigurerade uppskattningar för vanliga ramverk för maskininlärning samt en allmän uppskattning.

Den här koden skapar ett allmänt `est`uppskattningsobjekt, som anger

* En skriptkatalog för skripten. Alla filer i den här katalogen laddas upp till klusternoderna för körning.
* Utbildningen skript, *train_titanic.py*.
* Indatauppsättningen för utbildning, `titanic`. `as_named_input()`krävs så att indatauppsättningen kan refereras av det tilldelade namnet i utbildningsskriptet. 
* Beräkningsmålet för experimentet.
* Miljödefinitionen för experimentet.

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


## <a name="option-2--mount-files-to-a-remote-compute-target"></a>Alternativ 2: Montera filer på ett fjärrberäkningsmål

Om du vill göra dina datafiler tillgängliga på beräkningsmålet för utbildning använder du [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) för att montera eller hämta filer som den refererar till.

### <a name="mount-vs-download"></a>Montera kontra Ladda ner

Montering eller hämtning av filer av alla format stöds för datauppsättningar som skapats från Azure Blob-lagring, Azure-filer, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database och Azure Database för PostgreSQL. 

När du monterar en datauppsättning bifogar du filerna som refereras av datauppsättningen till en katalog (monteringspunkt) och gör den tillgänglig på beräkningsmålet. Montering stöds för Linux-baserade beräkningar, inklusive Azure Machine Learning Compute, virtuella datorer och HDInsight. När du hämtar en datauppsättning hämtas alla filer som refereras av datauppsättningen till beräkningsmålet. Nedladdning stöds för alla beräkningstyper. 

Om skriptet bearbetar alla filer som refereras av datauppsättningen och beräkningsdisken kan passa hela datauppsättningen, rekommenderas nedladdning för att undvika omkostnader för strömmande data från lagringstjänster. Om datastorleken överskrider beräkningsdiskstorleken går det inte att hämta. I det här scenariot rekommenderar vi montering eftersom endast de datafiler som används av skriptet läses in vid tidpunkten för bearbetningen.

Följande kod monteras `dataset` på temp-katalogen vid`mounted_path`

```python
import tempfile
mounted_path = tempfile.mkdtemp()

# mount dataset onto the mounted_path of a Linux-based compute
mount_context = dataset.mount(mounted_path)

mount_context.start()

import os
print(os.listdir(mounted_path))
print (mounted_path)
```

### <a name="create-a-filedataset"></a>Skapa en FileDataset

I följande exempel skapas en oregistrerad FileDataset från webb-url:ar. Läs mer om [hur du skapar datauppsättningar](https://aka.ms/azureml/howto/createdatasets) från andra källor.

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

### <a name="configure-the-estimator"></a>Konfigurera kalkylatorn

Förutom att skicka datauppsättningen `inputs` genom parametern i kalkylatorn kan `script_params` du också skicka datauppsättningen genom och få datasökvägen (monteringspunkten) i träningsskriptet via argument. På så sätt kan du hålla ditt träningsskript oberoende av azureml-sdk. Med andra ord kommer du att kunna använda samma utbildningsskript för lokal felsökning och fjärrträning på alla molnplattformar.

Ett [SKLearn-uppskattningsobjekt](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) används för att skicka körningen för scikit-learn-experiment. Läs mer om utbildning med [SKlearn-kalkylatorn](how-to-train-scikit-learn.md).

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

### <a name="retrieve-the-data-in-your-training-script"></a>Hämta data i utbildningsskriptet

När du har skickat körningen monteras datafiler som refereras av datauppsättningen `mnist` till beräkningsmålet. Följande kod visar hur du hämtar data i skriptet.

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

## <a name="notebook-examples"></a>Exempel på anteckningsbok

[Datauppsättningsanteckningsböckerna demonstrerar](https://aka.ms/dataset-tutorial) och utökar begreppen i den här artikeln.

## <a name="next-steps"></a>Nästa steg

* [Automatiskt träna maskininlärningsmodeller](how-to-auto-train-remote.md) med TabularDatasets

* [Modeller för tågbildklassificering](https://aka.ms/filedataset-samplenotebook) med FileDatasets

* [Träna med datauppsättningar med hjälp av rörledningar](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb)
