---
title: Träna med azureml-datauppsättningar
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder data uppsättningar i utbildning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 09/25/2019
ms.openlocfilehash: f87dbedb1428b5884e20a9f7daabea792387fe88
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76543315"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>Träna med data uppsättningar i Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig de två sätt som du kan använda för att använda [Azure Machine Learning data uppsättningar](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) i träning för fjärrkörning utan att oroa dig om anslutnings strängar eller data Sök vägar.

- Alternativ 1: om du har strukturerade data skapar du en TabularDataset och använder den direkt i ditt utbildnings skript.

- Alternativ 2: om du har ostrukturerade data skapar du en FileDataset och monterar eller laddar ned filer till en fjärrberäkning för utbildning.

Azure Machine Learning data uppsättningar ger en sömlös integrering med Azure Machine Learning utbildnings produkter som [ScriptRun](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py), [uppskattning](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) och [HyperDrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py).

## <a name="prerequisites"></a>Krav

För att skapa och träna med data uppsättningar behöver du:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

* En [Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

* [Azure Machine Learning SDK för python installerat](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), som innehåller paketet azureml-DataSets.

> [!Note]
> Vissa data uppsättnings klasser är beroende av [azureml-nu-](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) paketet. För Linux-användare stöds dessa klasser endast för följande distributioner: Red Hat Enterprise Linux, Ubuntu, Fedora och CentOS.

## <a name="option-1-use-datasets-directly-in-training-scripts"></a>Alternativ 1: Använd data uppsättningar direkt i tränings skript

I det här exemplet skapar du en [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) och använder den som direkt inström till `estimator` objekt för utbildning. 

### <a name="create-a-tabulardataset"></a>Skapa en TabularDataset

Följande kod skapar ett oregistrerat TabularDataset från en webb-URL. Du kan också skapa data uppsättningar från lokala filer eller sökvägar i data lager. Läs mer om [hur du skapar data uppsättningar](https://aka.ms/azureml/howto/createdatasets).

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

### <a name="access-the-input-dataset-in-your-training-script"></a>Få åtkomst till data uppsättningen data uppsättning i ditt utbildnings skript

TabularDataset-objekt ger möjlighet att läsa in data till en Pandas-eller Spark-DataFrame så att du kan arbeta med välbekanta data förberedelse-och utbildnings bibliotek. För att dra nytta av den här funktionen kan du skicka en TabularDataset som inmatad i din tränings konfiguration och sedan hämta den i skriptet.

Det gör du genom att komma åt data uppsättningen för indata via [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) -objektet i utbildnings skriptet och använda metoden [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) . 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic_ds']
# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-estimator"></a>Konfigurera uppskattningen

Ett [uppskattnings](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) objekt används för att skicka experimentet. Azure Machine Learning har förkonfigurerade uppskattningar för vanliga ramverk för maskin inlärning, samt en generisk uppskattning.

Den här koden skapar ett generiskt uppskattnings objekt, `est`, som anger

* En skript katalog för dina skript. Alla filer i den här katalogen laddas upp till klusternoderna för körning.
* Övnings skriptet *train_titanic. py*.
* Data uppsättningen för indata för träning, `titanic`. `as_named_input()` krävs så att indata-datauppsättningen kan refereras till av det tilldelade namnet i utbildnings skriptet. 
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

## <a name="option-2--mount-files-to-a-remote-compute-target"></a>Alternativ 2: montera filer till ett fjärran sluten beräknings mål

Om du vill göra dina datafiler tillgängliga på Compute-målet för träning använder du [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) för att montera eller ladda ned filer som det hänvisas till.

### <a name="mount-vs-download"></a>Montera v.s. Ladda ned
När du monterar en data uppsättning bifogar du filerna som data uppsättningen refererar till i en katalog (monterings punkt) och gör den tillgänglig på beräknings målet. Montering stöds för Linux-baserade beräkningar, inklusive Azure Machine Learning Compute, Virtual Machines och HDInsight. Om data storleken överskrider beräknings disk storleken eller om du bara läser in en del av data uppsättningen i skriptet rekommenderas montering. Eftersom nedladdning av en data mängd som är större än disk storleken Miss Miss kan och monteringen bara läser in den del av data som används av skriptet vid tidpunkten för bearbetningen. 

När du hämtar en data uppsättning laddas alla filer som refereras till av data uppsättningen ned till beräknings målet. Hämtning stöds för alla beräknings typer. Om skriptet bearbetar alla filer som refereras till av data uppsättningen och din beräknings disk får plats i din fullständiga data uppsättning, rekommenderas nedladdning för att undvika att strömma data strömmas från lagrings tjänsterna.

Det finns stöd för att montera eller hämta filer av alla format för data uppsättningar som skapats från Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database och Azure Database for PostgreSQL. 

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

Förutom att skicka data uppsättningen via `inputs`-parametern i uppskattningen kan du också skicka data uppsättningen via `script_params` och hämta data Sök vägen (monterings punkten) i utbildnings skriptet via argument. På så sätt kan du behålla ditt utbildnings skript oberoende av azureml-SDK. Med andra ord kommer du att kunna använda samma utbildnings skript för lokal fel sökning och fjärran sluten utbildning på vilken moln plattform som helst.

Ett [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) -behållarobjekt används för att skicka in körningen för scikit-se experiment. Lär dig mer om utbildning med [SKlearn-uppskattningen](how-to-train-scikit-learn.md).

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

När du har skickat körningen kommer datafiler som refereras till av `mnist`-datauppsättning att monteras till beräknings målet. Följande kod visar hur du hämtar data i skriptet.

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

[Antecknings böckerna för data uppsättningen](https://aka.ms/dataset-tutorial) demonstrerar och utökar koncepten i den här artikeln.

## <a name="next-steps"></a>Nästa steg

* [Träna maskin inlärnings modeller automatiskt](how-to-auto-train-remote.md) med TabularDatasets

* [Träna bild klassificerings modeller](https://aka.ms/filedataset-samplenotebook) med FileDatasets

* [Skapa och hantera miljöer för utbildning och distribution](how-to-use-environments.md)
