---
title: Ändra åtkomstnycklar för lagringskonto
titleSuffix: Azure Machine Learning
description: Läs om hur du ändrar åtkomstnycklarna för Azure Storage-kontot som används av din arbetsyta. Azure Machine Learning använder ett Azure Storage-konto för att lagra data och modeller.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.openlocfilehash: f1541c177cea2d223a5e7df576d95fab7eafb310
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296918"
---
# <a name="regenerate-storage-account-access-keys"></a>Återskapa åtkomstnycklar för lagringskonto
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Lär dig hur du ändrar åtkomstnycklarna för Azure Storage-konton som används av Azure Machine Learning. Azure Machine Learning kan använda lagringskonton för att lagra data eller tränade modeller.

Av säkerhetsskäl kan du behöva ändra åtkomstnycklarna för ett Azure Storage-konto. När du återskapar åtkomstnyckeln måste Azure Machine Learning uppdateras för att den nya nyckeln ska kunna användas. Azure Machine Learning kan använda lagringskontot för både modelllagring och som ett datalager.

## <a name="prerequisites"></a>Krav

* En Azure Machine Learning-arbetsyta. Mer information finns i artikeln [Skapa en arbetsyta.](how-to-manage-workspace.md)

* [Sdk](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)för Azure Machine Learning .

* [Azure Machine Learning CLI-tillägget](reference-azure-machine-learning-cli.md).

> [!NOTE]
> Kodavsnitten i det här dokumentet testades med version 1.0.83 av Python SDK.

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>Vad behöver uppdateras

Lagringskonton kan användas av arbetsytan Azure Machine Learning (lagring av loggar, modeller, ögonblicksbilder osv.) och som ett datalager. Processen för att uppdatera arbetsytan är ett enda Azure CLI-kommando och kan kördes efter uppdatering av lagringsnyckeln. Processen att uppdatera datalager är mer involverad och kräver att du upptäcker vilka datalager som för närvarande använder lagringskontot och sedan registrerar dem igen.

> [!IMPORTANT]
> Uppdatera arbetsytan med Hjälp av Azure CLI och datalager med Python, samtidigt. Det räcker inte att uppdatera det ena eller det andra och kan orsaka fel tills båda uppdateras.

Om du vill identifiera de lagringskonton som används av dina datalager använder du följande kod:

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " +
      default_ds.account_name + ", container name: " + default_ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob":
        print("Blob store - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
    if ds.datastore_type == "AzureFile":
        print("File share - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
```

Den här koden söker efter registrerade datalager som använder Azure Storage och listar följande information:

* Datastore namn: Namnet på det datalager som lagringskontot är registrerat under.
* Namn på lagringskonto: Namnet på Azure Storage-kontot.
* Behållare: Behållaren i lagringskontot som används av den här registreringen.

Det anger också om datalagret är för en Azure Blob eller en Azure File-resurs, eftersom det finns olika metoder för att registrera om varje typ av datalager.

Om det finns en post för det lagringskonto som du planerar att återskapa åtkomstnycklar för sparar du datalagrets namn, lagringskontonamn och behållarnamn.

## <a name="update-the-access-key"></a>Uppdatera åtkomstnyckeln

Så här uppdaterar du Azure Machine Learning för att använda den nya nyckeln:

> [!IMPORTANT]
> Utför alla steg och uppdatera både arbetsytan med HJÄLP av CLI och datalager med Python. Om du bara uppdaterar det ena eller det andra kan det orsaka fel tills båda uppdateras.

1. Återskapa nyckeln. Information om hur du återskapar en åtkomstnyckel finns i [Hantera åtkomstnycklar för lagringskonto](../storage/common/storage-account-keys-manage.md). Spara den nya nyckeln.

1. Så här uppdaterar du arbetsytan så att den nya nyckeln används:

    1. Så här loggar du in på Azure-prenumerationen som innehåller din arbetsyta med hjälp av följande Azure CLI-kommando:

        ```azurecli-interactive
        az login
        ```

        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

    1. Om du vill uppdatera arbetsytan så att den nya nyckeln används använder du följande kommando. Ersätt `myworkspace` med ditt Azure Machine Learning-arbetsytenamn och ersätt `myresourcegroup` med namnet på azure-resursgruppen som innehåller arbetsytan.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

        Det här kommandot synkroniserar automatiskt de nya nycklarna för Azure-lagringskontot som används av arbetsytan.

1. Om du vill registrera om datalager som använder lagringskontot använder du värdena från avsnittet [Vad som behöver uppdateras](#whattoupdate) och nyckeln från steg 1 med följande kod:

    ```python
    # Re-register the blob container
    ds_blob = Datastore.register_azure_blob_container(workspace=ws,
                                              datastore_name='your datastore name',
                                              container_name='your container name',
                                              account_name='your storage account name',
                                              account_key='new storage account key',
                                              overwrite=True)
    # Re-register file shares
    ds_file = Datastore.register_azure_file_share(workspace=ws,
                                          datastore_name='your datastore name',
                                          file_share_name='your container name',
                                          account_name='your storage account name',
                                          account_key='new storage account key',
                                          overwrite=True)
    
    ```

    Sedan `overwrite=True` anges, den här koden skriver över den befintliga registreringen och uppdaterar den för att använda den nya nyckeln.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du registrerar [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) datalager finns i klassreferensen.
