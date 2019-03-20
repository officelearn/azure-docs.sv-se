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
ms.openlocfilehash: af36f38bf206da588d327dc319d2418460f79b13
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58165036"
---
# <a name="access-data-from-your-datastores"></a>Komma åt data från ditt datalager

Datalager kan du interagera med och komma åt dina data oavsett om du kör koden lokalt, på ett beräkningskluster eller på en virtuell dator. I den här artikeln du lär dig Azure Machine Learning-arbetsflöden som ser till att dina datalager som är tillgängliga och få tillgång till din beräkningskontexten.

Den här anvisningen visar exempel för följande uppgifter:
* [Välj ett datalager](#access)
* [Hämta data](#get)
* [Ladda upp och ladda ned data till datalager](#up-and-down)
* [Åtkomst till datalagret under utbildning](#train)

## <a name="prerequisites"></a>Förutsättningar

Med datalager måste du ha en [arbetsytan](concept-azure-machine-learning-architecture.md#workspace) första. 

Börja med antingen [skapar en ny arbetsyta](quickstart-create-workspace-with-python.md) eller hämta en befintlig:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

Eller, [följa den här snabbstarten Python](quickstart-create-workspace-with-python.md) att använda SDK för att skapa din arbetsyta och komma igång.

<a name="access"></a>

## <a name="choose-a-datastore"></a>Välj ett datalager

Du kan använda standard-databasen eller ta med din egen.

### <a name="use-the-default-datastore-in-your-workspace"></a>Använd standard-datalager i din arbetsyta

Behöver inte skapa eller konfigurera ett lagringskonto eftersom varje arbetsyta har ett standard-datalager. Du kan använda som datalager direkt eftersom den redan är registrerad på arbetsytan. 

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
                                           container_name='your file share name',
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
from azureml.data import AzureFileDatastore, AzureBlobDatastore

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

När du gör ditt datalager tillgängliga i den fjärranslutna beräkningen, kan du komma åt den under träningskörningar (exempelvis utbildning eller validering data) genom att helt enkelt ange sökvägen till den som en parameter i dina utbildningsskript.

I följande tabell visas vanliga [ `DataReference` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) metoder som gör datalager som är tillgängliga i den fjärranslutna beräkningen.

##

Sätt|Metod|Beskrivning
----|-----|--------
Montera| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| Använd för att montera ett datalager i den fjärranslutna beräkningen. Standardläget för datalager.
Ladda ned|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|Använd data från den plats som anges av `path_on_compute` på din datalagret till den fjärranslutna databearbetning.
Ladda upp|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| Använd för att ladda upp data till roten för ditt datalager från den plats som anges av `path_on_compute`.

```Python
import azureml.data
from azureml.data import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

Om du vill referera till en viss mapp eller fil i ditt datalager, använder du datalagringen [ `path()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-) funktion.

```Python
#download the contents of the `./bar` directory from the datastore to the remote compute
ds.path('./bar').as_download()
```



> [!NOTE]
> Alla `ds` eller `ds.path` objekt som motsvarar en Miljövariabelns namn i formatet `"$AZUREML_DATAREFERENCE_XXXX"` vars värde representerar montering/hämtningssökvägen i den fjärranslutna beräkningen. Datastore-sökväg i den fjärranslutna beräkningen kanske inte är samma som körningssökvägen för utbildning-skriptet.

### <a name="examples"></a>Exempel 

Följande illustrera exempel som är specifika för den [ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) klassen för att komma åt ditt datalager under utbildningen.


```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': ds.as_mount()
}

est = Estimator(source_directory='your code directory',
                script_params=script_params,
                compute_target=compute_target,
                entry_script='train.py')
```

Eftersom `as_mount()` är standardläget för ett datalager kan du också direkt skicka `ds` till den `'--data_dir'` argumentet.

Eller skicka in en lista över datalager till konstruktorn kostnadsuppskattning `inputs` parameter för att montera eller kopiera till och från din datalagrets. Detta kodexempel:
* Laddar ned allt innehåll i datalagret `ds1` till fjärranslutna beräkningarna innan dina utbildningsskript `train.py` körs
* Hämtar mappen `'./foo'` i datalagret `ds2` till fjärranslutna beräkningarna innan `train.py` körs
* Överför filen `'./bar.pkl'` från fjärranslutna beräkningarna som är upp till databasen `ds3` när skriptet har körts

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```


## <a name="next-steps"></a>Nästa steg

* [Träna en modell](how-to-train-ml-models.md)

* [Distribuera en modell](how-to-deploy-and-where.md)
