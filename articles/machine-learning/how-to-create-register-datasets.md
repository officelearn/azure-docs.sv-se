---
title: Skapa Azure Machine Learning-datauppsättningar för att komma åt data
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar Azure Machine Learning-datauppsättningar för att komma åt dina data för maskininlärningsexperimentkörningar.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 02/10/2020
ms.openlocfilehash: f02046d1e2ee558ca4ea4472a03fddb5d0a6a16f
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549330"
---
# <a name="create-azure-machine-learning-datasets"></a>Skapa Azure Machine Learning-datauppsättningar

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du skapar Azure Machine Learning-datauppsättningar för att komma åt data för dina lokala experiment eller fjärrexperiment.

Med Azure Machine Learning-datauppsättningar kan du:

* Spara en enda kopia av data i lagringsutrymmet, refererad av datauppsättningar.

* Få sömlös åtkomst till data under modellträning utan att behöva oroa dig för anslutningssträngar eller datasökvägar.

* Dela data och samarbeta med andra användare.

## <a name="prerequisites"></a>Krav
' För att skapa och arbeta med datauppsättningar behöver du:

* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

* En [Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

* [Azure Machine Learning SDK för Python installerat](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), som innehåller azureml-dataset-paketet.

> [!NOTE]
> Vissa datauppsättningsklasser har beroenden på [azureml-dataprep-paketet.](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) För Linux-användare stöds dessa klasser endast på följande distributioner: Red Hat Enterprise Linux, Ubuntu, Fedora och CentOS.

## <a name="compute-size-guidance"></a>Vägledning för beräkningsstorlek

När du skapar en datauppsättning granska din beräkningsbehandlingskraft och storleken på dina data i minnet. Storleken på dina data i lagring är inte samma som storleken på data i en dataram. Data i CSV-filer kan till exempel expandera upp till 10 x i en dataram, så en CSV-fil på 1 GB kan bli 10 GB i en dataram. 

Den viktigaste faktorn är hur stor datauppsättningen är i minnet, d.v.s. som en dataram. Vi rekommenderar att din beräkningsstorlek och processorkraft innehåller 2x storleken på RAM. Så om din dataram är 10GB, du vill ha ett beräkningsmål med 20 + GB RAM för att säkerställa att dataramen kan bekväm passform i minnet och bearbetas. Om dina data komprimeras kan de expandera ytterligare. 20 GB relativt glesa data som lagras i komprimerat parkettformat kan expandera till ~800 GB i minnet. Eftersom Parquet filer lagra data i en columnar format, om du bara behöver hälften av kolumnerna, då du bara behöver ladda ~ 400 GB i minnet.
 
Om du använder Pandas, det finns ingen anledning att ha mer än 1 vCPU eftersom det är allt den kommer att använda. Du kan enkelt parallellisera till många virtuella processorer på en enda Azure Machine Learning-beräkningsinstans/nod via Modin och `import pandas as pd` `import modin.pandas as pd`Dask/Ray och skala ut till ett stort kluster om det behövs, genom att helt enkelt byta till . 
 
Om du inte kan få en tillräckligt stor virtuell för data, har du två alternativ: använd ett ramverk som Spark eller Dask för att utföra behandlingen på data på minne, dvs dataramen laddas i RAM-partitionen av partition och bearbetas, med slutresultatet samlas in i slutet. Om detta är för långsamt kan du skala ut Spark eller Dask till ett kluster som fortfarande kan användas interaktivt. 

## <a name="dataset-types"></a>Datamängdstyper

Det finns två datauppsättningstyper, baserat på hur användarna använder dem i utbildning:

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) representerar data i tabellformat genom att tolka den angivna filen eller listan med filer. Detta ger dig möjlighet att materialisera data till en Pandas eller Spark DataFrame. Du kan `TabularDataset` skapa ett objekt från CSV-, .tsv-, .parkett-, .jsonl-filer och från SQL-frågeresultat. En fullständig lista finns i [klassen TabularDatasetFactory](https://aka.ms/tabulardataset-api-reference).

* Klassen [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) refererar till en eller flera filer i dina datalager eller offentliga webbadresser. Med den här metoden kan du hämta eller montera filerna till din beräkning som ett FileDataset-objekt. Filerna kan vara i alla format, vilket möjliggör ett bredare utbud av maskininlärningsscenarier, inklusive djupinlärning.

Mer information om kommande API-ändringar finns i [meddelande om ändring av API-ändring av datauppsättning.](https://aka.ms/tabular-dataset)

## <a name="create-datasets"></a>Skapa datauppsättningar

Genom att skapa en datauppsättning skapar du en referens till datakällans plats, tillsammans med en kopia av dess metadata. Eftersom data finns kvar på den befintliga platsen medför du ingen extra lagringskostnad. Du kan `TabularDataset` skapa `FileDataset` både och datauppsättningar med https://ml.azure.comhjälp av Python SDK eller på .

För att data ska vara tillgängliga för Azure Machine Learning måste datauppsättningar skapas från sökvägar i [Azure-datalager](how-to-access-data.md) eller offentliga webb-URL:er. 

### <a name="use-the-sdk"></a>Använd SDK

Så här skapar du datauppsättningar från ett [Azure-datalager](how-to-access-data.md) med hjälp av Python SDK:

1. Kontrollera att `contributor` du `owner` har eller har åtkomst till det registrerade Azure-datalagret.

2. Skapa datauppsättningen genom att referera till sökvägar i datalagret.
> [!Note]
> Du kan skapa en datauppsättning från flera sökvägar i flera datacenter. Det finns ingen hård gräns för hur många filer eller datastorlek som du kan skapa en datauppsättning från. För varje datasökväg skickas dock några begäranden till lagringstjänsten för att kontrollera om den pekar på en fil eller en mapp. Denna overhead kan leda till försämrade prestanda eller fel. En datauppsättning som refererar till en mapp med 1000 filer inuti anses referera till en datasökväg. Vi rekommenderar att du skapar datauppsättning som refererar till mindre än 100 sökvägar i datalager för optimal prestanda.

#### <a name="create-a-tabulardataset"></a>Skapa en tabelldatauppsättning

Använd [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-) metoden i `TabularDatasetFactory` klassen för att läsa filer i CSV- eller .tsv-format och skapa en oregistrerad tabelluppsättning. Om du läser från flera filer sammanställs resultaten till en tabellrepresentation. 

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

När du skapar en tabelldatauppsättning härleds som standard kolumndatatyper automatiskt. Om de härledda typerna inte stämmer överens med dina förväntningar kan du ange kolumntyper med hjälp av följande kod. Parametern `infer_column_type` är endast tillämplig för datauppsättningar som skapats från avgränsade filer. Du kan också [läsa mer om datatyper som stöds](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py).

> [!IMPORTANT] 
> Om lagringsutrymmet ligger bakom ett virtuellt nätverk eller en brandvägg stöds endast skapandet av en datauppsättning via SDK. Om du vill skapa datauppsättningen måste `validate=False` `infer_column_types=False` du `from_delimited_files()` inkludera parametrarna och metoden. Detta kringgår den första valideringskontrollen och säkerställer att du kan skapa din datauppsättning från dessa säkra filer. 

```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |PassengerId (Passagerare)|Överlevde|Pclass (Pclass)|Namn|Sex|Ålder|SibSp (olikartade)|Parch (perch)|Biljett|Biljettpris|Stuga|Inlett
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, Mr Owen Harris|man|22.0|1|0|A/5 21171|7.2500||S
1|2|True|1|Cumings, Fru John Bradley (Florence Briggs Th...|kvinna|38.0|1|0|PC 17599|71.2833|C85 (på andra sätt)|C
2|3|True|3|Heikkinen, fröken. Laina|kvinna|26.0|0|0|STON/O2. 3101282|7.9250||S


Om du vill skapa en datauppsättning från en dataram för i minnet pandas skriver du data till en lokal fil, till exempel en csv, och skapar datauppsättningen från filen. Följande kod visar det här arbetsflödet.

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

Använd [`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-) metoden för `TabularDatasetFactory` klassen för att läsa från Azure SQL Database:

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

I TabularDatasets kan du ange en tidsstämpel från en kolumn i data eller varifrån sökvägsmönsterdata lagras för att aktivera en tidsseriedrag. Denna specifikation möjliggör enkel och effektiv filtrering med tiden.

Använd [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) metoden på`TabularDataset` klassen för att ange din tidsstämpelkolumn och aktivera filtrering efter tid. Mer information finns i [Tabelltidsserierelaterad API-demo med NOAA-väderdata](https://aka.ms/azureml-tsd-notebook).

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

Använd [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) metoden på `FileDatasetFactory` klassen för att läsa in filer i valfritt format och skapa en oregistrerad FileDataset. Om lagringsutrymmet ligger bakom ett virtuellt nätverk `validate =False` eller `from_files()` en virtuell brandvägg anger du parametern i metoden. Detta kringgår det första valideringssteget och säkerställer att du kan skapa datauppsättningen från dessa säkra filer.

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
Följande steg och animeringar visar hur du skapar en https://ml.azure.comdatauppsättning i Azure Machine Learning studio, .

![Skapa en datauppsättning med användargränssnittet](./media/how-to-create-register-datasets/create-dataset-ui.gif)

Så här skapar du en datauppsättning i studion:
1. Logga in https://ml.azure.compå .
1. Välj **Datauppsättningar** i avsnittet **Tillgångar** i den vänstra rutan. 
1. Välj **Skapa datauppsättning** om du vill välja datauppsättningens källa. Den här källan kan vara lokala filer, ett datalager eller offentliga webbadresser.
1. Välj **Tabell eller** **Arkiv** för datauppsättningstyp.
1. Välj **Nästa** om du vill öppna formuläret **Datastore och filval.** I det här formuläret väljer du var du vill behålla datauppsättningen när du har skapat den, samt vilka datafiler som ska användas för datauppsättningen. 
1. Välj **Nästa** om du vill fylla i formulären **Inställningar och förhandsgranskning** och **schema.** De är intelligent ifyllda baserat på filtyp och du kan ytterligare konfigurera datauppsättningen innan du skapar dessa formulär. 
1. Välj **Nästa** om du vill granska formuläret **Bekräfta information.** Kontrollera dina val och skapa en valfri dataprofil för din datauppsättning. Läs mer om [dataprofilering](how-to-use-automated-ml-for-ml-models.md#profile). 
1. Välj **Skapa** för att slutföra skapandet av datauppsättningen.

## <a name="register-datasets"></a>Registrera datauppsättningar

För att slutföra skapandeprocessen registrerar du dina datauppsättningar med en arbetsyta. Använd [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) metoden för att registrera datauppsättningar med arbetsytan för att dela dem med andra och återanvända dem i olika experiment:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

> [!Note]
> Datauppsättningar som skapas via Azure Machine Learning studio registreras automatiskt på arbetsytan.

## <a name="create-datasets-with-azure-open-datasets"></a>Skapa datauppsättningar med Azure Open Datasets

[Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) är kurerade offentliga datauppsättningar som du kan använda för att lägga till scenariospecifika funktioner i maskininlärningslösningar för mer exakta modeller. Datauppsättningar innehåller offentliga data för väder, folkräkning, helgdagar, allmän säkerhet och plats som hjälper dig att träna maskininlärningsmodeller och berika prediktiva lösningar. Öppna datauppsättningar finns i molnet på Microsoft Azure och ingår i både SDK och arbetsytans användargränssnitt.

### <a name="use-the-sdk"></a>Använd SDK

Om du vill skapa datauppsättningar med Azure Open Datasets från SDK kontrollerar du att du har installerat paketet med `pip install azureml-opendatasets`. Varje diskret datauppsättning representeras av sin egen klass i SDK och vissa `TabularDataset` `FileDataset`klasser är tillgängliga som antingen en , eller båda. Se [referensdokumentationen](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) för en fullständig lista över klasser.

Du kan hämta vissa `TabularDataset` klasser `FileDataset`som antingen en eller , vilket gör att du kan manipulera och / eller ladda ner filerna direkt. Andra klasser kan **bara** hämta en datauppsättning med hjälp av en av `get_tabular_dataset()` eller `get_file_dataset()` funktioner. Följande kodexempel visar några exempel på dessa typer av klasser.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY return TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

När du registrerar en datauppsättning som skapats från Öppna datauppsättningar hämtas inga data omedelbart, men data kommer att nås senare när så önskas (till exempel under utbildning) från en central lagringsplats.

### <a name="use-the-ui"></a>Använda användargränssnittet

Du kan också skapa datauppsättningar från open datasets-klasser via användargränssnittet. På arbetsytan väljer du fliken **Datauppsättningar** under **Tillgångar**. På listrutan **Skapa datauppsättning** väljer du **Från Öppna datauppsättningar**.

![Öppna datauppsättning med användargränssnittet](./media/how-to-create-register-datasets/open-datasets-1.png)

Välj en datauppsättning genom att markera dess panel. (Du kan välja att filtrera med hjälp av sökfältet.) Välj **Nästa**.

![Välj datauppsättning](./media/how-to-create-register-datasets/open-datasets-2.png)

Välj ett namn som datauppsättningen ska registreras under och filtrera data automatiskt med hjälp av tillgängliga filter. I det här fallet filtrerar du tidsperioden till ett år och landskoden till endast USA för datauppsättningen helgdagar. Välj **Skapa**.

![Ange datauppsättningsparamer och skapa datauppsättning](./media/how-to-create-register-datasets/open-datasets-3.png)

Datauppsättningen är nu tillgänglig på arbetsytan under **Datauppsättningar**. Du kan använda den på samma sätt som andra datauppsättningar som du har skapat.

## <a name="version-datasets"></a>Versionsdatauppsättningar

Du kan registrera en ny datauppsättning under samma namn genom att skapa en ny version. En datauppsättningsversion är ett sätt att bokmärka tillståndet för dina data så att du kan använda en viss version av datauppsättningen för experiment eller framtida reproduktion. Läs mer om [datauppsättningsversioner](how-to-version-track-datasets.md).
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

## <a name="access-datasets-in-your-script"></a>Komma åt datauppsättningar i skriptet

Registrerade datauppsättningar är tillgängliga både lokalt och på distans på beräkningskluster som Azure Machine Learning-beräkningen. Om du vill komma åt din registrerade datauppsättning över experiment använder du följande kod för att komma åt din arbetsyta och registrerade datauppsättning efter namn. Som standard [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) returnerar `Dataset` metoden i klassen den senaste versionen av datauppsättningen som har registrerats med arbetsytan.

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

* Läs om hur du [tränar med datauppsättningar](how-to-train-with-datasets.md).
* Använd automatiserad maskininlärning för att [träna med TabularDatasets](https://aka.ms/automl-dataset).
* Fler exempel på datauppsättningsträning finns i [exempelböckerna](https://aka.ms/dataset-tutorial).
