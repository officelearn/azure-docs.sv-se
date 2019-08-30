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
ms.date: 08/22/2019
ms.openlocfilehash: e5d5d36e82914f1d6d03299db0ed1427ac5a389a
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147575"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Skapa och få åtkomst till data uppsättningar (för hands version) i Azure Machine Learning

I den här artikeln får du lära dig hur du skapar Azure Machine Learning data uppsättningar (för hands version) och hur du kommer åt data från lokala eller fjärranslutna experiment.

Med Azure Machine Learning data uppsättningar kan du: 

* **Behåll en enda kopia av data i din lagring** som refereras av data uppsättningar. 

* **Kom enkelt åt data under modell träning** utan att behöva oroa dig om anslutnings strängar eller data Sök vägar.

* **Dela data & samar beta** med andra användare.

## <a name="prerequisites"></a>Förutsättningar

Om du vill skapa och arbeta med data uppsättningar behöver du:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

* En [Azure Machine Learning service-arbetsyta](how-to-manage-workspace.md)

* [Azure Machine Learning SDK för python installerat](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), som innehåller paketet azureml-DataSets.

> [!Note]
> Vissa data uppsättnings klasser (för hands version) har beroenden för [azureml-nu-](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) paketet. För Linux-användare stöds dessa klasser endast för följande distributioner:  Red Hat Enterprise Linux, Ubuntu, Fedora och CentOS.

## <a name="dataset-types"></a>Data uppsättnings typer

Data uppsättningar kategoriseras i olika typer baserat på hur användarna använder dem i utbildningen. Lista över data uppsättnings typer:
* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) representerar data i tabell format genom att parsa den angivna filen eller listan med filer. Det ger dig möjlighet att materialisera data till en Pandas-DataFrame. Ett `TabularDataset` objekt kan skapas från CSV-, TSV-, Parquet-filer, SQL-frågeresultat osv. En fullständig lista finns i vår [dokumentation](https://aka.ms/tabulardataset-api-reference).
* FileDataset refererar till en eller flera filer i dina data lager eller offentliga URL: er. Det ger dig möjlighet att ladda ned eller montera filerna i din beräkning. Filerna kan vara i valfritt format, vilket möjliggör en större mängd maskin inlärnings scenarier, inklusive djup inlärning.

Läs mer om kommande API-ändringar i [här](https://aka.ms/tabular-dataset).

## <a name="create-datasets"></a>Skapa datauppsättningar 

Genom att skapa en data uppsättning skapar du en referens till data käll platsen, tillsammans med en kopia av dess metadata. Data behålls på den befintliga platsen, så ingen extra lagrings kostnad uppstår.

För att data ska vara tillgängliga via Azure Machine Learning-tjänsten måste data uppsättningar skapas från sökvägar i [Azure](how-to-access-data.md) -datalager eller offentliga webb-URL: er.

Skapa data uppsättningar från ett [Azure](how-to-access-data.md)-datalager:

* Kontrol lera att `contributor` du `owner` har eller åtkomst till registrerade Azure-datalager.

* Skapa data uppsättningen genom att referera till en sökväg i data lagret.

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()

# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)
```
### <a name="create-tabulardatasets"></a>Skapa TabularDatasets

Använd metoden i `TabularDatasetFactory` klassen för att läsa filer i CSV-eller TSV-format och skapa en oregistrerad TabularDataset. `from_delimited_files()` Om du läser från flera filer aggregeras resultaten i en tabell representation.

```Python
# create a TabularDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'weather/2018/11.csv'),
                  (datastore, 'weather/2018/12.csv'),
                  (datastore, 'weather/2019/*.csv')
                 ]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)

# create a TabularDataset from a delimited file behind a public web url
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |PassengerId|Överlevt|Pclass|Name|Sex|Ålder|SibSp|Parch|Biljett|Resa|Bagage|Har avlastat
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|0|3|Braund, Mr. Owen Harris|man|22,0|1|0|A/5 21171|7,2500||s
1|2|1|1|Cumings, fru. John Bradley (Florence Briggs to...|kvinna|38,0|1|0|PC 17599|71,2833|C85|C
2|3|1|3|Heikkinen, saknar. Laina|kvinna|26,0|0|0|STON/O2. 3101282|7,9250||s

### <a name="create-filedatasets"></a>Skapa FileDatasets
Använd metoden i `FileDatasetFactory` klassen för att läsa in filer i valfritt format och skapa en oregistrerad FileDataset. `from_files()`

```Python
# create a FileDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'animals/dog/1.jpg'),
                  (datastore, 'animals/dog/2.jpg'),
                  (datastore, 'animals/dog/*.jpg')
                 ]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = [
            'https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
            'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz'
           ]          
mnist_ds = Dataset.File.from_files(path=web_paths)
```
## <a name="register-datasets"></a>Registrera data uppsättningar

För att slutföra skapandet av processen registrerar du dina data uppsättningar med arbets ytan:

`register()` Använd metoden för att registrera data uppsättningar på din arbets yta så att de kan delas med andra och återanvändas mellan olika experiment.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data')
```

## <a name="version-datasets"></a>Versions data uppsättningar

Du kan registrera en ny data uppsättning med samma namn genom att skapa en ny version. Data uppsättnings version är ett sätt att lägga till data i data uppsättningen, så att du kan använda en speciell version av data uppsättningen för experimentering eller framtida åter givning. Typiska scenarier för att överväga versions hantering: 
* När nya data är tillgängliga för omträning.
* När du använder olika metoder för data förberedelse eller funktions teknik.

```Python
# create a TabularDataset from new Titanic training data
web_paths = [
            'https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
            'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv'
           ]          
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```


## <a name="access-your-data-during-training"></a>Få åtkomst till dina data under utbildningen

Registrerade data uppsättningar kan nås lokalt och via fjärr anslutning på beräknings kluster som Azure Machine Learning Compute. Använd följande kod för att hämta din arbets yta och registrerad data uppsättning efter namn för att få åtkomst till din registrerade data uppsättning över experiment. \- [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) Metoden`Dataset` i klassen returnerar den senaste versionen av data uppsättningen som är registrerad på arbets ytan.

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

## <a name="next-steps"></a>Nästa steg

* Använd automatisk maskin inlärning för att [träna med TabularDatasets](https://aka.ms/automl-dataset).
* Fler exempel på utbildning med data uppsättningar finns i [exempel antecknings böckerna](https://aka.ms/dataset-tutorial).
