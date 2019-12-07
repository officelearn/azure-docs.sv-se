---
title: Få åtkomst till data i Azure Storage-tjänster
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder data lager för att få åtkomst till Azure Storage-tjänster under utbildning med Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: d2e86c06cca26da2776459f3c20bf921a02ed89b
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894714"
---
# <a name="access-data-in-azure-storage-services"></a>Få åtkomst till data i Azure Storage-tjänster
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du enkelt kan komma åt dina data i Azure Storage-tjänster via Azure Machine Learning data lager. Data lager används för att lagra anslutnings information, t. ex. prenumerations-ID och token-auktorisering. Med hjälp av data lager kan du komma åt lagringen utan att behöva hårdkoda anslutnings information i dina skript. Du kan skapa data lager från dessa [Azure Storage-lösningar](#matrix). För lagrings lösningar som inte stöds, rekommenderar vi att du flyttar dina data till våra Azure Storage-lösningar som stöds för att spara data vid maskin inlärning. [Lär dig hur du flyttar dina data](#move). 

Den här instruktionen visar exempel på följande uppgifter:
* [Registrera data lager](#access)
* [Hämta data lager från arbets ytan](#get)
* [Ladda upp och ladda ned data med data lager](#up-and-down)
* [Få åtkomst till data under utbildning](#train)
* [Flytta data till Azure](#move)

## <a name="prerequisites"></a>Krav

- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

- Ett Azure Storage-konto med en [Azure Blob-behållare](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) eller [Azure-filresurs](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- [Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)eller åtkomst till [Azure Machine Learning Studio](https://ml.azure.com/).

- En Azure Machine Learning-arbetsyta. 
    - [Skapa antingen en Azure Machine Learning arbets yta](how-to-manage-workspace.md) eller Använd en befintlig med python SDK.

        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore
        
        ws = Workspace.from_config()
        ```

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Skapa och registrera data lager

När du registrerar en Azure Storage-lösning som ett data lager skapar du automatiskt detta data lager i en angiven arbets yta. Du kan skapa och registrera data lager på en arbets yta med python SDK eller Azure Machine Learning Studio.

### <a name="using-the-python-sdk"></a>Använda Python SDK

Alla register metoder finns i [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) -klassen och har formatet register_azure_ *.

Den information du behöver för att fylla i metoden register () finns via [Azure Portal](https://portal.azure.com). Välj **lagrings konton** i den vänstra rutan och välj det lagrings konto som du vill registrera. Sidan **Översikt** innehåller information, till exempel konto namn och behållare eller fil resurs namn. För autentiseringsinformation, som konto nyckel eller SAS-token, navigerar du till **konto nycklar** i fönstret **Inställningar** till vänster. 

I följande exempel visas hur du registrerar en Azure Blob-behållare eller en Azure-filresurs som ett data lager.

+ För en **Azure Blob container data lager**använder [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)

    Följande kod skapar och registrerar data lagret, `my_datastore`till arbets ytan `ws`. Detta data lager har åtkomst till Azure Blob-behållaren `my_blob_container`på Azure Storage-kontot `my_storage_account` att använda den angivna konto nyckeln.

    ```Python
       datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                          datastore_name='my_datastore', 
                                                          container_name='my_blob_container',
                                                          account_name='my_storage_account', 
                                                          account_key='your storage account key',
                                                          create_if_not_exists=True)
    ```
    Om ditt lagrings konto finns i ett VNET stöds endast skapande av Azure Blob-datalager. Ange parametern `grant_workspace_access` att `True` för att ge din arbets yta åtkomst till ditt lagrings konto.

+ Använd [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)för ett **fil resurs data lager i Azure**. 

    Följande kod skapar och registrerar data lagret, `my_datastore`till arbets ytan `ws`. I det här data lagret får du till gång till Azure-filresursen `my_file_share`på Azure Storage-kontot `my_storage_account` att använda den angivna konto nyckeln.

    ```Python
       datastore = Datastore.register_azure_file_share(workspace=ws, 
                                                      datastore_name='my_datastore', 
                                                      file_share_name='my_file_share',
                                                      account_name='my_storage account', 
                                                      account_key='your storage account key',
                                                      create_if_not_exists=True)
    ```

####  <a name="storage-guidance"></a>Minnesriktlinjer

Vi rekommenderar Azure Blob-behållare. Både standard-och Premium lagring är tillgängliga för blobbar. Även om det kostar mer kostsamt, rekommenderar vi Premium Storage på grund av snabbare data flödes hastigheter som kan förbättra hastigheten på inlärnings körningen, särskilt om du tränar mot en stor data uppsättning. Se [pris Kalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service) för lagrings kontot för kostnads information.

### <a name="using-azure-machine-learning-studio"></a>Använda Azure Machine Learning Studio 

Skapa ett nytt data lager med några steg i Azure Machine Learning Studio.

1. Logga in på [Azure Machine Learning Studio](https://ml.azure.com/).
1. Välj **data lager** i det vänstra fönstret under **Hantera**.
1. Välj **+ nytt data lager**.
1. Slutför det nya data lager formuläret. Det formulär som uppdateras med hjälp av alternativen för Azure Storage-typ och autentiseringstyp.
  
Den information du behöver för att fylla i formuläret finns via [Azure Portal](https://portal.azure.com). Välj **lagrings konton** i den vänstra rutan och välj det lagrings konto som du vill registrera. Sidan **Översikt** innehåller information, till exempel konto namn och behållare eller fil resurs namn. För autentiseringsscheman, som konto nyckel eller SAS-token, navigerar du till **konto nycklar** i fönstret **Inställningar** till vänster.

Följande exempel visar hur formuläret skulle se ut för att skapa ett Azure Blob-datalager. 
    
 ![Nytt data lager](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Hämta data lager från din arbets yta

Om du vill hämta ett särskilt data lager som registrerats i den aktuella arbets ytan använder du metoden [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) statisk metod i data lagrings klassen:

```Python
#get named datastore from current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Om du vill hämta listan över data lager som registrerats med en specifik arbets yta kan du använda egenskapen [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) på ett objekt i arbets ytan:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

När du skapar en arbets yta registreras en Azure Blob-behållare och en Azure-filresurs till arbets ytan med namnet `workspaceblobstore` respektive `workspacefilestore`. De lagrar anslutnings informationen för BLOB-behållaren och fil resursen som är etablerad i det lagrings konto som är kopplat till arbets ytan. `workspaceblobstore` anges som standard data lager.

Hämta den arbetsytan standard datalager:

```Python
datastore = ws.get_default_datastore()
```

Om du vill definiera ett annat standard data lager för den aktuella arbets ytan använder du [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) metoden på objektet arbets yta:

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Ladda upp & Hämta data
De [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) -och [`download()`s](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) metoderna som beskrivs i följande exempel är speciella för och körs identiskt för [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) -och [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) -klasserna.

### <a name="upload"></a>Ladda upp

 Ladda upp en katalog eller enskilda filer till databasen med hjälp av Python-SDK.

Ladda upp en katalog till ett datalager `datastore`:

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

Parametern `target_path` anger platsen i fil resursen (eller BLOB-behållaren) som ska överföras. Standard `None`, i vilket fall hämtar data som överförs till rot. Annars, om `overwrite=True` befintliga data på `target_path` skrivs över.

Eller ladda upp en lista med enskilda filer till data lagret via `upload_files()`-metoden.

### <a name="download"></a>Ladda ned

På samma sätt kan hämta data från ett datalager till det lokala filsystemet.

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

Parametern `target_path` är platsen för den lokala katalog där data ska hämtas till. Om du vill ange en sökväg till mappen i filresursen (eller blob-behållare) för att hämta, ange sökvägen till `prefix`. Om `prefix` är `None`, ska ladda ned hela innehållet i filresursen (eller blob-behållare).

<a name="train"></a>
## <a name="access-your-data-during-training"></a>Få åtkomst till dina data under utbildningen

> [!IMPORTANT]
> Att använda [Azure Machine Learning data uppsättningar](how-to-create-register-datasets.md) är det nya rekommenderade sättet att komma åt dina data i utbildningen. Data uppsättningar tillhandahåller funktioner som läser in tabell data i Pandas eller Spark DataFrame, och möjligheten att ladda ned eller montera filer i alla format från Azure Blob, Azure File, Azure Data Lake gen 1, Azure Data Lake gen 2, Azure SQL, Azure PostgreSQL. Lär dig mer om [hur du tränar med data uppsättningar](how-to-train-with-datasets.md).

I följande tabell visas de metoder som talar om för beräknings målet hur data lagringen ska användas vid körningar. 

Tvåvägsupprättade|Metod|Beskrivning|
----|-----|--------
Montera| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| Används för att montera data lagret på Compute-målet.
Ladda ned|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|Använd för att ladda ned innehållet i ditt data lager till den plats som anges av `path_on_compute`. <br><br> Den här nedladdningen sker före körningen.
Ladda upp|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| Använd för att ladda upp en fil från den plats som anges av `path_on_compute` till ditt data lager. <br><br> Överföringen sker efter din körning.

Om du vill referera till en mapp eller fil i ditt data lager och göra den tillgänglig på beräknings målet, använder du [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) metoden data lager.

```Python
#to mount the full contents in your storage to the compute target
datastore.as_mount()

#to download the contents of the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> Alla angivna `datastore`-eller `datastore.path`-objekt matchas mot ett miljö variabel namn i formatet `"$AZUREML_DATAREFERENCE_XXXX"`, vars värde representerar monterings-/hämtnings Sök vägen i mål beräkningen. Data lagrets sökväg i mål beräkningen kanske inte är samma som körnings Sök vägen för utbildnings skriptet.

### <a name="examples"></a>Exempel 

Följande kod exempel är speciella för [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) klassen för åtkomst till data under träning. 

`script_params` är en ord lista som innehåller parametrar till entry_script. Använd den för att skicka in ett data lager och beskriva hur data görs tillgängliga på beräknings målet. Läs mer i vår [självstudier](tutorial-train-models-with-aml.md)från slut punkt till slut punkt.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': datastore.path('/bar').as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

Du kan också skicka en lista över data lager till konstruktorn för en uppskattad konstruktor `inputs` parameter för att montera eller kopiera data till/från dina data lager (er). Det här kod exemplet:
* Laddar ned allt innehåll i `datastore1` till beräknings målet innan ditt utbildnings skript `train.py` körs
* Laddar ned mappen `'./foo'` i `datastore2` till Compute-målet innan `train.py` körs
* Laddar upp filen `'./bar.pkl'` från Compute-målet till `datastore3` när skriptet har körts

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```
<a name="matrix"></a>

### <a name="compute-and-datastore-matrix"></a>Beräknings-och data lager mat ris

Data lager har för närvarande stöd för lagring av anslutnings information till de lagrings tjänster som anges i följande matris. I den här matrisen visas tillgängliga funktioner för data åtkomst för de olika Compute-målen och data bearbetnings scenarierna. Läs mer om [beräknings målen för Azure Machine Learning](how-to-set-up-training-targets.md#compute-targets-for-training).

|Databearbetning|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Lokal|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|Gäller inte         |Gäller inte                                                                         |
| Azure Machine Learning-beräkning |[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|Gäller inte         |Gäller inte                                                                         |
| Virtuella maskiner               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |Gäller inte         |Gäller inte                                                                         |
| HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |Gäller inte         |Gäller inte                                                                         |
| Dataöverföring                  |[ML&nbsp;pipelines](concept-ml-pipelines.md)                                               |Gäller inte                                           |[ML&nbsp;pipelines](concept-ml-pipelines.md)            |[ML&nbsp;pipelines](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[ML&nbsp;pipelines](concept-ml-pipelines.md)                                              |Gäller inte                                           |[ML&nbsp;pipelines](concept-ml-pipelines.md)             |Gäller inte                                                                         |
| Azure Batch                    |[ML&nbsp;pipelines](concept-ml-pipelines.md)                                               |Gäller inte                                           |Gäller inte         |Gäller inte                                                                         |
| Azure DataLake-analys       |Gäller inte                                           |Gäller inte                                           |[ML&nbsp;pipelines](concept-ml-pipelines.md)             |Gäller inte                                                                         |

> [!NOTE]
> Det kan finnas scenarier där mycket iterativa stora data processer körs snabbare med `as_download()` i stället för `as_mount()`; Detta kan verifieras experimentellt.

### <a name="accessing-source-code-during-training"></a>Åtkomst till käll kod under träning

Azure Blob Storage har högre överföringshastigheter än Azure-filresursen och kommer att skalas till ett stort antal jobb som startas parallellt. Därför rekommenderar vi att du konfigurerar dina körningar för att använda blob-lagring för överföring av källfiler.

I följande kod exempel anges i den körnings konfiguration som BLOB data lager ska användas för käll kods överföringar.

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
| [IoT Edge modul](how-to-deploy-and-where.md) | &nbsp; | Distribuera modeller till IoT Edge enheter. |

I situationer där SDK inte ger åtkomst till data lager kan du skapa anpassad kod med hjälp av relevanta Azure SDK för att få åtkomst till data. Till exempel är [Azure Storage SDK för python](https://github.com/Azure/azure-storage-python) ett klient bibliotek som du kan använda för att komma åt data som lagras i blobbar eller filer.

<a name="move"></a>
## <a name="move-data-to-supported-azure-storage-solutions"></a>Flytta data till Azure Storage-lösningar som stöds

Azure Machine Learning stöder åtkomst till data från Azure-Blob, Azure-fil, Azure Data Lake gen 1, Azure Data Lake gen 2, Azure SQL, Azure PostgreSQL. Vi rekommenderar att du flyttar dina data till våra Azure Storage-lösningar som stöds med hjälp av Azure Data Factory för att spara data Utgångs kostnader för lagring som inte stöds under Machine Learning-experiment. Azure Data Factory ger effektiv och flexibel data överföring med fler än 80 färdiga anslutnings program, inklusive Azure Data Services, lokala data källor, Amazon S3 och RedShift och Google BigQuery – utan extra kostnad. [Följ steg för steg-guiden för att flytta dina data med hjälp av Azure Data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

## <a name="next-steps"></a>Nästa steg

* [Träna en modell](how-to-train-ml-models.md).

* [Distribuera en modell](how-to-deploy-and-where.md).
