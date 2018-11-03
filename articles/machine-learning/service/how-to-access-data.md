---
title: Använd datalager i Azure Machine Learning för att få åtkomst till data
description: Hur du använder datalager att komma åt data lagringsutrymme vid träning
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 79e26d4d2cf5743abae6dc0f1fb58585e1b9b9db
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2018
ms.locfileid: "50977914"
---
# <a name="how-to-access-data-during-training"></a>Hur du kommer åt data vid träning
Använd ett datalager för att komma åt och interagera med dina data i Azure Machine Learning-arbetsflöden.

I Azure Machine Learning-tjänsten databasen är en abstraktion över [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction). Datalagringen kan referera till antingen en [Azure Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) behållare eller [Azure-filresurs](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) som det underliggande lagringsutrymmet. 

## <a name="create-a-datastore"></a>Skapa ett datalager
Om du vill använda datalager måste du först en [arbetsytan](concept-azure-machine-learning-architecture.md#workspace). Börja med antingen [skapar en ny arbetsyta](quickstart-create-workspace-with-python.md) eller hämta en befintlig:

```Python
import azureml.core
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="use-the-default-datastore"></a>Använd standard-datalager
Behöver inte skapa eller konfigurera ett lagringskonto.  Varje arbetsyta har ett standard-datalager som du kan börja använda direkt.

Hämta den arbetsytan standard datalager:
```Python
ds = ws.get_default_datastore()
```

### <a name="register-a-datastore"></a>Registrera ett datalager
Om du har befintliga Azure Storage kan registrera du den som ett datalager på din arbetsyta. Du kan registrera ett Azure Blob-behållare eller Azure-filresurs som ett datalager. Alla register-metoder som finns på den `Datastore` klassen och har formen `register_azure_*`.

#### <a name="azure-blob-container-datastore"></a>Azure Blob-behållare datalager
Registrera ett datalager i Azure Blob-behållare:

```Python
ds = Datastore.register_azure_blob_container(workspace=ws, 
                                             datastore_name='your datastore name', 
                                             container_name='your azure blob container name',
                                             account_name='your storage account name', 
                                             account_key='your storage account key',
                                             create_if_not_exists=True)
```

#### <a name="azure-file-share-datastore"></a>Azure File Share datalager
Registrera en Azure-filresurs datalager:

```Python
ds = Datastore.register_azure_file_share(workspace=ws, 
                                         datastore_name='your datastore name', 
                                         container_name='your file share name',
                                         account_name='your storage account name', 
                                         account_key='your storage account key',
                                         create_if_not_exists=True)
```

### <a name="get-an-existing-datastore"></a>Hämta en befintlig datalager
Fråga efter ett registrerade datalager efter namnet:
```Python
ds = Datastore.get(ws, datastore_name='your datastore name')
```

Du kan också få alla datalagringen för en arbetsyta:
```Python
datastores = ws.datastores()
for name, ds in datastores.items(),
    print(name, ds.datastore_type)"
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
`ds.as_mount()`: genom att ange det här mount-läget kan datalagringen ska monteras åt dig i den fjärranslutna beräkningen. 
* **Ladda ned/laddar upp**  
    * `ds.as_download(path_on_compute='your path on compute')` hämtar data från ditt datalager till fjärranslutna beräkningarna till den plats som anges av `path_on_compute`.
    * `ds.as_upload(path_on_compute='yourfilename'` Överför data till databasen.  Anta att din utbildning skriptet skapar en `foo.pkl` fil i den aktuella arbetskatalogen i den fjärranslutna beräkningen. Ladda upp den här filen till ditt datalager med `ds.as_upload(path_on_compute='./foo.pkl')` när filen skapas i skriptet. Filen har överförts till roten i ditt datalager.
    
Om du vill referera till en viss mapp eller fil i ditt datalager, använder du datalagringen **`path`** funktion. Till exempel för att hämta innehållet i den `./bar` katalogen från databasen till din beräkningsmål, Använd `ds.path('./bar').as_download()`.

Alla `ds` eller `ds.path` objekt som motsvarar en Miljövariabelns namn i formatet `"$AZUREML_DATAREFERENCE_XXXX"` vars värde representerar montering/hämtningssökvägen i den fjärranslutna beräkningen. Datastore-sökväg i den fjärranslutna beräkningen kanske inte är samma som sökväg för körningen för skriptet.

Om du vill få åtkomst till ditt datalager vid träning, skickar du det till dina utbildningsskript som ett argument på kommandoraden via `script_params`:

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
`as_mount()` är standardläget för ett datalager, så du kan också direkt bara skicka `ds` till den `'--data_dir'` argumentet.

Eller skicka in en lista över datalager till konstruktorn kostnadsuppskattning `inputs` parameter för att montera eller kopiera till och från din datalagrets:

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
