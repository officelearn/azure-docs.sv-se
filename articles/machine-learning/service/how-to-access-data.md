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
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: 98977f20af734e3adf927213b685f8c33b9383ea
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56816886"
---
# <a name="access-data-from-your-datastores"></a>Komma åt data från ditt datalager
I den här artikeln får du lära dig olika sätt att komma åt och interagera med dina data i Azure Machine Learning arbetsflöden via datalager.

I Azure Machine Learning-tjänsten databasen är en abstraktion över [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction). Datalagringen kan referera till antingen en [Azure Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) behållare eller [Azure-filresurs](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) som det underliggande lagringsutrymmet. 

Den här anvisningen visar exempel för följande uppgifter: 
* Skapa ett datalager
* [Ladda upp och ladda ned data till datalager](#upload-and-download-data)
* [Åtkomst till datalagret för träning](#access-datastores-for-training)

## <a name="create-a-datastore"></a>Skapa ett datalager
Om du vill använda datalager måste du först en [arbetsytan](concept-azure-machine-learning-architecture.md#workspace). Börja med antingen [skapar en ny arbetsyta](quickstart-create-workspace-with-python.md) eller hämta en befintlig:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

### <a name="use-the-default-datastore"></a>Använd standard-datalager
Behöver inte skapa eller konfigurera ett lagringskonto.  Varje arbetsyta har ett standard-datalager som du kan börja använda direkt.

Hämta den arbetsytan standard datalager:
```Python
ds = ws.get_default_datastore()
```

### <a name="register-a-datastore"></a>Registrera ett datalager
Om du har befintliga Azure Storage kan registrera du den som ett datalager på din arbetsyta. Du kan också registrera ett Azure Blob-behållare eller Azure-filresurs som ett datalager. Alla register-metoder som finns på den [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) klassen och har formen `register_azure_*`.

#### <a name="azure-blob-container-datastore"></a>Azure Blob-behållare datalager
Registrera ett datalager i Azure Blob-behållare [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-:)

```Python
ds = Datastore.register_azure_blob_container(workspace=ws, 
                                             datastore_name='your datastore name', 
                                             container_name='your azure blob container name',
                                             account_name='your storage account name', 
                                             account_key='your storage account key',
                                             create_if_not_exists=True)
```

#### <a name="azure-file-share-datastore"></a>Azure File Share datalager
Registrera en Azure-filresurs-datalager [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)

```Python
ds = Datastore.register_azure_file_share(workspace=ws, 
                                         datastore_name='your datastore name', 
                                         container_name='your file share name',
                                         account_name='your storage account name', 
                                         account_key='your storage account key',
                                         create_if_not_exists=True)
```

### <a name="get-an-existing-datastore"></a>Hämta en befintlig datalager
Fråga efter en redan registrerade datalager efter namnet:

```Python
ds = Datastore.get(ws, datastore_name='your datastore name')
```

Du kan också få alla datalagringen för en arbetsyta:

```Python
datastores = ws.datastores
for name, ds in datastores.items():
    print(name, ds.datastore_type)
```

För att underlätta för att ange en av dina registrerade datalager som standard-databasen för din arbetsyta:

```Python
ws.set_default_datastore('your datastore name')
```

## <a name="upload-and-download-data"></a>Ladda upp och hämta data
### <a name="upload"></a>Ladda upp
Ladda upp en katalog eller enskilda filer till databasen med hjälp av Python-SDK.

Ladda upp en katalog till ett datalager `ds`:

```Python
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

## <a name="access-datastores-for-training"></a>Åtkomst till datalager för träning
Du kan komma åt ett datalager under ett utbildnings kör (till exempel för utbildning eller validering data) på en fjärransluten beräkningsmål via Python SDK.

Det finns två sätt att tillgängliggöra dina datalager i den fjärranslutna beräkningen som stöds:
* **Montera**  
    * `ds.as_mount()`, att ange det här läget montera databasen hämtar monterad åt dig i den fjärranslutna beräkningen. 

* **Ladda ned/laddar upp**  
    * `ds.as_download(path_on_compute='your path on compute')` hämtar data från ditt datalager till fjärranslutna beräkningarna till den plats som anges av `path_on_compute`.

    * `ds.as_upload(path_on_compute='yourfilename'` Överför data till roten för ditt datalager från den plats som anges av `path_on_compute`
    
Om du vill referera till en viss mapp eller fil i ditt datalager, använder du datalagringen **`path()`** funktion.

```Python
#download the contents of the `./bar` directory from the datastore 
ds.path('./bar').as_download()
```
Alla `ds` eller `ds.path` objekt som motsvarar en Miljövariabelns namn i formatet `"$AZUREML_DATAREFERENCE_XXXX"` vars värde representerar montering/hämtningssökvägen i den fjärranslutna beräkningen. Datastore-sökväg i den fjärranslutna beräkningen kanske inte är samma som sökväg för körningen för skriptet.

Om du vill få åtkomst till ditt datalager vid träning, skickar du det till dina utbildningsskript som ett argument på kommandoraden via `script_params` från den [kostnadsuppskattning](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) klass:

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
`as_mount()` är standardläget för ett datalager, så du kan också direkt skicka `ds` till den `'--data_dir'` argumentet.

Eller skicka in en lista över datalager till konstruktorn kostnadsuppskattning `inputs` parameter för att montera eller kopiera till och från din datalagrets

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```

Koden ovan kommer att:

* Hämta allt innehåll i datalagret `ds1` till fjärranslutna beräkningarna innan dina utbildningsskript `train.py` körs

* ladda ned mappen med `'./foo'` i datalagret `ds2` till fjärranslutna beräkningarna innan `train.py` körs

* ladda upp filen `'./bar.pkl'` från fjärranslutna beräkningarna som är upp till databasen `d3` när skriptet har körts

## <a name="next-steps"></a>Nästa steg

* [Träna en modell](how-to-train-ml-models.md)
* [Distribuera en modell](how-to-deploy-and-where.md)

