---
title: Skapa data uppsättningar för att få åtkomst till data med azureml-datauppsättningar
titleSuffix: Azure Machine Learning
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
ms.openlocfilehash: 6c3a8d62bd6b3650f834540bd7bb13027792b091
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076971"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Skapa och få åtkomst till data uppsättningar (för hands version) i Azure Machine Learning

I den här artikeln får du lära dig hur du skapar Azure Machine Learning data uppsättningar (för hands version) och hur du kommer åt data från lokala eller fjärranslutna experiment.

Med Azure Machine Learning data uppsättningar kan du: 

* **Behåll en enda kopia av data i din lagring** som refereras av data uppsättningar. 

* **Kom enkelt åt data under modell träning** utan att behöva oroa dig om anslutnings strängar eller data Sök vägar.

* **Dela data & samar beta** med andra användare.

## <a name="prerequisites"></a>Förutsättningar

Om du vill skapa och arbeta med data uppsättningar behöver du:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

* En [Azure Machine Learning arbets yta](how-to-manage-workspace.md)

* [Azure Machine Learning SDK för python installerat](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), som innehåller paketet azureml-DataSets.

> [!Note]
> Vissa data uppsättnings klasser (för hands version) har beroenden för [azureml-nu-](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) paketet. För Linux-användare stöds dessa klasser endast för följande distributioner:  Red Hat Enterprise Linux, Ubuntu, Fedora och CentOS.

## <a name="dataset-types"></a>Data uppsättnings typer

Data uppsättningar kategoriseras i två typer baserat på hur användarna använder dem i utbildningen. 

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) representerar data i tabell format genom att parsa den angivna filen eller listan med filer. Det ger dig möjlighet att materialisera data till en Pandas-DataFrame. Ett `TabularDataset` objekt kan skapas från CSV-, TSV-, Parquet-filer, SQL-frågeresultat osv. En fullständig lista finns i vår [dokumentation](https://aka.ms/tabulardataset-api-reference).

* [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) refererar till en eller flera filer i dina data lager eller offentliga URL: er. Det ger dig möjlighet att ladda ned eller montera filerna i din beräkning. Filerna kan vara i valfritt format, vilket möjliggör en större mängd maskin inlärnings scenarier, inklusive djup inlärning.

Läs mer om kommande API-ändringar i [här](https://aka.ms/tabular-dataset).

## <a name="create-datasets"></a>Skapa datauppsättningar 

Genom att skapa en data uppsättning skapar du en referens till data käll platsen, tillsammans med en kopia av dess metadata. Data behålls på den befintliga platsen, så ingen extra lagrings kostnad uppstår.

För att data ska kunna nås av Azure Machine Learning måste data uppsättningar skapas från sökvägar i [Azure-datalager](how-to-access-data.md) eller offentliga webb adresser.

Skapa data uppsättningar från ett [Azure-datalager](how-to-access-data.md):

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

TabularDatasets kan skapas via SDK eller med hjälp av landnings sidan för arbets ytan (för hands version). En tidsstämpel kan anges från en kolumn i data eller Sök vägs data lagras i för att möjliggöra en timeseries-trait, som möjliggör enkel och effektiv filtrering med tiden. 

#### <a name="using-the-sdk"></a>Med SDK 

Använd metoden i `TabularDatasetFactory` klassen för att läsa filer i CSV-eller TSV-format och skapa en oregistrerad TabularDataset. [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---partition-format-none-) Om du läser från flera filer aggregeras resultaten i en tabell representation.

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

Använd metoden i `TabularDataset` klassen för att aktivera enkel och effektiv filtrering med tiden. [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) Fler exempel och information hittar du [här](http://aka.ms/azureml-tsd-notebook). 

```Python
# create a TabularDataset with timeseries trait
datastore_paths = [(datastore, 'weather/*/*/*/data.parquet')]

# get a coarse timestamp column from the path pattern
dataset = Dataset.Tabular.from_parquet_files(path=datastore_path, partition_format='weather/{coarse_time:yyy/MM/dd}/data.parquet')

# set coarse timestamp to the virtual column created, and fine grain timestamp from a column in the data
dataset = dataset.with_timestamp_columns(fine_grain_timestamp='datetime', coarse_grain_timestamp='coarse_time')

# filter with timeseries trait specific methods 
data_slice = dataset.time_before(datetime(2019, 1, 1))
data_slice = dataset.time_after(datetime(2019, 1, 1))
data_slice = dataset.time_between(datetime(2019, 1, 1), datetime(2019, 2, 1)) 
data_slice = dataset.time_recent(timedelta(weeks=1, days=1))                  
```

#### <a name="using-the-workspace-landing-page"></a>Använda arbets ytans landnings sida 

Logga in på [sidan med landnings sidan för arbets ytan](https://ml.azure.com) för att skapa en data uppsättning via webb upplevelsen. För närvarande har sidan landnings sidan för arbets ytan bara stöd för att skapa TabularDatasets.

Följande animering visar hur du skapar en data uppsättning i landnings sidan för arbets ytan. 

Först väljer du **data uppsättningar** i avsnittet **till gångar** i det vänstra fönstret. Välj sedan **+ skapa data uppsättning** för att välja källa för din data uppsättning. Detta kan antingen vara från lokala filer, data lager eller offentliga webb adresser. **Inställningarna och för hands versionen** och **schema** formulären fylls i intelligent baserat på filtypen. Välj **Nästa** för att granska dem eller om du vill konfigurera data uppsättningen ytterligare innan du skapar den. Välj **klar** för att skapa data uppsättningen. 

![Skapa en data uppsättning med användar gränssnittet](media/how-to-create-register-datasets/create-dataset-ui.gif)

### <a name="create-filedatasets"></a>Skapa FileDatasets

Använd metoden i `FileDatasetFactory` klassen för att läsa in filer i valfritt format och skapa en oregistrerad FileDataset. [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-)

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

[`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) Använd metoden för att registrera data uppsättningar på din arbets yta så att de kan delas med andra och återanvändas mellan olika experiment.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data')
```

>[!Note]
> Data uppsättningar som skapas via sidan med landnings sidan för arbets ytan registreras automatiskt på arbets ytan. 

## <a name="version-datasets"></a>Versions data uppsättningar

Du kan registrera en ny data uppsättning med samma namn genom att skapa en ny version. Data uppsättnings version är ett sätt att lägga till data i data uppsättningen, så att du kan använda en speciell version av data uppsättningen för experimentering eller framtida åter givning. Typiska scenarier för att överväga versions hantering: 
* När nya data är tillgängliga för omträning.
* När du använder olika metoder för data förberedelse eller funktions teknik.

```Python
# create a TabularDataset from Titanic training data
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
