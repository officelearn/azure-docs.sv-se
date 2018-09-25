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
ms.openlocfilehash: 615ab592c040eedf7d31e3a3036f558ea6c09740
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990815"
---
# <a name="how-to-access-data-during-training"></a>Hur du kommer åt data vid träning
I Azure Machine Learning services, ett datalager är en abstraktion över [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction). Datalagringen kan referera till antingen en [Azure Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) behållare eller [Azure-filresurs](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) som det underliggande lagringsutrymmet. Datalager kan du enkelt komma åt och interagera med din datalagring under dina Azure Machine Learning-arbetsflöden via Python SDK eller CLI.

## <a name="create-a-datastore"></a>Skapa ett datalager
För att kunna använda datalager måste du först en [arbetsytan](concept-azure-machine-learning-architecture.md#workspace). Du kan skapa en ny arbetsyta, eller så kan du hämta en befintlig:

```Python
import azureml.core
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="use-the-default-datastore"></a>Använd standard-datalager
Varje arbetsyta har ett standard datalager som du kan börja använda direkt, vilket sparar arbetet med att skapa och konfigurera dina egna lagringskonton.

Hämta den arbetsytan standard datalager:
```Python
ds = ws.get_default_datastore()
```

### <a name="register-a-datastore"></a>Registrera ett datalager
Om du redan har befintliga Azure Storage kan registrera du den som ett datalager på din arbetsyta. Azure Machine Learning tillhandahåller funktioner för att registrera en Azure Blob-behållare eller Azure-filresurs som ett datalager. Alla register-metoder som finns på den `Datastore` klassen och har formen `register_azure_*`.

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

För att underlätta för om du vill ange en av dina registrerade datalager som standard-databasen för din arbetsyta kan du göra detta på följande sätt:
```Python
ws.set_default_datastore('your datastore name')
```

## <a name="upload-and-download-data"></a>Ladda upp och hämta data
### <a name="upload"></a>Ladda upp
Du kan överföra en katalog eller enskilda filer till databasen med hjälp av Python-SDK.

Ladda upp en katalog till ett datalager `ds`:
```Python
ds.upload(src_dir='your source directory',
          target_path='your target path',
          overwrite=True,
          show_progress=True)
```
`target_path` Anger var i filresursen (eller blob-behållare) för att ladda upp. Standard `None`, i vilket fall hämtar data som överförs till rot. `overwrite=True` skriver över befintliga data på `target_path`.

Du kan också ladda upp en lista med enskilda filer till databasen via datalagringen `upload_files()` metod.

### <a name="download"></a>Ladda ned
På samma sätt kan hämta du data från ett datalager till det lokala filsystemet.

```Python
ds.download(target_path='your target path',
            prefix='your prefix',
            show_progress=True)
```
`target_path` är platsen för den lokala katalogen för nedladdning av data till. Om du vill ange en sökväg till mappen i filresursen (eller blob-behållare) för att hämta, ange sökvägen till `prefix`. Om `prefix` är `None`, ska ladda ned hela innehållet i filresursen (eller blob-behållare).

## <a name="access-datastores-for-training"></a>Åtkomst till datalager för träning
Du kan komma åt ett datalager under en utbildning som körs (t.ex. för utbildning eller validering data) på en fjärransluten beräkningsmål via Python SDK. 

Det finns två sätt att tillgängliggöra dina datalager i den fjärranslutna beräkningen som stöds:
* **Montera**  
`ds.as_mount()`: genom att ange det här mount-läget kan datalagringen ska monteras åt dig i den fjärranslutna beräkningen. 
* **Ladda ned/laddar upp**  
    * `ds.as_download(path_on_compute='your path on compute')`: med det här läget hämtning data ladda ned från ditt datalager till fjärranslutna beräkningarna till den plats som anges av `path_on_compute`.
    * Omvänt kan du kan också ladda upp data som producerats från utbildning körs ett datalager. Exempel: om din utbildning skriptet skapar en `foo.pkl` fil i den aktuella arbetskatalogen i den fjärranslutna beräkningen du kan ange för den som får överföras till ditt datalager när skriptet har körts: `ds.as_upload(path_on_compute='./foo.pkl')`. Detta kommer att överföra filen i roten för ditt datalager.
    
Om du vill referera till en viss mapp eller fil i ditt datalager, kan du använda datalagringen **`path`** funktion. Exempel: om ditt datalager har en katalog med relativa sökvägen `./bar`, och du bara vill ladda ned innehållet i den här mappen till beräkningsmål-, kan du göra detta på följande sätt: `ds.path('./bar').as_download()`

Alla `ds` eller `ds.path` objekt som motsvarar en Miljövariabelns namn i formatet `"$AZUREML_DATAREFERENCE_XXXX"` vars värde representerar montering/hämtningssökvägen i den fjärranslutna beräkningen. Datastore-sökväg i den fjärranslutna beräkningen kanske inte är samma som sökväg för körningen för skriptet.

För att komma åt ditt datalager vid träning, du kan skicka den till dina utbildningsskript som ett argument på kommandoraden via `script_params`:

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

Du kan också skicka i en lista över datalager till den `inputs` -parametern för konstruktorn kostnadsuppskattning att montera eller kopiera till och från din datalagrets:

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
