---
title: Träna med azureml-datauppsättningar
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder data uppsättningar i utbildning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 7a3f839a676723942af2e669839457ed3246aabd
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90885883"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>Träna med data uppsättningar i Azure Machine Learning


I den här artikeln får du lära dig hur du arbetar med [Azure Machine Learning data uppsättningar](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py&preserve-view=true) i dina utbildnings experiment.  Du kan använda data uppsättningar i det lokala eller fjärranslutna beräknings målet utan att behöva oroa dig om anslutnings strängar eller data Sök vägar.

Azure Machine Learning data uppsättningar ger en sömlös integrering med Azure Machine Learning utbildnings produkter som [ScriptRun](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py&preserve-view=true), [uppskattare](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py&preserve-view=true), [HyperDrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py&preserve-view=true) och [Azure Machine Learning pipelines](how-to-create-your-first-pipeline.md).

## <a name="prerequisites"></a>Förutsättningar

För att skapa och träna med data uppsättningar behöver du:

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

* En [Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

* [Azure Machine Learning SDK för python installerat](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true), som innehåller paketet azureml-DataSets.

> [!Note]
> Vissa data uppsättnings klasser är beroende av [azureml-nu-](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py&preserve-view=true) paketet. För Linux-användare stöds dessa klasser endast för följande distributioner: Red Hat Enterprise Linux, Ubuntu, Fedora och CentOS.

## <a name="access-and-explore-input-datasets"></a>Komma åt och utforska indata-datauppsättningar

Du kan komma åt en befintlig TabularDataset från övnings skriptet för ett experiment på din arbets yta och läsa in data uppsättningen i en Pandas dataframe för ytterligare utforskning i din lokala miljö.

I följande kod används- [`get_context()`]() metoden i- [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&preserve-view=true) klassen för att komma åt den befintliga TabularDataset, `titanic` i övnings skriptet. Använder sedan [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) metoden för att läsa in data uppsättningen i en Pandas-dataframe för ytterligare data utforskning och förberedelser innan utbildning.

> [!Note]
> Om den ursprungliga data källan innehåller NaN, tomma strängar eller tomma värden, ersätts dessa värden som ett *Null* -värde när du använder to_pandas_dataframe (). 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic']

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

Om du behöver läsa in de data som för bereddes i en ny data uppsättning från en minnes Pandas-dataframe skriver du data till en lokal fil, t. ex. en Parquet, och skapar en ny data uppsättning från filen. Du kan också skapa data uppsättningar från lokala filer eller sökvägar i data lager. Läs mer om [hur du skapar data uppsättningar](how-to-create-register-datasets.md).

## <a name="use-datasets-directly-in-training-scripts"></a>Använda data uppsättningar direkt i tränings skript

Om du har strukturerade data som ännu inte har registrerats som en data uppsättning skapar du en TabularDataset och använder den direkt i ditt utbildnings skript för ditt lokala eller fjärranslutna experiment.

I det här exemplet skapar du en oregistrerad [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true) och använder den som direkt indatamängd för ditt `estimator` objekt för utbildning. Om du vill återanvända den här TabularDataset med andra experiment i din arbets yta, se [så här registrerar du data uppsättningar på din arbets yta](how-to-create-register-datasets.md#register-datasets).

### <a name="create-a-tabulardataset"></a>Skapa en TabularDataset

Följande kod skapar ett oregistrerat TabularDataset från en webb-URL.  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

TabularDataset-objekt ger möjlighet att läsa in data i din TabularDataset i en Pandas-eller Spark-DataFrame så att du kan arbeta med välbekanta data förberedelse-och utbildnings bibliotek utan att behöva lämna din antecknings bok. För att dra nytta av den här funktionen, se [åtkomst och utforska indata-datauppsättningar](#access-and-explore-input-datasets).

### <a name="configure-the-estimator"></a>Konfigurera uppskattningen

Ett [uppskattnings](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py&preserve-view=true) objekt används för att skicka experimentet. Azure Machine Learning har förkonfigurerade uppskattningar för vanliga ramverk för maskin inlärning, samt en generisk uppskattning.

Den här koden skapar ett generiskt uppskattnings objekt, `est` som anger

* En skript katalog för dina skript. Alla filer i den här katalogen laddas upp till klusternoderna för körning.
* Övnings skriptet *train_titanic. py*.
* Data uppsättningen för indata för träning `titanic_ds` . `as_named_input()` krävs för att data uppsättningen ska refereras av det tilldelade namnet `titanic` i ditt utbildnings skript. 
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

## <a name="mount-files-to-remote-compute-targets"></a>Montera filer på fjärranslutna beräknings mål

Om du har ostrukturerade data kan du skapa en [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py&preserve-view=true) och antingen montera eller ladda ned dina datafiler så att de blir tillgängliga för ditt fjärrberäknings mål för utbildning. Lär dig mer om hur du använder [montering jämfört med att ladda ned](#mount-vs-download) för dina experiment. 

I följande exempel skapas en FileDataset och monterar data uppsättningen till Compute-målet genom att skicka den som ett argument i uppskattnings verktyget för utbildning. 

> [!Note]
> Om du använder en anpassad Docker-bas avbildning måste du installera säkring via `apt-get install -y fuse` som ett beroende för att data uppsättnings montering ska fungera. Lär dig hur du [skapar en anpassad versions avbildning](how-to-deploy-custom-docker-image.md#build-a-custom-base-image).

### <a name="create-a-filedataset"></a>Skapa en FileDataset

I följande exempel skapas en oregistrerad FileDataset från webb-URL: er. Lär dig mer om [hur du skapar data uppsättningar](how-to-create-register-datasets.md) från andra källor.

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

Vi rekommenderar att du skickar data uppsättningen som ett argument vid montering. Förutom att skicka data uppsättningen via `inputs` -parametern i uppskattningen kan du också skicka data uppsättningen via `script_params` och hämta data Sök vägen (monterings punkten) i utbildnings skriptet via argument. På så sätt kan du använda samma utbildnings skript för lokal fel sökning och fjärran sluten utbildning på vilken moln plattform som helst.

Ett [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py&preserve-view=true) -behållarobjekt används för att skicka in körningen för scikit-se experiment. När du har skickat körningen kommer datafiler som refereras `mnist` till data uppsättningen att monteras till beräknings målet. Lär dig mer om utbildning med [SKlearn-uppskattningen](how-to-train-scikit-learn.md).

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

Följande kod visar hur du hämtar data i skriptet.

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

## <a name="mount-vs-download"></a>Montering mot nedladdning

Det finns stöd för att montera eller hämta filer av alla format för data uppsättningar som skapats från Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database och Azure Database for PostgreSQL. 

När du **monterar** en data uppsättning bifogar du filerna som data uppsättningen refererar till i en katalog (monterings punkt) och gör den tillgänglig på beräknings målet. Montering stöds för Linux-baserade beräkningar, inklusive Azure Machine Learning Compute, Virtual Machines och HDInsight. 

När du **hämtar** en data uppsättning laddas alla filer som refereras till av data uppsättningen ned till beräknings målet. Hämtning stöds för alla beräknings typer. 

Om skriptet bearbetar alla filer som refereras av data uppsättningen, och din beräknings disk kan passa din fullständiga data uppsättning, rekommenderas nedladdningen för att undvika att strömma data strömmas från lagrings tjänsterna. Om data storleken överskrider beräknings disk storleken är det inte möjligt att hämta. I det här scenariot rekommenderar vi montering eftersom endast de datafiler som används av skriptet läses in vid tidpunkten för bearbetningen.

Följande kod monteras `dataset` i Temp-katalogen på `mounted_path`

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

## <a name="access-datasets-in-your-script"></a>Få åtkomst till data uppsättningar i skriptet

Registrerade data uppsättningar kan nås både lokalt och via fjärr anslutning på beräknings kluster som Azure Machine Learning Compute. Använd följande kod för att komma åt din arbets yta och registrerad data uppsättning efter namn för att få åtkomst till din registrerade data uppsättning över experiment. Som standard [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#&preserve-view=trueget-by-name-workspace--name--version--latest--) returnerar metoden i `Dataset` klassen den senaste versionen av data uppsättningen som är registrerad på arbets ytan.

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="accessing-source-code-during-training"></a>Åtkomst till käll kod under träning

Azure Blob Storage har högre överföringshastigheter än en Azure-filresurs och kommer att skalas till ett stort antal jobb som startas parallellt. Därför rekommenderar vi att du konfigurerar dina körningar för att använda blob-lagring för överföring av källfiler.

I följande kod exempel anges i den körnings konfiguration som BLOB data lager ska användas för käll kods överföringar.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>Exempel på bärbara datorer

[Antecknings böckerna för data uppsättningen](https://aka.ms/dataset-tutorial) demonstrerar och utökar koncepten i den här artikeln.

## <a name="next-steps"></a>Nästa steg

* [Träna maskin inlärnings modeller automatiskt](how-to-auto-train-remote.md) med TabularDatasets.

* [Träna bild klassificerings modeller](https://aka.ms/filedataset-samplenotebook) med FileDatasets.

* [Träna med data uppsättningar med pipelines](how-to-create-your-first-pipeline.md).
