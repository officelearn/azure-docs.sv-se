---
title: Komma åt data i datalager / BLOB-objekt för träning
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder datalager för att komma åt blobblagring för data under utbildning med Azure Machine Learning-tjänsten
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 02/25/2019
ms.custom: seodec18
ms.openlocfilehash: 0ab01187b03b3d658b171029003667588382bd7f
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2019
ms.locfileid: "59563542"
---
# <a name="access-data-from-your-datastores"></a>Komma åt data från ditt datalager

 I Azure Machine Learning-tjänsten är datalager compute Platsoberoende mekanismer för att komma åt lagringsutrymme utan ändringar i källkoden. Om du skriver kod för utbildning för att dra en sökväg som en parameter eller ange ett datalager direkt till en kostnadsuppskattning, kontrollera Azure Machine Learning arbetsflöden datastore-platser är tillgängliga och få tillgång till din beräkningskontexten.

Den här anvisningen visar exempel på följande uppgifter:
* [Välj ett datalager](#access)
* [Hämta data](#get)
* [Ladda upp och ladda ned data till datalager](#up-and-down)
* [Åtkomst till datalagret under utbildning](#train)

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda datalager måste du först en [arbetsytan](concept-azure-machine-learning-architecture.md#workspace).

Börja med antingen [skapar en ny arbetsyta](setup-create-workspace.md#sdk) eller hämta en befintlig:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="choose-a-datastore"></a>Välj ett datalager

Du kan använda standard-databasen eller ta med din egen.

### <a name="use-the-default-datastore-in-your-workspace"></a>Använd standard-datalager i din arbetsyta

 Varje arbetsyta har ett registrerade, standard-datalager som du kan använda direkt.

Hämta den arbetsytan standard datalager:

```Python
ds = ws.get_default_datastore()
```

### <a name="register-your-own-datastore-with-the-workspace"></a>Registrera ditt eget datalager med arbetsytan

Om du har befintliga Azure Storage kan registrera du den som ett datalager på din arbetsyta.   Alla register-metoder som finns på den [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) klassen och har formatet register_azure_ *. 

I följande exempel visar att registrera en Azure Blob-behållare eller en Azure-filresurs som ett datalager.

+ För en **Azure Blob-behållare datalager**, använda [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  ds = Datastore.register_azure_blob_container(workspace=ws, 
                                               datastore_name='your datastore name', 
                                               container_name='your azure blob container name',
                                               account_name='your storage account name', 
                                               account_key='your storage account key',
                                               create_if_not_exists=True)
  ```

+ För en **Azure filen resurs datalager**, använda [ `register_azure_file_share()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). Exempel: 
  ```Python
  ds = Datastore.register_azure_file_share(workspace=ws, 
                                           datastore_name='your datastore name', 
                                           file_share_name='your file share name',
                                           account_name='your storage account name', 
                                           account_key='your storage account key',
                                           create_if_not_exists=True)
  ```

<a name="get"></a>

## <a name="find--define-datastores"></a>Sök och definiera datalager

Hämta ett angivna datalager som registrerats i den aktuella arbetsytan [ `get()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) :

```Python
#get named datastore from current workspace
ds = Datastore.get(ws, datastore_name='your datastore name')
```

Använd den här koden för att få en lista över alla datalager i en viss arbetsyta:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, ds in datastores.items():
    print(name, ds.datastore_type)
```

För att definiera ett annat datalager för den aktuella arbetsytan använder [ `set_default_datastore()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-):

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Ladda upp och hämta data
Den [ `upload()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) och [ `download()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) metoder som beskrivs i följande exempel är specifika för och driva identiskt för den [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) och [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) klasser.

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
## <a name="access-datastores-during-training"></a>Datalager för åtkomst vid träning

När du gör ditt datalager tillgängliga på beräkningsmål-, kan du komma åt den under träningskörningar (exempelvis utbildning eller validering data) genom att helt enkelt ange sökvägen till den som en parameter i dina utbildningsskript.

Följande tabell listar de [ `DataReference` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) metoder som talar om hur du använder databasen under körningar för beräkningsmål.

Sätt|Metod|Beskrivning|
----|-----|--------
Montera| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| Använd för att montera databasen på beräkningsmål.
Ladda ned|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|Använd för att hämta innehållet i dina datalager till den plats som anges av `path_on_compute`. <br> Uppdateringen sker innan körningen för utbildning som kör kontext.
Ladda upp|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| Använda för att överföra en fil från den plats som anges av `path_on_compute` till ditt datalager. <br> Den här uppladdningen händer efter din körning för utbildning som kör kontext.

 ```Python
import azureml.data
from azureml.data.data_reference import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

Om du vill referera till en viss mapp eller fil i ditt datalager och göra den tillgänglig på beräkningsmål, använder du datalagringen [ `path()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-) funktion.

```Python
#download the contents of the `./bar` directory in ds to the compute target
ds.path('./bar').as_download()
```

> [!NOTE]
> Alla `ds` eller `ds.path` objekt som motsvarar en Miljövariabelns namn i formatet `"$AZUREML_DATAREFERENCE_XXXX"` vars värde representerar sökvägen till monteringspunkten/Hämtningsmappen i beräkningen som mål. Datastore-sökväg i beräkningen som mål kanske inte är samma som körningssökvägen för utbildning-skriptet.

### <a name="compute-context-and-datastore-type-matrix"></a>Compute-sammanhang och datalager typen matris

I följande matrisen visas de tillgängliga data åtkomst överallt-funktionerna för de olika kontext och datalager beräkningsscenarier. Termen ”Pipeline” i denna matris refererar till möjligheten att använda datalager som indata eller utdata i [Azure Machine Learning Pipelines](https://docs.microsoft.com/azure/machine-learning/service/concept-ml-pipelines).

||Lokala beräkningar|Azure Machine Learning-beräkning|Dataöverföring|Databricks|HDInsight|Azure Batch|Azure DataLake Analytics|Virtuella datorer|
-|--|-----------|----------|---------|-----|--------------|---------|---------|
|AzureBlobDatastore|[`as_download()`] [`as_upload()`]|[`as_mount()`]<br> [`as_download()`] [`as_upload()`] <br> Pipeline|Pipeline|Pipeline|[`as_download()`] <br> [`as_upload()`]|Pipeline||[`as_download()`] <br> [`as_upload()`]|
|AzureFileDatastore|[`as_download()`] [`as_upload()`]|[`as_mount()`]<br> [`as_download()`] [`as_upload()`] Pipeline |||[`as_download()`] [`as_upload()`]|||[`as_download()`] [`as_upload()`]|
|AzureDataLakeDatastore|||Pipeline|Pipeline|||Pipeline||
|AzureDataLakeGen2Datastore|||Pipeline||||||
|AzureDataPostgresSqlDatastore|||Pipeline||||||
|AzureSqlDatabaseDataDatastore|||Pipeline||||||


> [!NOTE]
> Det kan finnas scenarier som iterativ mycket stora mängder dataprocesser körs snabbare med [`as_download()`] i stället för [`as_mount()`]; detta kan verifieras experimentellt.

### <a name="examples"></a>Exempel 

Följande kodexempel är specifika för den [ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) klassen för att komma åt ditt datalager under utbildningen.

Den här koden skapar en kostnadsuppskattning med hjälp av skriptet utbildning `train.py`, från den angivna källkatalogen med parametrar som definierats i `script_params`, på den angivna beräkningsmål.

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

Du kan även skicka i en lista över datalager till konstruktorn kostnadsuppskattning `inputs` parameter för att montera eller kopiera till och från din datalagrets. Detta kodexempel:
* Laddar ned allt innehåll i datalagret `ds1` till beräkningsmål innan dina utbildningsskript `train.py` körs
* Hämtar mappen `'./foo'` i datalagret `ds2` till beräkningsmål innan `train.py` körs
* Överför filen `'./bar.pkl'` från beräkningsmål upp till databasen `ds3` när skriptet har körts

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```

## <a name="next-steps"></a>Nästa steg

* [Träna en modell](how-to-train-ml-models.md)

* [Distribuera en modell](how-to-deploy-and-where.md)
