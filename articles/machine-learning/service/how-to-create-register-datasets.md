---
title: Skapa data uppsättningar för att få åtkomst till data med azureml-datauppsättningar
titleSuffix: Azure Machine Learning service
description: Lär dig hur du skapar data uppsättningar från olika källor och registrerar data uppsättningar med din arbets yta
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/21/2019
ms.openlocfilehash: 473bf87e1961c3c7687b0867885adef40c14d71f
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68694329"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Skapa och få åtkomst till data uppsättningar (för hands version) i Azure Machine Learning

I den här artikeln får du lära dig hur du skapar Azure Machine Learning data uppsättningar (för hands version) och hur du kommer åt data från lokala eller fjärranslutna experiment.

Med Azure Machine Learning data uppsättningar kan du: 

* **Behåll en enda kopia av data i din lagring** som refereras av data uppsättningar

* **Analysera data** genom analys av analys data 

* **Kom enkelt åt data under modell träning** utan att oroa dig för anslutnings strängen eller data Sök vägen

* **Dela data & samar beta** med andra användare

## <a name="prerequisites"></a>Förutsättningar

Om du vill skapa och arbeta med data uppsättningar behöver du:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

* En [Azure Machine Learning service-arbetsyta](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)

* [Azure Machine Learning SDK för python installerat](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), som innehåller paketet azureml-DataSets.

> [!Note]
> Vissa data uppsättnings klasser (för hands version) är beroende av [azureml-nu-](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) paketet (ga). För Linux-användare stöds dessa klasser endast för följande distributioner:  Red Hat Enterprise Linux, Ubuntu, Fedora och CentOS.

## <a name="data-formats"></a>Data format

Du kan skapa en Azure Machine Learning data uppsättning från följande format:
+ [avgränsade](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-delimited-files-path--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-no-rows--0---comment-none--include-path-false--archive-options-none--partition-format-none-)
+ [json](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false--partition-format-none-)
+ [Excel](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true--partition-format-none-)
+ [Parquet](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false--partition-format-none-)
+ [Pandas DataFrame](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-pandas-dataframe-dataframe--path-none--in-memory-false-)
+ [SQL-fråga](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)
+ [binär](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-)

## <a name="create-datasets"></a>Skapa datauppsättningar 

Genom att skapa en data uppsättning skapar du en referens till data käll platsen, tillsammans med en kopia av dess metadata. Data behålls på den befintliga platsen, så ingen extra lagrings kostnad uppstår.

### <a name="create-from-local-files"></a>Skapa från lokala filer

Läs in filer från din lokala dator genom att ange sökvägen till filen eller mappen [`auto_read_files()`](/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false--partition-format-none-) med-metoden `Dataset` från-klassen.  Den här metoden utför följande steg utan att du behöver ange filtyp eller tolknings argument:

* Härleder och anger avgränsare.
* Hoppar över tomma poster överst i filen.
* Härleder och anger rubrik raden.
* Härleda och konvertera kolumn data typer.

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

Du kan också använda filspecifik funktion för att styra tolkningen av filen. 


### <a name="create-from-azure-datastores"></a>Skapa från Azure-datalager

Skapa data uppsättningar från ett [Azure](how-to-access-data.md)-datalager:

* Kontrol lera att `contributor` du `owner` har eller åtkomst till registrerade Azure-datalager.

* Skapa data uppsättningen genom att referera till en sökväg i data lagret 

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()

# retrieve an existing datastore in the workspace by name
dstore = Datastore.get(workspace, datastore_name)
```

Använd metoden för att läsa avgränsade filer från en DataReference och skapa en oregistrerad data uppsättning. [](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) `from_delimited_files()`

```Python
# create an in-memory Dataset on your local machine
dataset = Dataset.from_delimited_files(dstore.path('data/src/crime.csv'))

# returns the first 5 rows of the Dataset as a pandas Dataframe.
dataset.head(5)
```

## <a name="register-datasets"></a>Registrera data uppsättningar

För att slutföra skapandet av processen registrerar du dina data uppsättningar med arbets ytan:

[`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) Använd metoden för att registrera data uppsättningar på din arbets yta så att de kan delas med andra och återanvändas mellan olika experiment.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> Om `exist_ok = False` (standard) och du försöker registrera en data uppsättning med samma namn som en annan uppstår ett fel. Ange till `True` Skriv över befintlig.

## <a name="access-data-in-datasets"></a>Åtkomst till data i data uppsättningar

Registrerade data uppsättningar kan nås lokalt och via fjärr anslutning på beräknings kluster som Azure Machine Learning Compute. Använd följande kod för att hämta din arbets yta och registrerad data uppsättning efter namn för att få åtkomst till din registrerade data uppsättning över experiment.

```Python
workspace = Workspace.from_config()

# See list of datasets registered in workspace.
print(Dataset.list(workspace))

# Get dataset by name
dataset = Dataset.get(workspace, 'dataset_crime')

# Load data into pandas DataFrame
dataset.to_pandas_dataframe()
```

## <a name="next-steps"></a>Nästa steg

* [Utforska och Förbered data uppsättningar](how-to-explore-prepare-data.md).
* Ett exempel på hur du använder data uppsättningar finns i [exempel antecknings böckerna](https://aka.ms/dataset-tutorial).
