---
title: Åtkomst till data i data lager/blobbar för utbildning
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder data lager för att komma åt BLOB-datalagring under utbildning med Azure Machine Learning service
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 05/24/2019
ms.custom: seodec18
ms.openlocfilehash: 97a4bc20394553b97211763cedaa76c3711306f2
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68319315"
---
# <a name="access-data-from-your-datastores"></a>Få åtkomst till data från dina data lager

 I Azure Machine Learning-tjänsten, beräknar data lager platser-oberoende mekanismer för att komma åt lagringen utan att behöva ändra käll koden. Oavsett om du skriver en tränings kod för att ta en sökväg som en parameter eller om du vill tillhandahålla ett data lager direkt till en uppskattning, Azure Machine Learning arbets flöden se till att dina data lager platser är tillgängliga och görs tillgängliga för din beräknings kontext.

Den här instruktionen visar exempel på följande uppgifter:
* [Välj ett data lager](#access)
* [Hämta data](#get)
* [Ladda upp och ladda ned data till data lager](#up-and-down)
* [Åtkomst till data lager under utbildning](#train)

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda datalager måste du först en [arbetsytan](concept-workspace.md).

Börja med antingen [skapar en ny arbetsyta](setup-create-workspace.md#sdk) eller hämta en befintlig:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="choose-a-datastore"></a>Välj ett data lager

Du kan använda standard data lagret eller ta med dina egna.

### <a name="use-the-default-datastore-in-your-workspace"></a>Använd standard data lagret i din arbets yta

 Varje arbets yta har ett registrerat standard-datalager som du kan använda direkt.

Hämta den arbetsytan standard datalager:

```Python
ds = ws.get_default_datastore()
```

### <a name="register-your-own-datastore-with-the-workspace"></a>Registrera ditt eget data lager med arbets ytan

Om du har befintliga Azure Storage kan registrera du den som ett datalager på din arbetsyta. 

<a name="store"></a>

####  <a name="storage-guidance"></a>Minnesriktlinjer

Vi rekommenderar Blob Storage och blob-datalager. Både standard-och Premium lagring är tillgängliga för blobbar. Även om det kostar mer kostsamt, rekommenderar vi Premium Storage på grund av snabbare data flödes hastigheter som kan förbättra hastigheten på inlärnings körningen, särskilt om du tränar mot en stor data uppsättning. Se [pris Kalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service) för lagrings kontot för kostnads information.

>[!NOTE]
> Azure Machine Learning-tjänsten stöder andra typer av data lager som kan vara användbara för vissa scenarier. Om du till exempel behöver träna med data som lagras i en databas kan du använda AzureSQLDatabaseDatastore eller AzurePostgreSqlDatastore. Se [den här tabellen](#matrix) för tillgängliga data lager typer.

#### <a name="register-your-datastore"></a>Registrera ditt data lager
Alla register metoder finns i [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) -klassen och har formatet register_azure_ *.

I följande exempel visas hur du registrerar en Azure Blob-behållare eller en Azure-filresurs som ett data lager.

+ För en **Azure Blob container data lager**använder du[`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  ds = Datastore.register_azure_blob_container(workspace=ws, 
                                               datastore_name='your datastore name', 
                                               container_name='your azure blob container name',
                                               account_name='your storage account name', 
                                               account_key='your storage account key',
                                               create_if_not_exists=True)
  ```

+ För ett **Azure**-filresurs-datalager använder [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)du. Exempel: 
  ```Python
  ds = Datastore.register_azure_file_share(workspace=ws, 
                                           datastore_name='your datastore name', 
                                           file_share_name='your file share name',
                                           account_name='your storage account name', 
                                           account_key='your storage account key',
                                           create_if_not_exists=True)
  ```

<a name="get"></a>

## <a name="find--define-datastores"></a>Hitta & definiera data lager

Använd [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) följande för att hämta ett angivet data lager i den aktuella arbets ytan:

```Python
#get named datastore from current workspace
ds = Datastore.get(ws, datastore_name='your datastore name')
```

Använd den här koden om du vill hämta en lista över alla data lager på en specifik arbets yta:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, ds in datastores.items():
    print(name, ds.datastore_type)
```

Om du vill definiera ett annat standard data lager för den aktuella [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-)arbets ytan använder du:

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Ladda upp & Hämta data
Metoderna [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) och [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) som beskrivs i följande exempel är bara för och är identiska för [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) -och [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) -klasserna.

### <a name="upload"></a>Ladda upp

 Ladda upp en katalog eller enskilda filer till databasen med hjälp av Python-SDK.

Ladda upp en katalog till ett datalager `ds`:

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

ds.upload(src_dir='your source directory',
          target_path='your target path',
          overwrite=True,
          show_progress=True)
```

`target_path` Anger var i filresursen (eller blob-behållare) för att ladda upp. Standard `None`, i vilket fall hämtar data som överförs till rot. `overwrite=True` skriver över befintliga data på `target_path`.

Eller ladda upp en lista med enskilda filer till databasen via datalagringen `upload_files()` metod.

### <a name="download"></a>Ladda ned
På samma sätt kan hämta data från ett datalager till det lokala filsystemet.

```Python
ds.download(target_path='your target path',
            prefix='your prefix',
            show_progress=True)
```

`target_path` är platsen för den lokala katalogen för nedladdning av data till. Om du vill ange en sökväg till mappen i filresursen (eller blob-behållare) för att hämta, ange sökvägen till `prefix`. Om `prefix` är `None`, ska ladda ned hela innehållet i filresursen (eller blob-behållare).

<a name="train"></a>
## <a name="access-datastores-during-training"></a>Åtkomst till data lager under utbildning

När du har gjort ditt data lager tillgängligt i utbildningen kan du komma åt det under utbildnings körningar (till exempel utbildning eller verifierings data) genom att helt enkelt skicka sökvägen till den som en parameter i utbildnings skriptet.

I följande tabell visas de [`DataReference`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) metoder som talar om för beräknings målet hur data lagret används vid körningar.

Tvåvägsupprättade|Metod|Beskrivning|
----|-----|--------
Montera| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| Används för att montera data lagret på Compute-målet.
Ladda ned|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|Använd för att ladda ned innehållet i ditt data lager till den plats `path_on_compute`som anges av. <br> För utbildningens körnings kontext sker den här hämtningen före körningen.
Ladda upp|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| Används för att ladda upp en fil från den plats `path_on_compute` som anges av till ditt data lager. <br> För utbildningens körnings kontext sker denna uppladdning efter körningen.

 ```Python
import azureml.data
from azureml.data.data_reference import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

Om du vill referera till en mapp eller fil i ditt data lager och göra den tillgänglig på beräknings målet, använder du [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-) data Lagrets funktion.

```Python
#download the contents of the `./bar` directory in ds to the compute target
ds.path('./bar').as_download()
```

> [!NOTE]
> Ett `ds` `"$AZUREML_DATAREFERENCE_XXXX"` eller `ds.path` -objekt matchar ett miljö variabel namn för det format vars värde representerar monterings-/hämtnings Sök vägen i mål beräkningen. Data lagrets sökväg i mål beräkningen kanske inte är samma som körnings Sök vägen för utbildnings skriptet.

<a name="matrix"></a>
### <a name="training-compute-and-datastore-matrix"></a>Övnings beräkning och data lager mat ris

I följande matris visas tillgängliga funktioner för data åtkomst för olika inlärnings mål och data bearbetnings scenarier. Läs mer om [inlärnings mål för Azure Machine Learning](how-to-set-up-training-targets.md#compute-targets-for-training).

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

### <a name="examples"></a>Exempel 

Följande kod exempel är speciella [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) för klassen för åtkomst till ditt data lager under träning.

Den här koden skapar en uppskattning med hjälp av övnings `train.py`skriptet, från den angivna käll katalogen med hjälp av de `script_params`parametrar som definierats i, allt på det angivna inlärnings målet.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': ds.as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

Du kan också skicka en lista över data lager till en uppskattad konstruktormetod `inputs` -parameter för att montera eller kopiera till/från dina data lager (er). Det här kod exemplet:
* Laddar ned allt innehåll i data `ds1` lagret till beräknings målet innan ditt utbildnings skript `train.py` körs
* Laddar ned mappen `'./foo'` i data `ds2` lagret till beräknings målet innan `train.py` körs
* Laddar upp filen `'./bar.pkl'` från Compute-målet upp till data lagret `ds3` när skriptet har körts

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```

## <a name="access-datastores-during-for-scoring"></a>Åtkomst till data lager under för poängsättning

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
