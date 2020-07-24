---
title: Skapa Azure Machine Learning data uppsättningar för att få åtkomst till data
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar Azure Machine Learning data uppsättningar för att komma åt dina data för att köra maskin inlärnings experiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 06/29/2020
ms.openlocfilehash: c082c74ab448fda0926b5aab52088bf00fb719bf
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87031175"
---
# <a name="create-azure-machine-learning-datasets"></a>Skapa Azure Machine Learning data uppsättningar

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du skapar Azure Machine Learning data uppsättningar för att komma åt data för dina lokala eller fjärranslutna experiment. Information om var data uppsättningar passar i Azure Machine Learning programmets övergripande data åtkomst arbets flöde finns i artikeln [säker åtkomst till data](concept-data.md#data-workflow) .

Med Azure Machine Learning data uppsättningar kan du:

* Behåll en enda kopia av data i din lagring som refereras till av data uppsättningar.

* Få sömlös åtkomst till data under modell träning utan att behöva oroa dig om anslutnings strängar eller data Sök vägar.

* Dela data och samar beta med andra användare.

## <a name="prerequisites"></a>Förutsättningar

Om du vill skapa och arbeta med data uppsättningar behöver du:

* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

* En [Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

* [Azure Machine Learning SDK för python installerat](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), som innehåller paketet azureml-DataSets.

> [!NOTE]
> Vissa data uppsättnings klasser är beroende av [azureml-nu-](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) paketet, som endast är kompatibelt med 64-bitars python. För Linux-användare stöds dessa klasser endast för följande distributioner: Red Hat Enterprise Linux (7, 8), Ubuntu (14,04, 16,04, 18,04), Fedora (27, 28), Debian (8, 9) och CentOS (7).

## <a name="compute-size-guidance"></a>Vägledning för beräknings storlek

När du skapar en data uppsättning granskar du beräknings bearbetnings kraften och storleken på dina data i minnet. Storleken på dina data i lagringen är inte samma som storleken på data i en dataframe. Till exempel kan data i CSV-filer utökas upp till 10X i en dataframe, så en CSV-fil på 1 GB kan bli 10 GB i en dataframe. 

Huvud faktorn är hur stor data uppsättningen är i minnet, d.v.s. som en dataframe. Vi rekommenderar att din beräknings storlek och bearbetnings kraft innehåller 2x storleken på RAM-minne. Så om din dataframe är 10 GB, vill du ha ett beräknings mål med 20 + GB RAM-minne för att säkerställa att dataframe får plats i minnet och bearbetas. Om dina data är komprimerade kan de utökas ytterligare. 20 GB relativt sparse-data som lagras i ett komprimerat Parquet-format kan expanderas till ~ 800 GB i minnet. Eftersom Parquet-filer lagrar data i ett kolumn format, om du bara behöver hälften av kolumnerna, behöver du bara läsa in ~ 400 GB i minnet.
 
Om du använder Pandas finns det ingen anledning att ha fler än 1 vCPU eftersom det är allt som kommer att användas. Du kan enkelt parallellisera till många virtuella processorer på en enda Azure Machine Learning beräknings instans/nod via MODIR och dask/Ray och skala ut till ett stort kluster om det behövs, genom att helt enkelt ändra `import pandas as pd` till `import modin.pandas as pd` . 
 
Om du inte kan få tillräckligt med data för data kan du välja mellan två alternativ: Använd ett ramverk som Spark eller dask för att utföra bearbetningen av data "slut på minne", d.v.s. dataframe läses in i RAM-partitionen efter partition och bearbetning, med det slutliga resultatet som samlas in i slutet. Om detta är för långsamt kan Spark-eller dask göra att du kan skala ut till ett kluster som fortfarande kan användas interaktivt. 

## <a name="dataset-types"></a>Datamängdstyper

Det finns två typer av data uppsättningar, baserat på hur användarna använder dem i utbildningen:

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) representerar data i tabell format genom att parsa den angivna filen eller listan med filer. Det ger dig möjlighet att materialisera data till en Pandas-eller Spark-DataFrame. Du kan skapa ett `TabularDataset` objekt från. csv-,. tsv-,. Parquet-,. JSON-filer och från SQL-frågeresultat. En fullständig lista finns i [TabularDatasetFactory-klassen](https://aka.ms/tabulardataset-api-reference).

* [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) -klassen refererar till en eller flera filer i dina data lager eller offentliga URL: er. Med den här metoden kan du ladda ned eller montera filerna i beräkningen som ett FileDataset-objekt. Filerna kan vara i valfritt format, vilket möjliggör en större mängd maskin inlärnings scenarier, inklusive djup inlärning. 

## <a name="create-datasets"></a>Skapa datauppsättningar

Genom att skapa en datamängd skapar du en referens till datakällans plats, tillsammans med en kopia av dess metadata. Eftersom data blir kvar på den befintliga platsen debiteras du ingen extra lagringskostnad. Du kan skapa både `TabularDataset` och `FileDataset` data uppsättningar med hjälp av python SDK eller Azure Machine Learning Studio på https://ml.azure.com .

För att data ska kunna nås av Azure Machine Learning måste data uppsättningar skapas från sökvägar i [Azure-datalager](how-to-access-data.md) eller offentliga webb adresser. 

### <a name="use-the-sdk"></a>Använd SDK: n

Skapa data uppsättningar från ett [Azure-datalager](how-to-access-data.md) med hjälp av python SDK:

1. Kontrol lera att du har `contributor` eller `owner` åtkomst till registrerade Azure-datalager.

2. Skapa data uppsättningen genom att referera till sökvägar i data lagret.

> [!Note]
> Du kan skapa en data uppsättning från flera sökvägar i flera data lager. Det finns ingen hård gräns för antalet filer eller data storlekar som du kan skapa en data uppsättning från. För varje data Sök väg skickas dock några begär anden till lagrings tjänsten för att kontrol lera om det pekar på en fil eller mapp. Den här omkostnaderna kan leda till försämrade prestanda eller problem. En data uppsättning som refererar till en mapp med 1000 filer inuti betraktas som en referens till en data Sök väg. Vi rekommenderar att du skapar en data uppsättning som refererar mindre än 100 sökvägar i data lager för optimala prestanda.

#### <a name="create-a-tabulardataset"></a>Skapa en TabularDataset

Använd [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-) -metoden i- `TabularDatasetFactory` klassen för att läsa filer i CSV-eller TSV-format och för att skapa en oregistrerad TabularDataset. Om du läser från flera filer aggregeras resultaten i en tabell representation. 

Följande kod hämtar arbets ytans befintliga arbets yta och önskat data lager efter namn. Och skickar sedan data lagret och fil platserna till `path` parametern för att skapa en ny TabularDataset `weather_ds` .

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 file paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]

weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

Som standard härleds kolumn data typer automatiskt när du skapar en TabularDataset. Om de härledda typerna inte matchar dina förväntningar kan du ange kolumn typer med hjälp av följande kod. Parametern `infer_column_type` kan bara användas för data uppsättningar som skapats från avgränsade filer. Du kan också [läsa mer om vilka data typer som stöds](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py).

> [!IMPORTANT] 
> Om lagrings utrymmet ligger bakom ett virtuellt nätverk eller en brand vägg stöds endast skapande av en data uppsättning via SDK. Om du vill skapa din data uppsättning måste du ta med parametrarna `validate=False` och `infer_column_types=False` i din `from_delimited_files()` metod. Detta kringgår den första verifierings kontrollen och garanterar att du kan skapa din data uppsättning från dessa säkra filer. 

```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|Tabbindex|PassengerId|Överlevt|Pclass|Namn|Sex|Ålder|SibSp|Parch|Biljett|Resa|Bagage|Har avlastat
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Falskt|3|Braund, Mr. Owen Harris|man|22,0|1|0|A/5 21171|7,2500||S
1|2|Sant|1|Cumings, fru. John Bradley (Florence Briggs to...|kvinna|38,0|1|0|PC 17599|71,2833|C85|C
2|3|Sant|3|Heikkinen, saknar. Laina|kvinna|26,0|0|0|STON/O2. 3101282|7,9250||S

Om du vill skapa en data uppsättning från en i minnet Pandas dataframe skriver du data till en lokal fil, t. ex. en CSV-fil, och skapar din data uppsättning från den filen. Följande kod visar det här arbets flödet.

```python
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)
upload the local file to a datastore on the cloud
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required
from azureml.core import Workspace, Dataset

subscription_id = 'xxxxxxxxxxxxxxxxxxxxx'
resource_group = 'xxxxxx'
workspace_name = 'xxxxxxxxxxxxxxxx'

workspace = Workspace(subscription_id, resource_group, workspace_name)

# get the datastore to upload prepared data
datastore = workspace.get_default_datastore()

# upload the local file from src_dir to the target_path in datastore
datastore.upload(src_dir='data', target_path='data')
# create a dataset referencing the cloud location
dataset = Dataset.Tabular.from_delimited_files(datastore.path('data/prepared.csv'))
```

Använd- [`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-) metoden i- `TabularDatasetFactory` klassen för att läsa från Azure SQL Database:

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore. Take note of double parenthesis.
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

I TabularDatasets kan du ange en tidstämpel från en kolumn i data eller från där Sök vägs data lagras för att aktivera en tids serie egenskap. Den här specifikationen möjliggör enkel och effektiv filtrering med tiden.

Använd [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) metoden i `TabularDataset` klassen för att ange tidsstämpel-kolumnen och aktivera filtrering efter tid. Mer information finns i [tabell Time Series-relaterad API-demo med NOAA väderleks data](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb).

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

Använd [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) metoden i `FileDatasetFactory` klassen för att läsa in filer i valfritt format och för att skapa en oregistrerad FileDataset. Om lagrings platsen ligger bakom ett virtuellt nätverk eller en brand vägg anger du parametern `validate=False` i din `from_files()` metod. Detta kringgår det första verifierings steget och garanterar att du kan skapa din data uppsättning från dessa säkra filer.

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```

#### <a name="on-the-web"></a>På webben 
Följande steg och animering visar hur du skapar en data uppsättning i Azure Machine Learning Studio https://ml.azure.com .

![Skapa en data uppsättning med användar gränssnittet](./media/how-to-create-register-datasets/create-dataset-ui.gif)

Så här skapar du en data uppsättning i Studio:
1. Logga in på https://ml.azure.com .
1. Välj **data uppsättningar** i avsnittet **till gångar** i det vänstra fönstret. 
1. Välj **skapa data uppsättning** för att välja källa för din data uppsättning. Källan kan vara lokala filer, ett data lager eller offentliga URL: er.
1. Välj **tabell** eller **fil** för data uppsättnings typ.
1. Välj **Nästa** för att öppna formuläret **data lager och fil markering** . I det här formuläret väljer du var du vill behålla din data uppsättning när du har skapat den, samt hur du väljer vilka datafiler som ska användas för data uppsättningen. 
    1. Aktivera hoppa över verifiering om dina data finns i ett virtuellt nätverk. Läs mer om [isolering av virtuella nätverk och sekretess](how-to-enable-virtual-network.md#machine-learning-studio).
1. Välj **Nästa** för att fylla i **inställningarna och för hands versionen** och **schema** formulären. de fylls i intelligent utifrån filtypen och du kan konfigurera data uppsättningen ytterligare innan du skapar den här typen av formulär. 
1. Klicka på **Nästa** för att granska formuläret **Bekräfta Detaljer** . Kontrol lera dina val och skapa en valfri data profil för din data uppsättning. Läs mer om [data profilering](how-to-use-automated-ml-for-ml-models.md#profile). 
1. Välj **skapa** för att slutföra skapandet av data uppsättningen.

## <a name="register-datasets"></a>Registrera data uppsättningar

För att slutföra skapandet av processen registrerar du dina data uppsättningar med en arbets yta. Använd [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) metoden för att registrera data uppsättningar med din arbets yta för att dela dem med andra och återanvända dem över experiment på din arbets yta:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

> [!Note]
> Data uppsättningar som skapats med Azure Machine Learning Studio registreras automatiskt på arbets ytan.

## <a name="create-datasets-with-azure-open-datasets"></a>Skapa data uppsättningar med Azure Open-datauppsättningar

[Azure Open-datauppsättningar](https://azure.microsoft.com/services/open-datasets/) är granskade offentliga data uppsättningar som du kan använda för att lägga till scenario-/regionsspecifika funktioner till maskin inlärnings lösningar för mer exakta modeller. Data uppsättningar omfattar data från offentliga domäner för väder, räkning, helger, offentlig säkerhet och plats som hjälper dig att träna maskin inlärnings modeller och utöka förutsägelse lösningar. Öppna data uppsättningar finns i molnet på Microsoft Azure och ingår i både SDK-och arbets ytans användar gränssnitt.

### <a name="use-the-sdk"></a>Använd SDK: n

Om du vill skapa data uppsättningar med Azure Open data uppsättningar från SDK kontrollerar du att du har installerat paketet med `pip install azureml-opendatasets` . Varje diskret data uppsättning representeras av sin egen klass i SDK och vissa klasser är tillgängliga som antingen en `TabularDataset` , `FileDataset` eller båda. En fullständig lista över klasser finns i [referens dokumentationen](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) .

Du kan hämta vissa klasser som antingen en `TabularDataset` eller `FileDataset` , vilket gör att du kan manipulera och/eller ladda ned filerna direkt. Andra klasser kan **bara** hämta en data uppsättning med hjälp av en `get_tabular_dataset()` eller- `get_file_dataset()` funktion. I följande kod exempel visas några exempel på dessa typer av klasser.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

När du registrerar en data uppsättning som skapats från öppna data uppsättningar hämtas inga data direkt, men data kommer att kommas åt senare när de begärs (under utbildning, till exempel) från en central lagrings plats.

### <a name="use-the-ui"></a>Använd användar gränssnittet

Du kan också skapa data uppsättningar från öppna data uppsättnings klasser via användar gränssnittet. I arbets ytan väljer du fliken **data uppsättningar** under **till gångar**. Välj **från öppna data uppsättningar**på list menyn **skapa data uppsättning** .

![Öppna data uppsättning med användar gränssnittet](./media/how-to-create-register-datasets/open-datasets-1.png)

Välj en data uppsättning genom att välja dess panel. (Du kan välja att filtrera med hjälp av Sök fältet.) Välj **Nästa**.

![Välj data uppsättning](./media/how-to-create-register-datasets/open-datasets-2.png)

Välj ett namn under vilket du vill registrera data uppsättningen och filtrera sedan data genom att använda de tillgängliga filtren. I det här fallet filtrerar du tids perioden till ett år och lands koden för den offentliga helgdags data uppsättningen till endast USA. Välj **Skapa**.

![Ange data uppsättnings parametrar och skapa data uppsättning](./media/how-to-create-register-datasets/open-datasets-3.png)

Data uppsättningen är nu tillgänglig i din arbets yta under **data uppsättningar**. Du kan använda den på samma sätt som andra data uppsättningar som du har skapat.

## <a name="version-datasets"></a>Versions data uppsättningar

Du kan registrera en ny data uppsättning med samma namn genom att skapa en ny version. En data uppsättnings version är ett sätt att lägga till en data uppsättnings tillstånd så att du kan använda en angiven version av data uppsättningen för experimentering eller framtida åter givning. Läs mer om [data uppsättnings versioner](how-to-version-track-datasets.md).
```Python
# create a TabularDataset from Titanic training data
web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
             'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```

## <a name="access-datasets-in-your-script"></a>Få åtkomst till data uppsättningar i skriptet

Registrerade data uppsättningar kan nås både lokalt och via fjärr anslutning på beräknings kluster som Azure Machine Learning Compute. Använd följande kod för att komma åt din arbets yta och registrerad data uppsättning efter namn för att få åtkomst till din registrerade data uppsättning över experiment. Som standard [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) returnerar metoden i `Dataset` klassen den senaste versionen av data uppsättningen som är registrerad på arbets ytan.

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

## <a name="access-datasets-in-a-virtual-network"></a>Åtkomst till data uppsättningar i ett virtuellt nätverk

Om din arbets yta finns i ett virtuellt nätverk måste du konfigurera data uppsättningen för att hoppa över verifieringen. Mer information om hur du använder data lager och data uppsättningar i ett virtuellt nätverk finns i [nätverks isolering under träning &s härledning med privata virtuella nätverk](how-to-enable-virtual-network.md#use-datastores-and-datasets).

## <a name="next-steps"></a>Nästa steg

* Lär dig [hur du tränar med data uppsättningar](how-to-train-with-datasets.md).
* Använd automatisk maskin inlärning för att [träna med TabularDatasets](https://aka.ms/automl-dataset).
* Fler exempel på data uppsättnings utbildning finns i [exempel antecknings böckerna](https://aka.ms/dataset-tutorial).
