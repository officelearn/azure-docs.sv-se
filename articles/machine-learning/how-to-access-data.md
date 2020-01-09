---
title: Få åtkomst till data i Azure Storage Services
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder data lager för att säkert ansluta till Azure Storage-tjänster under utbildning med Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: ylxiong
author: YLXiong1125
ms.reviewer: nibaccam
ms.date: 12/10/2019
ms.custom: seodec18
ms.openlocfilehash: ac6ef6341013ca13d5a9f27be8897365c1c2155d
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2019
ms.locfileid: "75540950"
---
# <a name="access-data-in-azure-storage-services"></a>Få åtkomst till data i Azure Storage-tjänster
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du enkelt kan komma åt dina data i Azure Storage Services via Azure Machine Learning data lager. Data lager används för att lagra anslutnings information, t. ex. prenumerations-ID och token-auktorisering. När du använder data lager kan du komma åt din lagring utan att behöva hårdkoda anslutnings information i dina skript. 

Du kan skapa data lager från [dessa Azure Storage-lösningar](#matrix). För lagrings lösningar som inte stöds och för att spara data Utgångs kostnader under Machine Learning-experiment, rekommenderar vi att du [flyttar dina data](#move) till Azure Storage lösningar som stöds. 

## <a name="prerequisites"></a>Krav
Du behöver:
- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

- Ett Azure Storage-konto med en [Azure Blob-behållare](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) eller [Azure-filresurs](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- [Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)eller åtkomst till [Azure Machine Learning Studio](https://ml.azure.com/).

- En Azure Machine Learning-arbetsyta.
  
  [Skapa antingen en Azure Machine Learning arbets yta](how-to-manage-workspace.md) eller Använd en befintlig via python SDK:

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Skapa och registrera data lager

När du registrerar en Azure Storage-lösning som ett data lager skapar du automatiskt detta data lager i en angiven arbets yta. Du kan skapa och registrera data lager på en arbets yta med hjälp av python SDK eller Azure Machine Learning Studio.

### <a name="python-sdk"></a>Python SDK

Alla register metoder finns i [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) -klassen och har formuläret `register_azure_*`.

Du hittar den information som du behöver för att fylla i `register()`-metoden med hjälp av [Azure Portal](https://portal.azure.com):

1. Välj **lagrings konton** i den vänstra rutan och välj det lagrings konto som du vill registrera. 
2. Information som konto namn, behållare och fil resurs namn finns på sidan **Översikt** . Information om autentisering, som konto nyckel eller SAS-token, finns i **åtkomst nycklar** i fönstret **Inställningar** . 

> [!IMPORTANT]
> Om ditt lagrings konto finns i ett virtuellt nätverk stöds bara skapandet av ett Azure Blob-datalager. Om du vill ge din arbets yta åtkomst till ditt lagrings konto anger du parametern `grant_workspace_access` `True`.

I följande exempel visas hur du registrerar en Azure Blob-behållare, en Azure-filresurs och Azure SQL-data som ett data lager.

#### <a name="blob-container"></a>Blobcontainer

Använd [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)om du vill registrera en Azure Blob-behållare som ett data lager.

Följande kod skapar och registrerar `blob_datastore_name` data lagret på arbets ytan `ws`. Detta data lager har åtkomst till `my-container-name` BLOB-behållaren på `my-account-name` lagrings konto med hjälp av den angivna konto nyckeln.

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```

#### <a name="file-share"></a>Filresurs

Använd [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)om du vill registrera en Azure-filresurs som ett data lager. 

Följande kod skapar och registrerar `file_datastore_name` data lagret på arbets ytan `ws`. Detta data lager har åtkomst till `my-fileshare-name` fil resurs på `my-account-name` lagrings konto med hjälp av den angivna konto nyckeln.

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                 datastore_name=file_datastore_name, 
                                                 file_share_name=file_share_name, 
                                                 account_name=account_name,
                                                 account_key=account_key)
```

#### <a name="sql-data"></a>SQL-data

För ett Azure SQL-datalager använder du [register_azure_sql_database ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-sql-database-workspace--datastore-name--server-name--database-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--endpoint-none--overwrite-false--username-none--password-none-) för att registrera ett data lager för autentiseringsuppgifter som är anslutet till en Azure SQL-databas med SQL-autentisering eller tjänst huvud namn. 

Registrera dig genom SQL-autentisering:

```python
sql_datastore_name="azsqlsdksql"
server_name=os.getenv("SQL_SERVERNAME", "<my-server-name>") # Name of the Azure SQL server
database_name=os.getenv("SQL_DATBASENAME", "<my-database-name>") # Name of the Azure SQL database
username=os.getenv("SQL_USER_NAME", "<my-sql-user-name>") # Username of the database user to access the database
password=os.getenv("SQL_USER_PASSWORD", "<my-sql-user-password>") # Password of the database user to access the database

sql_datastore = Datastore.register_azure_sql_database(workspace=ws,
                                                  datastore_name=sql_datastore_name,
                                                  server_name=server_name,
                                                  database_name=database_name,
                                                  username=username,
                                                  password=password)

```

Registrera via ett huvud namn för tjänsten:

```python 
sql_datastore_name="azsqlsdksp"
server_name=os.getenv("SQL_SERVERNAME", "<my-server-name>") # Name of the SQL server
database_name=os.getenv("SQL_DATBASENAME", "<my-database-name>") # Name of the SQL database
client_id=os.getenv("SQL_CLIENTNAME", "<my-client-id>") # Client ID of the service principal with permissions to access the database
client_secret=os.getenv("SQL_CLIENTSECRET", "<my-client-secret>") # Secret of the service principal
tenant_id=os.getenv("SQL_TENANTID", "<my-tenant-id>") # Tenant ID of the service principal

sql_datastore = Datastore.register_azure_sql_database(workspace=ws,
                                                      datastore_name=sql_datastore_name,
                                                      server_name=server_name,
                                                      database_name=database_name,
                                                      client_id=client_id,
                                                      client_secret=client_secret,
                                                      tenant_id=tenant_id)
```

#### <a name="storage-guidance"></a>Minnesriktlinjer

Vi rekommenderar en Azure Blob-behållare. Både standard-och Premium lagring är tillgängliga för blobbar. Även om Premium Storage är dyrare, kan snabbare data flödes hastigheter förbättra hastigheten på din utbildning, särskilt om du tränar mot en stor data uppsättning. Information om kostnaden för lagrings konton finns i [pris Kalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning-studio 

Skapa ett nytt data lager med några steg i Azure Machine Learning Studio:

1. Logga in på [Azure Machine Learning Studio](https://ml.azure.com/).
1. Välj **data lager** i det vänstra fönstret under **Hantera**.
1. Välj **+ nytt data lager**.
1. Fyll i formuläret för ett nytt data lager. Formuläret uppdateras intelligent baserat på dina val för Azure Storage typ och autentiseringstyp.
  
Du kan hitta den information som du behöver för att fylla i formuläret på [Azure Portal](https://portal.azure.com). Välj **lagrings konton** i den vänstra rutan och välj det lagrings konto som du vill registrera. **Översikts** sidan innehåller information som konto namn, behållare och fil resurs namn. För verifierings objekt, som konto nyckel eller SAS-token, går du till **konto nycklar** i fönstret **Inställningar** .

Följande exempel visar hur formuläret ser ut när du skapar ett Azure Blob-datalager: 
    
![Formulär för ett nytt data lager](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Hämta data lager från din arbets yta

Om du vill hämta ett särskilt data lager som registrerats i den aktuella arbets ytan använder du metoden [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) static i `Datastore`-klassen:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Om du vill hämta listan över data lager som registrerats med en specifik arbets yta kan du använda egenskapen [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) på ett objekt i arbets ytan:

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

När du skapar en arbets yta registreras en Azure Blob-behållare och en Azure-filresurs automatiskt till arbets ytan. De heter `workspaceblobstore` respektive `workspacefilestore`. De lagrar anslutnings informationen för BLOB-behållaren och fil resursen som är etablerad i det lagrings konto som är kopplat till arbets ytan. `workspaceblobstore` containern anges som standard data lager.

Använd den här raden för att hämta arbets ytans standard data lager:

```Python
datastore = ws.get_default_datastore()
```

Om du vill definiera ett annat standard data lager för den aktuella arbets ytan använder du metoden [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) på objektet arbets yta:

```Python
# Define the default datastore for the current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>Ladda upp och hämta data

De [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) -och [`download()`s](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) metoderna som beskrivs i följande exempel är speciella för, och är identiska för, [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) -och [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) -klasserna.

### <a name="upload"></a>Ladda upp

Ladda upp antingen en katalog eller enskilda filer till data lagret med hjälp av python SDK:

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

Parametern `target_path` anger platsen i fil resursen (eller BLOB-behållaren) som ska överföras. Standardvärdet är `None`, så data överförs till roten. Om `overwrite=True`skrivs alla befintliga data på `target_path` över.

Du kan också ladda upp en lista med enskilda filer till data lagret via `upload_files()`-metoden.

### <a name="download"></a>Ladda ned

Hämta data från ett data lager till ditt lokala fil system:

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

Parametern `target_path` är platsen för den lokala katalog där data ska hämtas till. Om du vill ange en sökväg till mappen i filresursen (eller blob-behållare) för att hämta, ange sökvägen till `prefix`. Om `prefix` är `None`kommer allt innehåll i din fil resurs (eller BLOB-behållare) att laddas ned.

<a name="train"></a>
## <a name="access-your-data-during-training"></a>Få åtkomst till dina data under utbildningen

> [!IMPORTANT]
> Vi rekommenderar nu att använda [Azure Machine Learning data uppsättningar](how-to-create-register-datasets.md) för att komma åt dina data i utbildningen. Data uppsättningar tillhandahåller funktioner som läser in tabell data i en Pandas-eller Spark-DataFrame. Data uppsättningar ger också möjlighet att ladda ned eller montera filer i alla format från Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database och Azure Database for PostgreSQL. [Lär dig mer om hur du tränar med data uppsättningar](how-to-train-with-datasets.md).

I följande tabell visas de metoder som talar om för beräknings målet hur data lagringen ska användas vid körning: 

Tvåvägsupprättade|Metod|Beskrivning|
----|-----|--------
Montera| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| Används för att montera data lagret på Compute-målet. När data lagret är monterat görs alla filer i ditt data lager tillgängliga för ditt beräknings mål.
Ladda ned|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|Använd för att ladda ned innehållet i ditt data lager till den plats som anges av `path_on_compute`. <br><br> Den här nedladdningen sker före körningen.
Ladda upp|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| Använd för att ladda upp en fil från den plats som anges av `path_on_compute` till ditt data lager. <br><br> Överföringen sker efter körningen.

Om du vill referera till en mapp eller fil i ditt data lager och göra den tillgänglig på beräknings målet, använder du [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) -metoden för data lager:

```Python
# To mount the full contents in your storage to the compute target
datastore.as_mount()

# To download the contents of only the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> Alla angivna `datastore`-eller `datastore.path`-objekt matchar ett miljö variabel namn för formatet `"$AZUREML_DATAREFERENCE_XXXX"`. Värdet för det här namnet representerar monterings-/hämtnings Sök vägen på Compute-målet. Sökvägen till data lagret i Compute-målet kanske inte är samma som körnings Sök vägen för utbildnings skriptet.

### <a name="examples"></a>Exempel 

Vi rekommenderar att du använder [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) klassen för att få åtkomst till data under utbildningen. 

Variabeln `script_params` är en ord lista som innehåller parametrar som `entry_script`. Använd den för att skicka in ett data lager och beskriva hur data görs tillgängliga på beräknings målet. Läs mer i [självstudierna från slut punkt till slut punkt](tutorial-train-models-with-aml.md).

```Python
from azureml.train.estimator import Estimator

# Notice that '/' is in front, which indicates the absolute path
script_params = {
    '--data_dir': datastore.path('/bar').as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

Du kan också skicka en lista över data lager till `Estimator` konstruktorns `inputs`-parameter för att montera eller kopiera data till/från ditt data lager. Det här kod exemplet:
* Laddar ned allt innehåll i `datastore1` till beräknings målet innan det `train.py` utbildnings skriptet körs.
* Laddar ned mappen `'./foo'` i `datastore2` till Compute-målet innan `train.py` körs.
* Överför `'./bar.pkl'`-filen från Compute-målet till `datastore3` när skriptet har körts.

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```
Om du föredrar att använda ett `RunConfig` objekt för utbildning måste du konfigurera ett [`DataReference`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) -objekt. 

Följande kod visar hur du arbetar med ett `DataReference`-objekt i en uppskattnings pipeline. Det fullständiga exemplet finns i [den här antecknings boken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-estimatorstep.ipynb).

```Python
from azureml.core import Datastore
from azureml.data.data_reference import DataReference
from azureml.pipeline.core import PipelineData

def_blob_store = Datastore(ws, "workspaceblobstore")

input_data = DataReference(
       datastore=def_blob_store,
       data_reference_name="input_data",
       path_on_datastore="20newsgroups/20news.pkl")

output = PipelineData("output", datastore=def_blob_store)
```
<a name="matrix"></a>

### <a name="compute-and-datastore-matrix"></a>Beräknings-och data lager mat ris

Data lager har för närvarande stöd för lagring av anslutnings information till de lagrings tjänster som anges i följande matris. I den här matrisen visas tillgängliga funktioner för data åtkomst för de olika beräknings målen och data bearbetnings scenarierna. [Läs mer om beräknings målen för Azure Machine Learning](how-to-set-up-training-targets.md#compute-targets-for-training).

|Databearbetning|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Lokal|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|Gäller inte         |Gäller inte                                                                         |
| Azure Machine Learning-beräkning |[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-) [Machine Learning pipelines](concept-ml-pipelines.md)|[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-) [Machine Learning pipelines](concept-ml-pipelines.md)|Gäller inte         |Gäller inte                                                                         |
| Virtuella maskiner               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |Gäller inte         |Gäller inte                                                                         |
| Azure HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |Gäller inte         |Gäller inte                                                                         |
| Dataöverföring                  |[Machine Learning pipelines](concept-ml-pipelines.md)                                               |Gäller inte                                           |[Machine Learning pipelines](concept-ml-pipelines.md)            |[Machine Learning pipelines](concept-ml-pipelines.md)                                                                            |
| Azure Databricks                     |[Machine Learning pipelines](concept-ml-pipelines.md)                                              |Gäller inte                                           |[Machine Learning pipelines](concept-ml-pipelines.md)             |Gäller inte                                                                         |
| Azure Batch                    |[Machine Learning pipelines](concept-ml-pipelines.md)                                               |Gäller inte                                           |Gäller inte         |Gäller inte                                                                         |
| Azure Data Lake Analytics       |Gäller inte                                           |Gäller inte                                           |[Machine Learning pipelines](concept-ml-pipelines.md)             |Gäller inte                                                                         |

> [!NOTE]
> Det kan finnas scenarier där mycket iterativa stora data processer körs snabbare med `as_download()` i stället för `as_mount()`. Du kan validera det här experimentet.

### <a name="accessing-source-code-during-training"></a>Åtkomst till käll kod under träning

Azure Blob Storage har högre överföringshastigheter än en Azure-filresurs och kommer att skalas till ett stort antal jobb som startas parallellt. Därför rekommenderar vi att du konfigurerar dina körningar för att använda blob-lagring för överföring av källfiler.

Följande kod exempel anger i den körnings konfiguration som BLOB-datalagret ska användas för käll kods överföringar:

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Åtkomst till data under Poängsättning

Azure Machine Learning tillhandahåller flera olika sätt att använda dina modeller för att beräkna poäng. Några av dessa metoder ger inte åtkomst till data lager. Använd följande tabell för att förstå vilka metoder du kan använda för att komma åt data lager under poängsättningen:

| Metod | Åtkomst till data lager | Beskrivning |
| ----- | :-----: | ----- |
| [Batch förutsägelse](how-to-run-batch-predictions.md) | ✔ | Gör förutsägelser för stora mängder data asynkront. |
| [Webbtjänst](how-to-deploy-and-where.md) | &nbsp; | Distribuera modeller som en webb tjänst. |
| [Azure IoT Edge modul](how-to-deploy-and-where.md) | &nbsp; | Distribuera modeller till IoT Edge enheter. |

I situationer där SDK inte ger åtkomst till data lager kan du kanske skapa anpassad kod med hjälp av relevanta Azure SDK för att få åtkomst till data. Till exempel är [Azure Storage SDK för python](https://github.com/Azure/azure-storage-python) ett klient bibliotek som du kan använda för att komma åt data som lagras i blobbar eller filer.

<a name="move"></a>
## <a name="move-data-to-supported-azure-storage-solutions"></a>Flytta data till Azure Storage-lösningar som stöds

Azure Machine Learning stöder åtkomst till data från Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database och Azure Database for PostgreSQL. Om du använder lagring som inte stöds rekommenderar vi att du flyttar dina data till Azure Storage lösningar som stöds med hjälp av Azure Data Factory. Genom att flytta data till lagring som stöds kan du spara data utgående kostnader under Machine Learning-experiment. 

Azure Data Factory ger effektiv och flexibel data överföring med fler än 80 färdiga kopplingar utan extra kostnad. Dessa anslutningar omfattar Azure Data Services, lokala data källor, Amazon S3 och RedShift och Google BigQuery. [Följ steg-för-steg-guiden för att flytta dina data med hjälp av Azure Data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

## <a name="next-steps"></a>Nästa steg

* [Träna en modell](how-to-train-ml-models.md)
* [Distribuera en modell](how-to-deploy-and-where.md)
