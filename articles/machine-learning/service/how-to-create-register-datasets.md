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
ms.date: 11/04/2019
ms.openlocfilehash: aabbac60acc53cfccc29fc3dbd06575e09840d83
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497131"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Skapa och få åtkomst till data uppsättningar (för hands version) i Azure Machine Learning

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

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
> Vissa data uppsättnings klasser (för hands version) har beroenden för [azureml-nu-](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) paketet. För Linux-användare stöds dessa klasser endast för följande distributioner: Red Hat Enterprise Linux, Ubuntu, Fedora och CentOS.

## <a name="dataset-types"></a>Data uppsättnings typer

Data uppsättningar kategoriseras i två typer baserat på hur användarna använder dem i utbildningen.

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) representerar data i tabell format genom att parsa den angivna filen eller listan med filer. Det ger dig möjlighet att materialisera data till en Pandas-eller Spark-DataFrame. Ett `TabularDataset`-objekt kan skapas från CSV-, TSV-, Parquet-filer, SQL-frågeresultat osv. En fullständig lista finns i vår [dokumentation](https://aka.ms/tabulardataset-api-reference).

* [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) refererar till en eller flera filer i dina data lager eller offentliga URL: er. Det ger dig möjlighet att ladda ned eller montera filerna i din beräkning. Filerna kan vara i valfritt format, vilket möjliggör en större mängd maskin inlärnings scenarier, inklusive djup inlärning.

Läs mer om kommande API-ändringar i [här](https://aka.ms/tabular-dataset).

## <a name="create-datasets"></a>Skapa datauppsättningar

Genom att skapa en data uppsättning skapar du en referens till data käll platsen, tillsammans med en kopia av dess metadata. Data behålls på den befintliga platsen, så ingen extra lagrings kostnad uppstår. Både TabularDatasets och FileDatasets kan skapas med hjälp av python SDK eller arbets ytans landnings sida (för hands version). 

För att data ska kunna nås av Azure Machine Learning måste data uppsättningar skapas från sökvägar i [Azure-datalager](how-to-access-data.md) eller offentliga webb adresser.

### <a name="using-the-sdk"></a>Med SDK

Skapa data uppsättningar från ett [Azure-datalager](how-to-access-data.md) med python SDK:

* Kontrol lera att du har `contributor` eller `owner` åtkomst till registrerade Azure-datalager.

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

#### <a name="create-a-tabulardataset"></a>Skapa en TabularDataset

TabularDatasets kan skapas via SDK eller med hjälp av Azure Machine Learning Studio. En tidstämpel kan anges från en kolumn i data eller Sök vägs data lagras i för att möjliggöra en Time Series-egenskap, vilket möjliggör enkel och effektiv filtrering med tiden.

Använd [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---partition-format-none-) -metoden i `TabularDatasetFactory`-klassen för att läsa filer i CSV-eller TSV-format och skapa en oregistrerad TabularDataset. Om du läser från flera filer aggregeras resultaten i en tabell representation.

```Python
# create a TabularDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'weather/2018/11.csv'),
                  (datastore, 'weather/2018/12.csv'),
                  (datastore, 'weather/2019/*.csv')
                 ]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

Som standard härleds kolumn data typer automatiskt när TabularDataset skapas. Om de härledda typerna inte är som förväntat, kan du ange kolumn typer med hjälp av följande kod. Läs mer om data typer som stöds [här](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py).

```Python
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |PassengerId|Överlevt|Pclass|Namn|Sex|Ålder|SibSp|Parch|Biljett|Resa|Bagage|Har avlastat
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, Mr. Owen Harris|man|22,0|1|0|A/5 21171|7,2500||S
1|2|True|1|Cumings, fru. John Bradley (Florence Briggs to...|kvinna|38,0|1|0|PC 17599|71,2833|C85|C
2|3|True|3|Heikkinen, saknar. Laina|kvinna|26,0|0|0|STON/O2. 3101282|7,9250||S

Använd [`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none-) -metoden i `TabularDatasetFactory`-klassen för att läsa från Azure SQL Database.

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

I TabularDatasets kan en tidstämpel anges från en kolumn i data eller Sök vägs data lagras i för att möjliggöra en tids serie egenskap, vilket möjliggör enkel och effektiv filtrering med tiden.

Använd [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) -metoden i `TabularDataset`-klassen för att ange din tidsstämpel-kolumn och aktivera filtrering efter tid. Fler exempel och information hittar du [här](https://aka.ms/azureml-tsd-notebook).

```Python
# create a TabularDataset with time series trait
datastore_paths = [(datastore, 'weather/*/*/*/data.parquet')]

# get a coarse timestamp column from the path pattern
dataset = Dataset.Tabular.from_parquet_files(path=datastore_path, partition_format='weather/{coarse_time:yyy/MM/dd}/data.parquet')

# set coarse timestamp to the virtual column created, and fine grain timestamp from a column in the data
dataset = dataset.with_timestamp_columns(fine_grain_timestamp='datetime', coarse_grain_timestamp='coarse_time')

# filter with time-series-trait-specific methods
data_slice = dataset.time_before(datetime(2019, 1, 1))
data_slice = dataset.time_after(datetime(2019, 1, 1))
data_slice = dataset.time_between(datetime(2019, 1, 1), datetime(2019, 2, 1))
data_slice = dataset.time_recent(timedelta(weeks=1, days=1))
```

#### <a name="create-a-filedataset"></a>Skapa en FileDataset

Använd metoden [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) i `FileDatasetFactory`-klassen för att läsa in filer i valfritt format och skapa en oregistrerad FileDataset.

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

#### <a name="on-the-web"></a>På webben 
Följande steg och animering visar hur du skapar en data uppsättning i Azure Machine Learning Studio https://ml.azure.com.

![Skapa en data uppsättning med användar gränssnittet](media/how-to-create-register-datasets/create-dataset-ui.gif)

Så här skapar du en data uppsättning i Studio:
1. Logga in på https://ml.azure.com.
1. Välj **data uppsättningar** i avsnittet **till gångar** i det vänstra fönstret. 
1. Välj **+ skapa data uppsättning** för att välja källa för din data uppsättning. Detta kan antingen vara från lokala filer, data lager eller offentliga webb adresser.
1. Välj **tabell** eller **fil** för data uppsättnings typ.
1. Välj **Nästa** för att granska formulären **Inställningar och för hands version**, **schema** och **bekräfta information** . de fylls i intelligent utifrån filtypen. Använd dessa formulär för att kontrol lera dina val och för att konfigurera data uppsättningen ytterligare innan du skapar den.  
1. Välj **skapa** för att slutföra skapandet av data uppsättningen.

## <a name="register-datasets"></a>Registrera data uppsättningar

För att slutföra skapandet av processen registrerar du dina data uppsättningar med en arbets yta.

Använd [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) metoden för att registrera data uppsättningar med arbets ytan så att de kan delas med andra och återanvändas mellan olika experiment.

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

> [!Note]
> Data uppsättningar som skapas via Azure Machine Learning Studio registreras automatiskt på arbets ytan.

## <a name="create-datasets-with-azure-open-datasets"></a>Skapa data uppsättningar med Azure Open-datauppsättningar

[Azure Open-datauppsättningar](https://azure.microsoft.com/services/open-datasets/) är granskade offentliga data uppsättningar som du kan använda för att lägga till scenario-/regionsspecifika funktioner till maskin inlärnings lösningar för mer exakta modeller. Data uppsättningar omfattar data från offentliga domäner för väder, räkning, helger, offentlig säkerhet och plats som hjälper dig att träna maskin inlärnings modeller och utöka förutsägelse lösningar. Öppna data uppsättningar finns i molnet på Microsoft Azure och ingår i både SDK-och arbets ytans användar gränssnitt.

### <a name="using-the-sdk"></a>Med SDK

Om du vill skapa data uppsättningar med Azure Open data uppsättningar från SDK kontrollerar du att du har installerat paketet med `pip install azureml-opendatasets`. Varje diskret data uppsättning representeras av en egen klass i SDK och vissa klasser är tillgängliga antingen som en `TabularDataset`, `FileDataset`eller båda. En fullständig lista över klasser finns i [referens dokumentationen](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) .

De flesta klasser ärver från och returnerar en instans av `TabularDataset`. Exempel på dessa klasser är `PublicHolidays`, `BostonSafety`och `UsPopulationZip`. Om du vill skapa en `TabularDataset` från dessa typer av klasser använder du konstruktorn utan argument. När du registrerar en data uppsättning som skapats från öppna data uppsättningar hämtas inga data direkt, men data kommer att kommas åt senare när de begärs (under utbildning, till exempel) från en central lagrings plats. 

```python
from azureml.opendatasets import UsPopulationZip

tabular_dataset = UsPopulationZip()
tabular_dataset = tabular_dataset.register(workspace=workspace, name="pop data", description="US population data by zip code")
```

Vissa klasser kan hämtas antingen som en `TabularDataset` eller `FileDataset`, vilket gör att du kan manipulera och/eller ladda ned filerna direkt. Andra klasser kan bara hämta en data uppsättning med hjälp av funktionerna `get_tabular_dataset()` **eller** `get_file_dataset()`. I följande kod exempel visas några exempel på dessa typer av klasser.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY return TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

### <a name="using-the-ui"></a>Använda användar gränssnittet

Du kan också skapa data uppsättningar från öppna data uppsättnings klasser med hjälp av användar gränssnittet. I arbets ytan navigerar du till fliken **data uppsättningar** under *till gångar*. Klicka på list rutan **skapa data mängd** och klicka sedan på **från öppna data uppsättningar**.

![Öppna data uppsättning med användar gränssnittet](media/how-to-create-register-datasets/open-datasets-1.png)

Välj sedan en data uppsättning genom att välja panelen, filtrera med hjälp av Sök fältet. Klicka sedan på **Nästa**.

![Välj data uppsättning](media/how-to-create-register-datasets/open-datasets-2.png)

Välj sedan ett namn för att registrera data uppsättningen och filtrera informationen om med hjälp av tillgängliga filter. I det här fallet för data uppsättningen för offentliga helgdagar filtrerar du tids perioden till ett år och lands koden till endast USA. Klicka sedan på **Skapa**.

![Ange data uppsättnings parametrar och skapa data uppsättning](media/how-to-create-register-datasets/open-datasets-3.png)

Data uppsättningen har nu skapats och är tillgänglig i din arbets yta under **data uppsättningar**och kan användas på samma sätt som andra data uppsättningar som du har skapat.

## <a name="version-datasets"></a>Versions data uppsättningar

Du kan registrera en ny data uppsättning med samma namn genom att skapa en ny version. Data uppsättnings version är ett sätt att lägga till data i data uppsättningen, så att du kan använda en speciell version av data uppsättningen för experimentering eller framtida åter givning. Läs mer om [data uppsättnings versioner](how-to-version-track-datasets.md).
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


## <a name="access-datasets-in-your-script"></a>Få åtkomst till data uppsättningar i skriptet

Registrerade data uppsättningar kan nås lokalt och via fjärr anslutning på beräknings kluster som Azure Machine Learning Compute. Använd följande kod för att hämta din arbets yta och registrerad data uppsättning efter namn för att få åtkomst till din registrerade data uppsättning över experiment. [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) -metoden i klassen `Dataset` som standard returnerar den senaste versionen av data uppsättningen som är registrerad på arbets ytan.

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

* Lär dig [hur du tränar med data uppsättningar](how-to-train-with-datasets.md).
* Använd automatisk maskin inlärning för att [träna med TabularDatasets](https://aka.ms/automl-dataset).
* Fler exempel på utbildning med data uppsättningar finns i [exempel antecknings böckerna](https://aka.ms/dataset-tutorial).
