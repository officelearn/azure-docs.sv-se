---
title: Få åtkomst till data i Azure Storage-tjänster
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder data lager för att få åtkomst till Azure Storage-tjänster under utbildning med Azure Machine Learning service
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 08/2/2019
ms.custom: seodec18
ms.openlocfilehash: 7b800a7ef38624dbe89a61dd04e2bd97b02066bb
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70191929"
---
# <a name="access-data-in-azure-storage-services"></a>Få åtkomst till data i Azure Storage-tjänster

 I den här artikeln får du lära dig hur du enkelt kan komma åt dina data i Azure Storage-tjänster via Azure Machine Learning data lager. Data lager används för att lagra anslutnings information, t. ex. prenumerations-ID och token-auktorisering, för att få åtkomst till lagrings utrymmet utan att behöva hårdkoda informationen i dina skript.

Den här instruktionen visar exempel på följande uppgifter:
* [Registrera data lager](#access)
* [Hämta data lager från arbets ytan](#get)
* [Ladda upp och ladda ned data med data lager](#up-and-down)
* [Få åtkomst till data under utbildning](#train)

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda datalager måste du först en [arbetsytan](concept-workspace.md).

Börja med antingen [skapar en ny arbetsyta](how-to-manage-workspace.md) eller hämta en befintlig:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="register-datastores"></a>Registrera data lager

Alla register metoder finns i [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) -klassen och har formatet register_azure_ *.

I följande exempel visas hur du registrerar en Azure Blob-behållare eller en Azure-filresurs som ett data lager.

+ För en **Azure Blob container data lager**använder du[`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                      datastore_name='your datastore name', 
                                                      container_name='your azure blob container name',
                                                      account_name='your storage account name', 
                                                      account_key='your storage account key',
                                                      create_if_not_exists=True)
  ```

+ För ett **Azure**-filresurs-datalager använder [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)du. Exempel: 
  ```Python
  datastore = Datastore.register_azure_file_share(workspace=ws, 
                                                  datastore_name='your datastore name', 
                                                  file_share_name='your file share name',
                                                  account_name='your storage account name', 
                                                  account_key='your storage account key',
                                                  create_if_not_exists=True)
  ```

####  <a name="storage-guidance"></a>Minnesriktlinjer

Vi rekommenderar Azure Blob-behållare. Både standard-och Premium lagring är tillgängliga för blobbar. Även om det kostar mer kostsamt, rekommenderar vi Premium Storage på grund av snabbare data flödes hastigheter som kan förbättra hastigheten på inlärnings körningen, särskilt om du tränar mot en stor data uppsättning. Se [pris Kalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service) för lagrings kontot för kostnads information.

<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Hämta data lager från din arbets yta

Om du vill hämta ett särskilt data lager som registrerats i den [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) aktuella arbets ytan använder du den statiska metoden i data lagrings klassen:

```Python
#get named datastore from current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Om du vill hämta listan över data lager som registrerats med en specifik arbets yta kan `datastores` du använda-egenskapen på ett objekt i arbets ytan:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

När du skapar en arbets yta registreras en Azure Blob-behållare och en Azure-filresurs till arbets ytan `workspaceblobstore` med `workspacefilestore` namnet respektive. De lagrar anslutnings informationen för BLOB-behållaren och fil resursen som är etablerad i det lagrings konto som är kopplat till arbets ytan. `workspaceblobstore` Anges som standard data lager.

Hämta den arbetsytan standard datalager:

```Python
datastore = ws.get_default_datastore()
```

Om du vill definiera ett annat standard data lager för den aktuella [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) arbets ytan använder du metoden på objektet arbets yta:

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Ladda upp & Hämta data
Metoderna [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) och [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) som beskrivs i följande exempel är bara för och är identiska för [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) -och [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) -klasserna.

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

`target_path` Parametern anger platsen i fil resursen (eller BLOB-behållaren) som ska överföras. Standard `None`, i vilket fall hämtar data som överförs till rot. När `overwrite=True` befintliga`target_path` data skrivs över.

Eller ladda upp en lista med enskilda filer till data lagret via `upload_files()` -metoden.

### <a name="download"></a>Ladda ned

På samma sätt kan hämta data från ett datalager till det lokala filsystemet.

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

`target_path` Parametern är platsen för den lokala katalogen där data ska hämtas till. Om du vill ange en sökväg till mappen i filresursen (eller blob-behållare) för att hämta, ange sökvägen till `prefix`. Om `prefix` är `None`, ska ladda ned hela innehållet i filresursen (eller blob-behållare).

<a name="train"></a>
## <a name="access-your-data-during-training"></a>Få åtkomst till dina data under utbildningen

För att få åtkomst till data under utbildningen kan du antingen ladda ned eller montera dina data från dina Azure Storage-tjänster till beräknings målet via data lager.

I följande tabell visas de metoder som talar om för beräknings målet hur data lagringen ska användas vid körningar. 

Tvåvägsupprättade|Metod|Beskrivning|
----|-----|--------
Montera| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| Används för att montera data lagret på Compute-målet.
Ladda ned|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|Använd för att ladda ned innehållet i ditt data lager till den plats `path_on_compute`som anges av. <br> Den här nedladdningen sker före körningen.
Ladda upp|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| Används för att ladda upp en fil från den plats `path_on_compute` som anges av till ditt data lager. <br> Överföringen sker efter din körning.

Om du vill referera till en mapp eller fil i ditt data lager och göra den tillgänglig på beräknings målet, använder [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) du data lagrings metoden.

```Python
#to mount the full contents in your storage to the compute target
datastore.as_mount()

#to download the contents of the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> Ett `datastore` `"$AZUREML_DATAREFERENCE_XXXX"`eller `datastore.path` -objekt matchar ett miljö variabel namn för formatet, vars värde representerar monterings-/hämtnings Sök vägen i mål beräkningen. Data lagrets sökväg i mål beräkningen kanske inte är samma som körnings Sök vägen för utbildnings skriptet.

### <a name="examples"></a>Exempel 

Följande kod exempel är speciella för [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) klassen för att få åtkomst till data under träning. 

`script_params`är en ord lista som innehåller parametrar för entry_script. Du kan använda den för att skicka in ett data lager och beskriva hur data ska göras tillgängliga för beräknings mål. Läs mer i vår självstudier från slut punkt [](tutorial-train-models-with-aml.md)till slut punkt.

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

Du kan också skicka en lista över data lager till en uppskattad konstruktormetod `inputs` -parameter för att montera eller kopiera data till/från dina data lager (er). Det här kod exemplet:
* Laddar ned allt innehåll i `datastore1` beräknings målet innan ditt utbildnings skript `train.py` körs
* Laddar ned mappen `'./foo'` i `datastore2` till Compute-målet innan `train.py` den körs
* Laddar upp filen `'./bar.pkl'` från Compute-målet `datastore3` till när skriptet har körts

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```

### <a name="compute-and-datastore-matrix"></a>Beräknings-och data lager mat ris

Data lager har för närvarande stöd för lagring av anslutnings information till de lagrings tjänster som anges i följande matris. I den här matrisen visas tillgängliga funktioner för data åtkomst för de olika Compute-målen och data bearbetnings scenarierna. Läs mer om [beräknings målen för Azure Machine Learning](how-to-set-up-training-targets.md#compute-targets-for-training).

|Compute|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Lokala|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|Gäller inte         |Gäller inte                                                                         |
| Azure Machine Learning-beräkning |[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|Gäller inte         |Gäller inte                                                                         |
| Virtuella datorer               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |Gäller inte         |Gäller inte                                                                         |
| HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |Gäller inte         |Gäller inte                                                                         |
| Dataöverföring                  |[ML&nbsp;pipelines](concept-ml-pipelines.md)                                               |Gäller inte                                           |[ML&nbsp;pipelines](concept-ml-pipelines.md)            |[ML&nbsp;pipelines](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[ML&nbsp;pipelines](concept-ml-pipelines.md)                                              |Gäller inte                                           |[ML&nbsp;pipelines](concept-ml-pipelines.md)             |Gäller inte                                                                         |
| Azure Batch                    |[ML&nbsp;pipelines](concept-ml-pipelines.md)                                               |Gäller inte                                           |Saknas         |Gäller inte                                                                         |
| Azure DataLake-analys       |Gäller inte                                           |Gäller inte                                           |[ML&nbsp;pipelines](concept-ml-pipelines.md)             |Gäller inte                                                                         |

> [!NOTE]
> Det kan finnas scenarier där hög iterativa data processer körs snabbare med `as_download()` i stället för `as_mount()`; detta kan verifieras experimentellt.

### <a name="accessing-source-code-during-training"></a>Åtkomst till käll kod under träning

Azure Blob Storage har högre överföringshastigheter än Azure-filresursen och kommer att skalas till ett stort antal jobb som startas parallellt. Därför rekommenderar vi att du konfigurerar dina körningar för att använda blob-lagring för överföring av källfiler.

I följande kod exempel anges i den körnings konfiguration som BLOB data lager ska användas för käll kods överföringar.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Åtkomst till data under Poängsättning

Azure Machine Learnings tjänsten ger dig flera olika sätt att använda dina modeller för poäng. Några av dessa metoder ger inte åtkomst till data lager. Använd följande tabell för att förstå vilka metoder du kan använda för att komma åt data lager under poängsättningen:

| Metod | Åtkomst till data lager | Beskrivning |
| ----- | :-----: | ----- |
| [Batch förutsägelse](how-to-run-batch-predictions.md) | ✔ | Gör förutsägelser för stora mängder data asynkront. |
| [Webbtjänst](how-to-deploy-and-where.md) | &nbsp; | Distribuera modeller som en webb tjänst. |
| [IoT Edge modul](how-to-deploy-and-where.md) | &nbsp; | Distribuera modeller till IoT Edge enheter. |

I situationer där SDK inte ger åtkomst till data lager kan du kanske skapa anpassad kod med hjälp av relevanta Azure SDK för att få åtkomst till data. Du kan till exempel använda [Azure Storage SDK för python](https://github.com/Azure/azure-storage-python) för att komma åt data som lagras i blobbar.


## <a name="next-steps"></a>Nästa steg

* [Träna en modell](how-to-train-ml-models.md)

* [Distribuera en modell](how-to-deploy-and-where.md)
