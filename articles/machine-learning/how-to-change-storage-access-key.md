---
title: Ändra åtkomst nycklar för lagrings kontot
titleSuffix: Azure Machine Learning
description: Lär dig hur du ändrar åtkomst nycklarna för det Azure Storage konto som används av din arbets yta. Azure Machine Learning använder ett Azure Storage konto för att lagra data och modeller.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 11/06/2019
ms.openlocfilehash: becb05616ecac51fa4646836a6b1a2f3b71d3543
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2019
ms.locfileid: "75540846"
---
# <a name="regenerate-storage-account-access-keys"></a>Återskapa åtkomst nycklar för lagrings kontot
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Lär dig hur du ändrar åtkomst nycklarna för Azure Storage konton som används av Azure Machine Learning. Azure Machine Learning kan använda lagrings konton för att lagra data eller utbildade modeller.

Av säkerhets synpunkt kan du behöva ändra åtkomst nycklarna för ett Azure Storage-konto. När du återskapar åtkomst nyckeln måste Azure Machine Learning uppdateras för att använda den nya nyckeln. Azure Machine Learning kan använda lagrings kontot för både modell lagring och som ett data lager.

## <a name="prerequisites"></a>Krav

* En Azure Machine Learning-arbetsyta. Mer information finns i artikeln [skapa en arbets yta](how-to-manage-workspace.md) .

* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* [Azure Machine Learning CLI-tillägget](reference-azure-machine-learning-cli.md).

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>Vad som behöver uppdateras

Lagrings konton kan användas av Azure Machine Learning arbets ytan (lagrings loggar, modeller, ögonblicks bilder osv.) och som ett data lager. Processen för att uppdatera arbets ytan är ett enda Azure CLI-kommando och kan köras efter uppdatering av lagrings nyckeln. Processen för att uppdatera data lager är mer involverad och du måste identifiera vilka data lager som för närvarande använder lagrings kontot och sedan registrera dem på nytt.

> [!IMPORTANT]
> Uppdatera arbets ytan med hjälp av Azure CLI och data lagret med python, på samma tidpunkt. Uppdatering av endast en eller flera är inte tillräckligt och kan orsaka fel tills båda uppdateras.

Använd följande kod för att identifiera de lagrings konton som används av dina data lager:

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

Den här koden söker efter registrerade data lager som använder Azure Storage och visar följande information:

* Data lager namn: namnet på det data lager som lagrings kontot är registrerat under.
* Lagrings konto namn: namnet på Azure Storage kontot.
* Container: den behållare i lagrings kontot som används vid registreringen.

Det visar också om data lagret är för en Azure-Blob eller en Azure-filresurs eftersom det finns olika metoder för att omregistrera varje typ av data lager.

Om det finns en post för det lagrings konto som du planerar att återskapa åtkomst nycklar för, sparar du data lagrings namnet, lagrings kontots namn och behållar namnet.

## <a name="update-the-access-key"></a>Uppdatera åtkomst nyckeln

Om du vill uppdatera Azure Machine Learning att använda den nya nyckeln använder du följande steg:

> [!IMPORTANT]
> Utför alla steg, uppdatera både arbets ytan med hjälp av CLI och data lager med python. Uppdatering av bara en av dem kan orsaka fel tills båda uppdateras.

1. Återskapa nyckeln. Information om hur du återskapar en åtkomst nyckel finns i [Hantera åtkomst nycklar för lagrings kontot](../storage/common/storage-account-keys-manage.md). Spara den nya nyckeln.

1. Använd följande steg för att uppdatera arbets ytan till att använda den nya nyckeln:

    1. Logga in på Azure-prenumerationen som innehåller arbets ytan med hjälp av följande Azure CLI-kommando:

        ```azurecli-interactive
        az login
        ```

    1. Använd följande kommando för att uppdatera arbets ytan för att använda den nya nyckeln. Ersätt `myworkspace` med namnet på din Azure Machine Learning arbets yta och ersätt `myresourcegroup` med namnet på den Azure-resurs grupp som innehåller arbets ytan.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

        Det här kommandot synkroniserar automatiskt de nya nycklarna för Azure Storage-kontot som används av arbets ytan.

1. Om du vill registrera data lager på nytt som använder lagrings kontot använder du värdena från avsnittet [vad som behöver uppdateras](#whattoupdate) och nyckeln från steg 1 med följande kod:

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

    Eftersom `overwrite=True` anges skriver den här koden över den befintliga registreringen och uppdaterar den för att använda den nya nyckeln.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du registrerar data lager finns i referens för [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) -klassen.
