---
title: Ansluta till Azure Storage-tjänster
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder data lager för att säkert ansluta till Azure Storage-tjänster under utbildning med Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/24/2020
ms.custom: seodec18, tracking-python
ms.openlocfilehash: d73427db5fd168a31c478f92ef11307df136a775
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2020
ms.locfileid: "85125420"
---
# <a name="connect-to-azure-storage-services"></a>Ansluta till Azure Storage-tjänster
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du **ansluter till Azure Storage-tjänster via Azure Machine Learning data lager**. Data lager lagrar anslutnings information, t. ex. prenumerations-ID och token-auktorisering i [Key Vault](https://azure.microsoft.com/services/key-vault/) som är kopplade till arbets ytan, så att du kan komma åt lagringen på ett säkert sätt utan att behöva hårdkoda dem i dina skript. 

**För lagrings lösningar som inte stöds**och för att spara utgående kostnader under ml-experiment, [flytta dina data](#move) till en Azure Storage-lösning som stöds.  Du kan skapa data lager från [dessa Azure Storage-lösningar](#matrix). 

Information om var data lagret får plats i Azure Machine Learning det totala arbets flödet för data åtkomst finns i artikeln [säker åtkomst till data](concept-data.md#data-workflow) .

## <a name="prerequisites"></a>Krav

Du behöver:
- En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

- Ett Azure Storage-konto med en [Azure Blob-behållare](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) eller [Azure-filresurs](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- [Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)eller åtkomst till [Azure Machine Learning Studio](https://ml.azure.com/).

- En Azure Machine Learning-arbetsyta.
  
  [Skapa antingen en Azure Machine Learning arbets yta](how-to-manage-workspace.md) eller Använd en befintlig via python SDK. Importera `Workspace` och `Datastore` -klassen och Läs in din prenumerations information från filen `config.json` med hjälp av funktionen `from_config()` . Detta söker efter JSON-filen i den aktuella katalogen som standard, men du kan också ange en Sök vägs parameter som pekar på filen med hjälp av `from_config(path="your/file/path")` .

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Typer av data lagrings tjänster som stöds

Data lager har för närvarande stöd för lagring av anslutnings information till de lagrings tjänster som anges i följande matris.

| Lagrings &nbsp; typ | Autentiseringstyp &nbsp; | [Azure &nbsp; Machine &nbsp; Learning Studio](https://ml.azure.com/) | [Azure &nbsp; Machine &nbsp; Learning &nbsp; python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [Azure &nbsp; Machine &nbsp; Learning cli](reference-azure-machine-learning-cli.md) | [REST-API för Azure &nbsp; Machine &nbsp; Learning &nbsp;](https://docs.microsoft.com/rest/api/azureml/)
---|---|---|---|---|---
[Azure &nbsp; BLOB &nbsp; Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| Kontonyckel <br> SAS-token | ✓ | ✓ | ✓ |✓
[Azure &nbsp; - &nbsp; filresurs](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| Kontonyckel <br> SAS-token | ✓ | ✓ | ✓ |✓
[Azure &nbsp; data Lake &nbsp; Storage gen &nbsp; 1](https://docs.microsoft.com/azure/data-lake-store/)| Tjänstens huvudnamn| ✓ | ✓ | ✓ |✓
[Azure &nbsp; data Lake &nbsp; Storage gen &nbsp; 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| Tjänstens huvudnamn| ✓ | ✓ | ✓ |✓
[Azure &nbsp; SQL &nbsp; Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| SQL-autentisering <br>Tjänstens huvudnamn| ✓ | ✓ | ✓ |✓
[Azure- &nbsp; postgresql](https://docs.microsoft.com/azure/postgresql/overview) | SQL-autentisering| ✓ | ✓ | ✓ |✓
[Azure &nbsp; Database &nbsp; för &nbsp; mysql](https://docs.microsoft.com/azure/mysql/overview) | SQL-autentisering|  | ✓* | ✓* |✓*
[Databricks &nbsp; fil &nbsp; system](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)| Ingen autentisering | | ✓** | ✓ ** |✓** 

* MySQL stöds endast för pipeline- [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). <br>
* * Databricks stöds bara för pipeline- [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)

### <a name="storage-guidance"></a>Minnesriktlinjer

Vi rekommenderar att du skapar ett data lager för en [Azure Blob-behållare](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Både standard-och Premium lagring är tillgängliga för blobbar. Även om Premium Storage är dyrare, kan snabbare data flödes hastigheter förbättra hastigheten på din utbildning, särskilt om du tränar mot en stor data uppsättning. Information om kostnaden för lagrings konton finns i [pris Kalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json) bygger på Azure Blob Storage och är utformat för företags Big data Analytics. En grundläggande del av Data Lake Storage Gen2 är att lägga till ett [hierarkiskt namn område](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) till Blob Storage. Det hierarkiska namn området ordnar objekt/filer i en hierarki med kataloger för effektiv data åtkomst.

När du skapar en arbets yta registreras en Azure Blob-behållare och en Azure-filresurs automatiskt till arbets ytan. De heter `workspaceblobstore` `workspacefilestore` respektive. `workspaceblobstore`används för att lagra arbets ytans artefakter och dina experiment loggar för Machine Learning. `workspacefilestore`används för att lagra antecknings böcker och R-skript som har auktoriserats via [beräknings instanser](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files). `workspaceblobstore`Containern har angetts som standard data lager.

> [!IMPORTANT]
> Azure Machine Learning designer (för hands version) skapar ett data lager med namnet **azureml_globaldatasets** automatiskt när du öppnar ett exempel på design sidan för designern. Detta data lager innehåller bara exempel data uppsättningar. Använd **inte** det här data lagret för någon konfidentiell data åtkomst.
> ![Automatiskt skapad data lager för designer exempel data uppsättningar](media/how-to-access-data/datastore-designer-sample.png)

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Skapa och registrera data lager

När du registrerar en Azure Storage-lösning som ett data lager skapar och registrerar du automatiskt data lagret till en speciell arbets yta. Du kan skapa och registrera data lager på en arbets yta med hjälp av [python SDK](#python-sdk) eller [Azure Machine Learning Studio](#azure-machine-learning-studio).

>[!IMPORTANT]
> Som en del av den första processen för att skapa och registrera data lagret, verifierar Azure Machine Learning att den underliggande lagrings tjänsten finns och att den användare som angav huvud kontot (användar namn, tjänstens huvud namn eller SAS-token) har åtkomst till lagringen. För Azure Data Lake Storage gen 1-och 2-datalager, sker dock verifieringen senare, när data åtkomst metoder som [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py) eller [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) anropas. 
<br><br>
När data lagret har skapats utförs den här verifieringen bara för metoder som kräver åtkomst till den underliggande lagrings behållaren, **inte** varje gång som data lager objekt hämtas. Verifieringen sker till exempel om du vill hämta filer från ditt data lager. men om du bara vill ändra ditt standard data lager sker inte verifieringen.

### <a name="python-sdk"></a>Python SDK

Alla register metoder finns i- [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) klassen och har formuläret `register_azure_*` .
> [!IMPORTANT]
> Om ditt lagrings konto finns i ett virtuellt nätverk stöds endast skapande av data lager **via SDK** .

Du hittar den information som du behöver för att fylla i `register_azure_*()` metoden på [Azure Portal](https://portal.azure.com).

* Data lager namnet får bara bestå av gemena bokstäver, siffror och under streck. 

* Om du planerar att använda en konto nyckel eller SAS-token för autentisering väljer du **lagrings konton** i den vänstra rutan och väljer det lagrings konto som du vill registrera. 
  * **Översikts** sidan innehåller information som konto namn, behållare och fil resurs namn. 
      1. För konto nycklar går du till **åtkomst nycklar** i fönstret **Inställningar** . 
      1. För SAS-token, gå till **signaturer för delad åtkomst** i fönstret **Inställningar** .

* Om du planerar att använda ett huvud namn för tjänsten för autentisering går du till din **Appregistreringar** och väljer vilken app du vill använda. 
    * Dess motsvarande **översikts** sida innehåller nödvändig information, t. ex. klient-ID och klient-ID.

> [!IMPORTANT]
> Av säkerhets skäl kan du behöva ändra åtkomst nycklarna för ett Azure Storage konto (konto nyckel eller SAS-token). När du gör det måste du synkronisera de nya autentiseringsuppgifterna med din arbets yta och de data lager som är anslutna till den. Lär dig hur du synkroniserar dina uppdaterade autentiseringsuppgifter med [de här stegen](how-to-change-storage-access-key.md). 

I följande exempel visas hur du registrerar en Azure Blob-behållare, en Azure-filresurs och Azure Data Lake Storage generation 2 som ett data lager. De parametrar som anges i de här exemplen är de **parametrar som krävs** för att skapa och registrera ett data lager. 

Om du vill skapa data lager för andra lagrings tjänster och se valfria parametrar för dessa metoder kan du läsa [referens dokumentationen för de aktuella `register_azure_*` metoderna](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods).

#### <a name="blob-container"></a>Blobcontainer

Använd för att registrera en Azure Blob-behållare som ett data lager [`register_azure_blob_container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) .

Följande kod skapar och registrerar `blob_datastore_name` data lagret på `ws` arbets ytan. Detta data lager har åtkomst till `my-container-name` BLOB-behållaren på `my-account-name` lagrings kontot med hjälp av den angivna konto åtkomst nyckeln.

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```
Om BLOB-behållaren finns i ett virtuellt nätverk inkluderar du parametern `skip_validation=True` i din [`register_azure_blob_container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) metod. 

#### <a name="file-share"></a>Filresurs

Använd för att registrera en Azure-filresurs som ett data lager [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-) . 

Följande kod skapar och registrerar `file_datastore_name` data lagret på `ws` arbets ytan. Detta data lager har åtkomst till `my-fileshare-name` fil resursen på `my-account-name` lagrings kontot med hjälp av den angivna konto åtkomst nyckeln.

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                     datastore_name=file_datastore_name, 
                                                     file_share_name=file_share_name, 
                                                     account_name=account_name,
                                                     account_key=account_key)
```
Om fil resursen finns i ett virtuellt nätverk inkluderar du parametern `skip_validation=True` i din [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-) metod. 

#### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage generation 2

För en Azure Data Lake Storage generation 2 (ADLS gen 2) data lager använder du [register_azure_data_lake_gen2 ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) för att registrera ett data lager för autentiseringsuppgifter som är anslutet till en Azure DataLake gen 2-lagring med [tjänstens huvud namn](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal). För att kunna använda tjänstens huvud namn måste du [Registrera ditt program](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) och bevilja tjänstens huvud namn med åtkomst till *lagrings BLOB-dataläsaren* . Lär dig mer om [åtkomst kontroll som har kon figurer ATS för ADLS gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

För att kunna använda tjänstens huvud namn måste du [Registrera ditt program](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) och ge tjänstens huvud namn till gång till rätt data åtkomst. Lär dig mer om [åtkomst kontroll som har kon figurer ATS för ADLS gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Följande kod skapar och registrerar `adlsgen2_datastore_name` data lagret på `ws` arbets ytan. Detta data lager har åtkomst till fil systemet `test` i `account_name` lagrings kontot med hjälp av de angivna autentiseringsuppgifterna för tjänstens huvud namn.

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

Skapa ett nytt data lager med några steg i Azure Machine Learning Studio:

> [!IMPORTANT]
> Om ditt lagrings konto finns i ett virtuellt nätverk stöds endast skapande av data lager [via SDK](#python-sdk) . 

1. Logga in på [Azure Machine Learning Studio](https://ml.azure.com/).
1. Välj **data lager** i det vänstra fönstret under **Hantera**.
1. Välj **+ nytt data lager**.
1. Fyll i formuläret för ett nytt data lager. Formuläret uppdateras intelligent baserat på dina val för Azures lagrings typ och autentiseringstyp.
  
Du kan hitta den information som du behöver för att fylla i formuläret på [Azure Portal](https://portal.azure.com). Välj **lagrings konton** i den vänstra rutan och välj det lagrings konto som du vill registrera. **Översikts** sidan innehåller information som konto namn, behållare och fil resurs namn. 

* För verifierings objekt, som konto nyckel eller SAS-token, går du till **åtkomst nycklar** i fönstret **Inställningar** . 

* För tjänstens huvud objekt, t. ex. klient-ID och klient-ID, går du till **Appregistreringar** och väljer vilken app du vill använda. Dess motsvarande **översikts** sida kommer att innehålla dessa objekt. 

> [!IMPORTANT]
> Av säkerhets skäl kan du behöva ändra åtkomst nycklarna för ett Azure Storage konto (konto nyckel eller SAS-token). När du gör det måste du synkronisera de nya autentiseringsuppgifterna med din arbets yta och de data lager som är anslutna till den. Lär dig hur du synkroniserar dina uppdaterade autentiseringsuppgifter med [de här stegen](how-to-change-storage-access-key.md). 

Följande exempel visar hur formuläret ser ut när du skapar ett Azure Blob-datalager: 
    
![Formulär för ett nytt data lager](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Hämta data lager från din arbets yta

Om du vill hämta ett särskilt data lager som registrerats i den aktuella arbets ytan använder du den [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) statiska metoden i `Datastore` klassen:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Om du vill hämta listan över data lager som registrerats med en specifik arbets yta kan du använda- [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) egenskapen på ett objekt i arbets ytan:

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Använd den här raden för att hämta arbets ytans standard data lager:

```Python
datastore = ws.get_default_datastore()
```
Du kan också ändra standard data lagret med följande kod. Den här funktionen stöds bara via SDK. 

```Python
 ws.set_default_datastore(new_default_datastore)
```
<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>Ladda upp och ladda ned data

[`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-)Metoderna och som [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) beskrivs i följande exempel är bara för och har samma funktioner som [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) -och [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) -klasserna.

> [!NOTE]
> Det finns inte stöd för att ladda upp till AzureDataLakeGen2-datalager just nu.

### <a name="upload"></a>Ladda upp

Ladda upp antingen en katalog eller enskilda filer till data lagret med hjälp av python SDK:

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

`target_path`Parametern anger platsen i fil resursen (eller BLOB-behållaren) som ska överföras. Den används som standard `None` , så data överförs till roten. Om `overwrite=True` kommer befintliga data att `target_path` skrivas över.

Du kan också ladda upp en lista med enskilda filer till data lagret via- `upload_files()` metoden.

### <a name="download"></a>Ladda ned

Hämta data från ett data lager till ditt lokala fil system:

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

`target_path`Parametern är platsen för den lokala katalogen där data ska hämtas till. Om du vill ange en sökväg till mappen i fil resursen (eller BLOB-behållaren) som ska laddas ned anger du sökvägen till `prefix` . Om `prefix` är `None` , kommer allt innehåll i din fil resurs (eller BLOB-behållare) att laddas ned.

<a name="train"></a>

## <a name="access-your-data-during-training"></a>Få åtkomst till dina data under utbildningen

Om du vill interagera med data i dina data lager eller paketera dina data i ett förbruknings Bart objekt för Machine Learning-uppgifter, till exempel utbildning, [skapar du en Azure Machine Learning data uppsättning](how-to-create-register-datasets.md). Data uppsättningar tillhandahåller funktioner som läser in tabell data i en Pandas-eller Spark-DataFrame. Data uppsättningar ger också möjlighet att ladda ned eller montera filer i alla format från Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database och Azure Database for PostgreSQL. [Lär dig mer om hur du tränar med data uppsättningar](how-to-train-with-datasets.md).

### <a name="accessing-source-code-during-training"></a>Åtkomst till käll kod under träning

Azure Blob Storage har högre överföringshastigheter än en Azure-filresurs och kommer att skalas till ett stort antal jobb som startas parallellt. Därför rekommenderar vi att du konfigurerar dina körningar för att använda blob-lagring för överföring av källfiler.

I följande kod exempel anges i den körnings konfiguration som BLOB data lager ska användas för käll kods överföringar.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Åtkomst till data under Poängsättning

Azure Machine Learning tillhandahåller flera olika sätt att använda dina modeller för att beräkna poäng. Några av dessa metoder ger inte åtkomst till data lager. Använd följande tabell för att förstå vilka metoder du kan använda för att komma åt data lager under poängsättningen:

| Metod | Åtkomst till data lager | Beskrivning |
| ----- | :-----: | ----- |
| [Batchförutsägelse](how-to-use-parallel-run-step.md) | ✔ | Göra förutsägelser kring stora mängder data asynkront. |
| [Webb tjänst](how-to-deploy-and-where.md) | &nbsp; | Distribuera modeller som en webb tjänst. |
| [Azure IoT Edge modul](how-to-deploy-and-where.md) | &nbsp; | Distribuera modeller till IoT Edge enheter. |

I situationer där SDK inte ger åtkomst till data lager kan du kanske skapa anpassad kod med hjälp av relevanta Azure SDK för att få åtkomst till data. Till exempel är [Azure Storage SDK för python](https://github.com/Azure/azure-storage-python) ett klient bibliotek som du kan använda för att komma åt data som lagras i blobbar eller filer.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Flytta data till Azure Storage-lösningar som stöds

Azure Machine Learning stöder åtkomst till data från Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database och Azure Database for PostgreSQL. Om du använder lagring som inte stöds rekommenderar vi att du flyttar dina data till Azure Storage-lösningar som stöds med hjälp av [Azure Data Factory och de här stegen](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool). Genom att flytta data till lagring som stöds kan du spara data utgående kostnader under Machine Learning-experiment. 

Azure Data Factory ger effektiv och flexibel data överföring med fler än 80 färdiga kopplingar utan extra kostnad. Dessa anslutningar omfattar Azure Data Services, lokala data källor, Amazon S3 och RedShift och Google BigQuery.

## <a name="next-steps"></a>Nästa steg

* [Skapa en Azure Machine Learning-datauppsättning](how-to-create-register-datasets.md)
* [Träna en modell](how-to-train-ml-models.md)
* [Distribuera en modell](how-to-deploy-and-where.md)
