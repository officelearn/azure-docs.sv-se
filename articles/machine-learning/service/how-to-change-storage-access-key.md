---
title: Ändra åtkomstnycklar för lagringskontot
titleSuffix: Azure Machine Learning service
description: Lär dig hur du ändrar åtkomstnycklarna för Azure Storage-kontot som används av din arbetsyta. Azure Machine Learning-tjänsten använder ett Azure Storage-konto för att lagra data och modeller. När du återskapar åtkomstnyckeln för lagringskontot måste du uppdatera Azure Machine Learning-tjänsten om du vill använda de nya nycklarna.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 05/17/2019
ms.openlocfilehash: 11abbb0494a9771676dcf2436b5e808346c16368
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078565"
---
# <a name="regenerate-storage-account-access-keys"></a>Återskapa åtkomstnycklar för lagringskontot

Lär dig hur du ändrar åtkomstnycklarna för Azure Storage-konton som används av Azure Machine Learning-tjänsten. Azure Machine Learning kan använda storage-konton för att lagra data eller anpassade modeller.

Av säkerhetsskäl kan du behöva ändra åtkomstnycklarna för ett Azure Storage-konto. När du återskapar åtkomstnyckeln måste du uppdatera Azure Machine Learning för att använda den nya nyckeln. Azure Machine Learning kanske använder storage-konto för båda modell-lagring och som ett datalager.

## <a name="prerequisites"></a>Nödvändiga komponenter

* En arbetsyta för Azure Machine Learning-tjänsten. Mer information finns i den [skapa en arbetsyta](setup-create-workspace.md) artikeln.

* The [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* Den [Azure Machine Learning CLI-tillägget](reference-azure-machine-learning-cli.md).

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>Vad behöver uppdateras

Storage-konton kan användas av arbetsytan Azure Machine Learning-tjänsten (lagra loggar, modeller, ögonblicksbilder, etc.) och som ett datalager. Processen för att uppdatera arbetsytan är en enda Azure CLI-kommando och kan vara kördes när du har uppdaterat lagringsnyckeln. Processen för att uppdatera datalager komplicerad mer och kräver att identifiera vilka datalager för närvarande använder storage-konto och registrera dem igen.

> [!IMPORTANT]
> Uppdatera arbetsytan med Azure CLI och datalagringen med hjälp av Python, på samma gång. Uppdaterar ena eller den andra räcker inte och kan orsaka fel förrän båda har uppdaterats.

För att identifiera de lagringskonton som används av ditt datalager, använder du följande kod:

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " + default_ds.account_name + ", container name: " + ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob" or ds.datastore_type == "AzureFile":
        print("datastore name: " + name + ", storage account name: " + ds.account_name + ", container name: " + ds.container_name)
```

Den här koden söker efter alla registrerade datalagringarna som använder Azure Storage och visar följande information:

* Namn på datalager: Namnet på databasen som storage-kontot är registrerat under.
* Lagringskontots namn: Namnet på Azure Storage-kontot.
* Behållare: Behållare i lagringskontot som används av denna registrering.

Om det finns en post för det lagringskonto som du planerar att återskapa åtkomstnycklarna för, spara datalager, lagringskontonamn och namn på behållare.

## <a name="update-the-access-key"></a>Uppdatera åtkomstnyckeln

Använd följande steg för att uppdatera Azure Machine Learning-tjänsten så att den nya nyckeln:

> [!IMPORTANT]
> Utföra alla steg som uppdaterar både arbetsytan med CLI och datalager med hjälp av Python. Kan orsaka fel om du uppdaterar ena eller den andra förrän båda har uppdaterats.

1. Återskapa nyckeln. Information om en åtkomstnyckeln finns i den [hantera ett lagringskonto](/azure/storage/common/storage-account-manage.md#access-keys) artikeln. Spara den nya nyckeln.

1. Använd följande steg för att uppdatera arbetsytan om du vill använda den nya nyckeln:

    1. Logga in på Azure-prenumerationen som innehåller din arbetsyta med hjälp av följande Azure CLI-kommando:

        ```azurecli-interactive
        az login
        ```

    1. Om du vill installera tillägget Azure Machine Learning, använder du följande kommando:

        ```azurecli-interactive
        az extension add -n azure-cli-ml 
        ```

    1. Använd följande kommando för att uppdatera arbetsytan om du vill använda den nya nyckeln. Ersätt `myworkspace` med namnet för Azure Machine Learning-arbetsytan och Ersätt `myresourcegroup` med namnet på Azure-resursgrupp som innehåller arbetsytan.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        Det här kommandot synkroniseras automatiskt nya nycklar för Azure storage-kontot som används av arbetsytan.

1. Om du vill Omregistrera datalagrets som använder storage-konto, använder du värden från den [vad som behöver uppdateras](#whattoupdate) avsnittet och nyckeln från steg 1 med följande kod:

    ```python
    ds = Datastore.register_azure_blob_container(workspace=ws, 
                                              datastore_name='your datastore name', 
                                              container_name='your container name',
                                              account_name='your storage account name', 
                                              account_key='new storage account key',
                                              overwrite=True)
    ```

    Eftersom `overwrite=True` anges den här koden skriver över den befintliga registreringen och uppdaterar den om du vill använda den nya nyckeln.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du registrerar datalager finns i den [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) klassen referens.
