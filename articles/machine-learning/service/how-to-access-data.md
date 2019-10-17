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
ms.date: 08/2/2019
ms.custom: seodec18
ms.openlocfilehash: 3576f7cc0297ff1e9b10373ccc27b09e1a0ae8ae
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72436707"
---
# <a name="access-data-in-azure-storage-services"></a>Få åtkomst till data i Azure Storage-tjänster

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

- [Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)eller åtkomst till din [landnings sida för din arbets yta (för hands version)](https://ml.azure.com/).

- En Azure Machine Learning-arbetsyta. 
    - [Skapa antingen en Azure Machine Learning arbets yta](how-to-manage-workspace.md) eller Använd en befintlig med python SDK.

        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore
        
        ws = Workspace.from_config()
        ```

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Skapa och registrera data lager

När du registrerar en Azure Storage-lösning som ett data lager skapar du automatiskt detta data lager i en angiven arbets yta. Du kan skapa och registrera data lager på en arbets yta med python SDK eller sidans landnings sida.

### <a name="using-the-python-sdk"></a>Använda Python SDK

Alla register metoder finns i klassen [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) och har formatet register_azure_ *.

Den information du behöver för att fylla i metoden register () finns i [Azure Portal](https://ms.portal.azure.com). Välj **lagrings konton** i den vänstra rutan och välj det lagrings konto som du vill registrera. Sidan **Översikt** innehåller information, till exempel konto namn och behållare eller fil resurs namn. För autentiseringsinformation, som konto nyckel eller SAS-token, navigerar du till **konto nycklar** i fönstret **Inställningar** till vänster. 

I följande exempel visas hur du registrerar en Azure Blob-behållare eller en Azure-filresurs som ett data lager.

+ Använd [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) för en **Azure Blob container data lager**

    Följande kod skapar och registrerar data lagret `my_datastore`, till arbets ytan `ws`. I det här data lagret används Azure Blob-behållaren `my_blob_container`, på Azure Storage-kontot `my_storage_account` med den angivna konto nyckeln.

    ```Python
       datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                          datastore_name='my_datastore', 
                                                          container_name='my_blob_container',
                                                          account_name='my_storage_account', 
                                                          account_key='your storage account key',
                                                          create_if_not_exists=True)
    ```

+ Använd [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)för en **Azure-fil resurs data lager**. 

    Följande kod skapar och registrerar data lagret `my_datastore`, till arbets ytan `ws`. I det här data lagret används Azure-filresursen `my_file_share`, på Azure Storage-kontot `my_storage_account` med den angivna konto nyckeln.

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

### <a name="using-the-workspace-landing-page"></a>Använda arbets ytans landnings sida 

Skapa ett nytt data lager med några steg i landnings sidan för arbets ytan.

1. Logga in på [sidan med landnings sidan för arbets ytan](https://ml.azure.com/).
1. Välj **data lager** i det vänstra fönstret under **Hantera**.
1. Välj **+ nytt data lager**.
1. Slutför det nya data lager formuläret. Det formulär som uppdateras med hjälp av alternativen för Azure Storage-typ och autentiseringstyp.
  
Den information du behöver fylla i formuläret finns i [Azure Portal](https://ms.portal.azure.com). Välj **lagrings konton** i den vänstra rutan och välj det lagrings konto som du vill registrera. Sidan **Översikt** innehåller information, till exempel konto namn och behållare eller fil resurs namn. För autentiseringsscheman, som konto nyckel eller SAS-token, navigerar du till **konto nycklar** i fönstret **Inställningar** till vänster.

Följande exempel visar hur formuläret skulle se ut för att skapa ett Azure Blob-datalager. 
    
 ![Nytt data lager](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Hämta data lager från din arbets yta

Om du vill hämta ett särskilt data lager som registrerats i den aktuella arbets ytan använder du den statiska metoden [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) i data lager klass:

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

När du skapar en arbets yta registreras en Azure Blob-behållare och en Azure-filresurs till arbets ytan med namnet `workspaceblobstore` respektive `workspacefilestore`. De lagrar anslutnings informationen för BLOB-behållaren och fil resursen som är etablerad i det lagrings konto som är kopplat till arbets ytan. @No__t-0 anges som standard data lager.

Så här hämtar du arbets ytans standard data lager:

```Python
datastore = ws.get_default_datastore()
```

Om du vill definiera ett annat standard data lager för den aktuella arbets ytan använder du metoden [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) på objektet arbets yta:

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Ladda upp & Hämta data
Metoderna [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) och [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) som beskrivs i följande exempel är speciella för och har samma funktioner som [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) -och [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) -klasserna.

### <a name="upload"></a>Ladda upp

 Ladda upp antingen en katalog eller enskilda filer till data lagret med python SDK.

Ladda upp en katalog till ett data lager `datastore`:

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

Parametern `target_path` anger platsen i fil resursen (eller BLOB-behållaren) som ska överföras. Standardvärdet är `None`, vilket innebär att data laddas upp till roten. När `overwrite=True` skrivs alla befintliga data på `target_path` över.

Eller ladda upp en lista med enskilda filer till data lagret via metoden `upload_files()`.

### <a name="download"></a>Ladda ned

På samma sätt kan du hämta data från ett data lager till ditt lokala fil system.

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

Parametern `target_path` är platsen för den lokala katalogen där data ska hämtas till. Om du vill ange en sökväg till mappen i fil resursen (eller BLOB-behållaren) som ska laddas ned anger du sökvägen till `prefix`. Om `prefix` är `None` kommer allt innehåll i din fil resurs (eller BLOB-behållare) att hämtas.

<a name="train"></a>
## <a name="access-your-data-during-training"></a>Få åtkomst till dina data under utbildningen

> [!IMPORTANT]
> Att använda [Azure Machine Learning data uppsättningar (för hands version)](how-to-create-register-datasets.md) är det nya rekommenderade sättet att komma åt dina data i utbildningen. Data uppsättningar tillhandahåller funktioner som läser in tabell data i Pandas eller Spark DataFrame, och möjligheten att ladda ned eller montera filer i alla format från Azure Blob, Azure File, Azure Data Lake gen 1, Azure Data Lake gen 2, Azure SQL, Azure PostgreSQL. Lär dig mer om [hur du tränar med data uppsättningar](how-to-train-with-datasets.md).

I följande tabell visas de metoder som talar om för beräknings målet hur data lagringen ska användas vid körningar. 

Tvåvägsupprättade|Metod|Beskrivning|
----|-----|--------
Montera| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| Används för att montera data lagret på Compute-målet.
Ladda ned|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|Använd för att ladda ned innehållet i ditt data lager till den plats som anges av `path_on_compute`. <br><br> Den här nedladdningen sker före körningen.
Ladda upp|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| Använd för att ladda upp en fil från den plats som anges av `path_on_compute` till ditt data lager. <br><br> Överföringen sker efter din körning.

Om du vill referera till en mapp eller fil i ditt data lager och göra den tillgänglig på beräknings målet använder du metoden data lager [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) .

```Python
#to mount the full contents in your storage to the compute target
datastore.as_mount()

#to download the contents of the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> Alla angivna `datastore`-eller `datastore.path`-objekt matchas mot ett miljö variabel namn för formatet `"$AZUREML_DATAREFERENCE_XXXX"`, vars värde representerar monterings-/hämtnings Sök vägen i mål beräkningen. Data lagrets sökväg i mål beräkningen kanske inte är samma som körnings Sök vägen för utbildnings skriptet.

### <a name="examples"></a>Exempel 

Följande kod exempel är speciella för klassen [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) för att få åtkomst till data under träning. 

`script_params` är en ord lista som innehåller parametrar för entry_script. Använd den för att skicka in ett data lager och beskriva hur data görs tillgängliga på beräknings målet. Läs mer i vår [självstudier](tutorial-train-models-with-aml.md)från slut punkt till slut punkt.

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

Du kan också skicka en lista över data lager till den uppskattade konstruktorn `inputs`-parametern för att montera eller kopiera data till/från dina data källor. Det här kod exemplet:
* Laddar ned allt innehåll i `datastore1` till beräknings målet innan ditt utbildnings skript `train.py` körs
* Hämtar mappen `'./foo'` i `datastore2` till Compute-målet innan `train.py` körs
* Överför filen `'./bar.pkl'` från beräknings målet till `datastore3` När skriptet har körts

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```
<a name="matrix"></a>

### <a name="compute-and-datastore-matrix"></a>Beräknings-och data lager mat ris

Data lager har för närvarande stöd för lagring av anslutnings information till de lagrings tjänster som anges i följande matris. I den här matrisen visas tillgängliga funktioner för data åtkomst för de olika Compute-målen och data bearbetnings scenarierna. Läs mer om [beräknings målen för Azure Machine Learning](how-to-set-up-training-targets.md#compute-targets-for-training).

|Databearbetning|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) - [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Lokal|[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|Gäller inte         |Gäller inte                                                                         |
| Azure Machine Learning Compute |[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ml @ no__t-4pipelines](concept-ml-pipelines.md)|[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ml @ no__t-4pipelines](concept-ml-pipelines.md)|Gäller inte         |Gäller inte                                                                         |
| Virtuella maskiner               |[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |Gäller inte         |Gäller inte                                                                         |
| HDInsight                      |[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |Gäller inte         |Gäller inte                                                                         |
| Dataöverföring                  |[ML @ no__t-1pipelines](concept-ml-pipelines.md)                                               |Gäller inte                                           |[ML @ no__t-1pipelines](concept-ml-pipelines.md)            |[ML @ no__t-1pipelines](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[ML @ no__t-1pipelines](concept-ml-pipelines.md)                                              |Gäller inte                                           |[ML @ no__t-1pipelines](concept-ml-pipelines.md)             |Gäller inte                                                                         |
| Azure Batch                    |[ML @ no__t-1pipelines](concept-ml-pipelines.md)                                               |Gäller inte                                           |Gäller inte         |Gäller inte                                                                         |
| Azure DataLake-analys       |Gäller inte                                           |Gäller inte                                           |[ML @ no__t-1pipelines](concept-ml-pipelines.md)             |Gäller inte                                                                         |

> [!NOTE]
> Det kan finnas scenarier där hög iterativa data processer körs snabbare med `as_download()` i stället för `as_mount()`; Detta kan verifieras experimentellt.

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
| [Webb tjänst](how-to-deploy-and-where.md) | &nbsp; | Distribuera modeller som en webb tjänst. |
| [IoT Edge modul](how-to-deploy-and-where.md) | &nbsp; | Distribuera modeller till IoT Edge enheter. |

I situationer där SDK inte ger åtkomst till data lager kan du skapa anpassad kod med hjälp av relevanta Azure SDK för att få åtkomst till data. Till exempel är [Azure Storage SDK för python](https://github.com/Azure/azure-storage-python) ett klient bibliotek som du kan använda för att komma åt data som lagras i blobbar eller filer.

<a name="move"></a>
## <a name="move-data-to-supported-azure-storage-solutions"></a>Flytta data till Azure Storage-lösningar som stöds

Azure Machine Learning-tjänsten har stöd för åtkomst till data från Azure Blob, Azure File, Azure Data Lake gen 1, Azure Data Lake gen 2, Azure SQL, Azure PostgreSQL. Vi rekommenderar att du flyttar dina data till våra Azure Storage-lösningar som stöds med hjälp av Azure Data Factory för att spara data Utgångs kostnader för lagring som inte stöds under Machine Learning-experiment. Azure Data Factory ger effektiv och flexibel data överföring med fler än 80 färdiga anslutnings program, inklusive Azure Data Services, lokala data källor, Amazon S3 och RedShift och Google BigQuery – utan extra kostnad. [Följ steg för steg-guiden för att flytta dina data med hjälp av Azure Data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

## <a name="next-steps"></a>Nästa steg

* [Träna en modell](how-to-train-ml-models.md)

* [Distribuera en modell](how-to-deploy-and-where.md)
