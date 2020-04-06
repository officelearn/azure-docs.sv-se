---
title: Ansluta till Azure-lagringstjänster
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder datalager för att på ett säkert sätt ansluta till Azure-lagringstjänster under utbildning med Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: 97aa446636ea3131246a06f69f74b5868abff608
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668651"
---
# <a name="connect-to-azure-storage-services"></a>Ansluta till Azure-lagringstjänster
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln kan du läsa om hur du ansluter till Azure-lagringstjänster via Azure Machine Learning-datalager. Datalager lagrar anslutningsinformation, som ditt prenumerations-ID och tokenauktorisering i [key vault som](https://azure.microsoft.com/services/key-vault/) är associerade med arbetsytan, så att du kan komma åt lagringsutrymmet på ett säkert sätt utan att behöva koda dem hårt i skripten.

Du kan skapa datalager från [dessa Azure-lagringslösningar](#matrix). För lagringslösningar som inte stöds och för att spara datautgående kostnader under maskininlärningsexperiment rekommenderar vi att du [flyttar dina data](#move) till Azure-lagringslösningar som stöds. 

## <a name="prerequisites"></a>Krav

Du behöver:
- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

- Ett Azure-lagringskonto med en [Azure blob-behållare](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) eller [Azure-filresurs](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- [Azure Machine Learning SDK för Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)eller åtkomst till Azure Machine Learning [Studio](https://ml.azure.com/).

- En Azure Machine Learning-arbetsyta.
  
  Skapa [en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md) eller använd en befintlig via Python SDK. Importera `Workspace` klassen `Datastore` och läs in prenumerationsinformationen `config.json` från `from_config()`filen med hjälp av funktionen . Detta letar efter JSON-filen i den aktuella katalogen som standard, men `from_config(path="your/file/path")`du kan också ange en sökvägsparameter som ska peka på filen med .

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Typer av datalagringstjänster som stöds

Datalager har för närvarande stöd för lagring av anslutningsinformation till lagringstjänster som anges i följande matris.

| Typ&nbsp;av lagring | Typ&nbsp;av autentisering | [Azure&nbsp;&nbsp;Machine Learning-studio](https://ml.azure.com/) | [Azure&nbsp;Machine&nbsp;Learning&nbsp; Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [Azure&nbsp;Machine&nbsp;Learning CLI](reference-azure-machine-learning-cli.md) | [Azure&nbsp;Machine&nbsp;Learning&nbsp; Rest API](https://docs.microsoft.com/rest/api/azureml/)
---|---|---|---|---|---
[Azure&nbsp;Blob-lagring&nbsp;](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| Kontonyckel <br> SAS-token | ✓ | ✓ | ✓ |✓
[Azure-filresurs&nbsp;&nbsp;](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| Kontonyckel <br> SAS-token | ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data&nbsp;Lake&nbsp;Storage Gen 1](https://docs.microsoft.com/azure/data-lake-store/)| Tjänstens huvudnamn| ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data&nbsp;Lake&nbsp;Storage Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| Tjänstens huvudnamn| ✓ | ✓ | ✓ |✓
[Azure&nbsp;&nbsp;SQL-databas](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| SQL-autentisering <br>Tjänstens huvudnamn| ✓ | ✓ | ✓ |✓
[Azure&nbsp;PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview) | SQL-autentisering| ✓ | ✓ | ✓ |✓
[Azure-databas&nbsp;&nbsp;för&nbsp;MySQL](https://docs.microsoft.com/azure/mysql/overview) | SQL-autentisering|  | ✓* | ✓* |✓*
[Databricks&nbsp;&nbsp;filsystem](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)| Ingen autentisering | | ✓** | ✓ ** |✓** 

*MySQL stöds endast för pipeline [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). <br>
**Databricks stöds endast för pipeline [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)

### <a name="storage-guidance"></a>Minnesriktlinjer

Vi rekommenderar att du skapar ett datalager för en [Azure Blob-behållare](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Både standard- och premiumlagring är tillgängliga för blobbar. Även om premiumlagring är dyrare kan dess snabbare genomströmningshastigheter förbättra hastigheten på dina träningskörningar, särskilt om du tränar mot en stor datauppsättning. Information om kostnaden för lagringskonton finns i [Azure-priskalkylatorn](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json) bygger på Azure Blob-lagring och är utformad för stordataanalys för företag. En grundläggande del av Data Lake Storage Gen2 är tillägget av ett [hierarkiskt namnområde](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) till Blob-lagring. Det hierarkiska namnområdet ordnar objekt/filer i en hierarki av kataloger för effektiv dataåtkomst.

När du skapar en arbetsyta registreras en Azure-blob-behållare och en Azure-filresurs automatiskt på arbetsytan. De är `workspaceblobstore` namngivna och `workspacefilestore`, respektive. `workspaceblobstore`används för att lagra arbetsytartefakter och experimentloggar för maskininlärning. `workspacefilestore`används för att lagra anteckningsböcker och R-skript som auktoriserats via [beräkningsinstans](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files). Behållaren `workspaceblobstore` anges som standarddatalager.

> [!IMPORTANT]
> Azure Machine Learning designer (förhandsversion) skapar ett datalager med namnet **azureml_globaldatasets** automatiskt när du öppnar ett exempel på designerns startsida. Det här datalagret innehåller bara exempeldatauppsättningar. Använd **inte** detta datalager för konfidentiell dataåtkomst!
> ![Automatiskt skapat datalager för designerexempeldatauppsättningar](media/how-to-access-data/datastore-designer-sample.png)

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Skapa och registrera datalager

När du registrerar en Azure-lagringslösning som ett datalager skapar och registrerar du automatiskt datalagringen till en viss arbetsyta. Du kan skapa och registrera datalager till en arbetsyta med hjälp av Studio Python SDK eller Azure Machine Learning.

>[!IMPORTANT]
> Som en del av den första datalagringsprocessen verifierar Azure Machine Learning att den underliggande lagringstjänsten finns och att användaren angav huvudnamn (användarnamn, tjänsthuvudnamn eller SAS-token) har åtkomst till den lagringen. För Azure Data Lake Storage Gen 1 och 2 datacenter sker dock den [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py) [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) här valideringen senare, när dataåtkomstmetoder gillar eller anropas. 
<br><br>
När datalagring har skapats utförs den här valideringen endast för metoder som kräver åtkomst till den underliggande lagringsbehållaren, **inte** varje gång datalagerobjekt hämtas. Validering sker till exempel om du vill hämta filer från ditt datalager. men om du bara vill ändra din standard datalager, då validering inte hända.

### <a name="python-sdk"></a>Python SDK

Alla registermetoder finns [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) i klassen och `register_azure_*`har formuläret .

Du hittar den information som du `register()` behöver för att fylla i metoden på [Azure-portalen](https://portal.azure.com).
Välj **Lagringskonton** i den vänstra rutan och välj det lagringskonto som du vill registrera. På sidan **Översikt** finns information som kontonamn, behållare och filresursnamn. 

* För autentiseringsobjekt, till exempel kontonyckel eller SAS-token, går du till **Kontonycklar** i **fönstret Inställningar.** 

* För tjänsthuvudobjekt som klient-ID och klient-ID går du till dina **appregistreringar** och väljer vilken app du vill använda. Motsvarande **översiktssida** innehåller dessa objekt.

> [!IMPORTANT]
> Om ditt lagringskonto finns i ett virtuellt nätverk stöds endast skapandet av Blob- , File share, ADLS Gen 1 och ADLS Gen **2-datalager via SDK.** Om du vill ge arbetsytan åtkomst till `grant_workspace_access` `True`ditt lagringskonto anger du parametern till .

Följande exempel visar hur du registrerar en Azure blob-behållare, en Azure-filresurs och Azure Data Lake Storage Generation 2 som ett datalager. För andra lagringstjänster, se [referensdokumentationen för `register_azure_*` tillämpliga metoder](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods).

#### <a name="blob-container"></a>Blobcontainer

Om du vill registrera en Azure blob-behållare som ett datalager använder du [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-).

Följande kod skapar och registrerar `blob_datastore_name` datalagret `ws` till arbetsytan. Det här datalagret `my-container-name` kommer åt `my-account-name` blob-behållaren på lagringskontot med hjälp av den angivna kontonyckeln.

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

Om du vill registrera en Azure-filresurs som ett datalager använder du [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). 

Följande kod skapar och registrerar `file_datastore_name` datalagret `ws` till arbetsytan. Det här datalagret kommer åt filresursen `my-fileshare-name` på `my-account-name` lagringskontot med hjälp av den angivna kontonyckeln.

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

#### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage Generation 2

För ett datalager för Azure Data Lake Storage Generation 2 (ADLS Gen 2) använder [du register_azure_data_lake_gen2()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) för att registrera ett datacenter som är anslutet till en Azure DataLake Gen 2-lagring med [behörighet för tjänstens huvudnamn](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal). För att kunna utnyttja ditt tjänstehuvudnamn måste du [registrera din ansökan](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) och ge tjänstens huvudman rätt dataåtkomst. Läs mer om [åtkomstkontroll som ställts in för ADLS Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Följande kod skapar och registrerar `adlsgen2_datastore_name` datalagret `ws` till arbetsytan. Det här datalagret `test` kommer `account_name` åt filsystemet på lagringskontot med hjälp av de angivna huvudautentiseringsuppgifterna för tjänsten.

```python 
adlsgen2_datastore_name = 'adlsgen2datastore'

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<my_subscription_id>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<my_resource_group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<my_account_name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<my_tenant_id>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<my_client_id>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<my_client_secret>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(workspace=ws,
                                                             datastore_name=adlsgen2_datastore_name,
                                                             account_name=account_name, # ADLS Gen2 account name
                                                             filesystem='test', # ADLS Gen2 filesystem
                                                             tenant_id=tenant_id, # tenant id of service principal
                                                             client_id=client_id, # client id of service principal
                                                             client_secret=client_secret) # the secret of service principal
```

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning-studio 

Skapa ett nytt datalager i några få steg i Azure Machine Learning studio:

> [!IMPORTANT]
> Om ditt lagringskonto finns i ett virtuellt nätverk stöds endast skapandet av datalager [via SDK.](#python-sdk) 

1. Logga in på [Azure Machine Learning studio](https://ml.azure.com/).
1. Välj **Datalager** i den vänstra rutan under **Hantera**.
1. Välj **+ Nytt datalager**.
1. Fyll i formuläret för ett nytt datalager. Formuläret uppdaterar sig intelligent baserat på dina val för Azure-lagringstyp och autentiseringstyp.
  
Du hittar den information som du behöver för att fylla i formuläret på [Azure-portalen](https://portal.azure.com). Välj **Lagringskonton** i den vänstra rutan och välj det lagringskonto som du vill registrera. På sidan **Översikt** finns information som kontonamn, behållare och filresursnamn. 

* För autentiseringsobjekt, till exempel kontonyckel eller SAS-token, går du till **Kontonycklar** i **fönstret Inställningar.** 

* För tjänsthuvudobjekt som klient-ID och klient-ID går du till dina **appregistreringar** och väljer vilken app du vill använda. Motsvarande **översiktssida** innehåller dessa objekt. 

I följande exempel visas hur formuläret ser ut när du skapar ett Azure blob-datalager: 
    
![Formulär för ett nytt datalager](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Hämta datalager från arbetsytan

Om du vill få ett specifikt datalager [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) registrerat på `Datastore` den aktuella arbetsytan använder du den statiska metoden i klassen:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Om du vill hämta listan över datalager som är [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) registrerade med en viss arbetsyta kan du använda egenskapen på ett arbetsyteobjekt:

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Om du vill hämta arbetsytans standarddatalager använder du den här raden:

```Python
datastore = ws.get_default_datastore()
```

<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>Ladda upp och ladda ner data

De [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) metoder och metoder som beskrivs i följande exempel är specifika för och fungerar på samma sätt för [azureblobDatastore-](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) och [AzureFileDatastore-klasserna.](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)

### <a name="upload"></a>Ladda upp

Ladda upp antingen en katalog eller enskilda filer till datalagret med hjälp av Python SDK:

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

Parametern `target_path` anger platsen i filresursen (eller blob-behållaren) som ska överföras. Det standard `None`är , så att data överförs till roten. Om `overwrite=True`skrivs befintliga `target_path` data på över.

Du kan också ladda upp en lista över `upload_files()` enskilda filer till datalagret via metoden.

### <a name="download"></a>Ladda ned

Ladda ned data från ett datalager till ditt lokala filsystem:

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

Parametern `target_path` är platsen för den lokala katalogen att hämta data till. Om du vill ange en sökväg till mappen i filresursen (eller blob-behållaren) som ska hämtas anger du sökvägen till `prefix`. Om `prefix` `None`är , kommer allt innehåll i filresursen (eller blob-behållaren) att hämtas.

<a name="train"></a>

## <a name="access-your-data-during-training"></a>Få tillgång till dina data under träningen

Om du vill interagera med data i dina datalager eller för att paketera dina data till ett förbrukningsobjekt för maskininlärningsuppgifter, till exempel utbildning, [skapar du en Azure Machine Learning-datauppsättning](how-to-create-register-datasets.md). Datauppsättningar tillhandahåller funktioner som läser in tabelldata i en pandas eller Spark DataFrame. Datauppsättningar ger också möjlighet att hämta eller montera filer av alla format från Azure Blob-lagring, Azure-filer, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database och Azure Database för PostgreSQL. [Läs mer om hur du tränar med datauppsättningar](how-to-train-with-datasets.md).

### <a name="accessing-source-code-during-training"></a>Komma åt källkoden under utbildningen

Azure Blob-lagring har högre dataflödeshastigheter än en Azure-filresurs och skalas till ett stort antal jobb som startats parallellt. Därför rekommenderar vi att du konfigurerar dina körningar för att använda Blob-lagring för överföring av källkodsfiler.

I följande kodexempel anges i körningskonfigurationen vilka blob-datalager som ska användas för källkodsöverföringar.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Få tillgång till data under bedömning

Azure Machine Learning innehåller flera sätt att använda dina modeller för bedömning. Vissa av dessa metoder ger inte åtkomst till datalager. Använd följande tabell för att förstå vilka metoder som gör att du kan komma åt datalager under bedömning:

| Metod | Tillgång till datalager | Beskrivning |
| ----- | :-----: | ----- |
| [Batchförutsägelse](how-to-use-parallel-run-step.md) | ✔ | Göra förutsägelser kring stora mängder data asynkront. |
| [Webbtjänst](how-to-deploy-and-where.md) | &nbsp; | Distribuera modeller som en webbtjänst. |
| [Azure IoT Edge-modul](how-to-deploy-and-where.md) | &nbsp; | Distribuera modeller till IoT Edge-enheter. |

I situationer där SDK inte ger åtkomst till datalager kan du kanske skapa anpassad kod med hjälp av relevant Azure SDK för att komma åt data. Azure Storage [SDK för Python](https://github.com/Azure/azure-storage-python) är till exempel ett klientbibliotek som du kan använda för att komma åt data som lagras i blobbar eller filer.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Flytta data till Azure-lagringslösningar som stöds

Azure Machine Learning stöder åtkomst till data från Azure Blob storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database och Azure Database for PostgreSQL. Om du använder lagring som inte stöds rekommenderar vi att du flyttar dina data till Azure-lagringslösningar som stöds med hjälp av [Azure Data Factory och dessa steg](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool). Om du flyttar data till lagring som stöds kan du spara datautgående kostnader under maskininlärningsexperiment. 

Azure Data Factory ger effektiv och flexibel dataöverföring med mer än 80 fördefinierade anslutningsappar utan extra kostnad. Dessa anslutningsappar inkluderar Azure-datatjänster, lokala datakällor, Amazon S3 och Redshift och Google BigQuery.

## <a name="next-steps"></a>Nästa steg

* [Skapa en Azure machine learning-datauppsättning](how-to-create-register-datasets.md)
* [Träna en modell](how-to-train-ml-models.md)
* [Distribuera en modell](how-to-deploy-and-where.md)
