---
title: Ändra åtkomst nycklar för lagrings kontot
titleSuffix: Azure Machine Learning service
description: Lär dig hur du ändrar åtkomst nycklarna för det Azure Storage konto som används av din arbets yta. Azure Machine Learning tjänsten använder ett Azure Storage konto för att lagra data och modeller. När du återskapar åtkomst nyckeln för lagrings kontot måste du uppdatera Azure Machine Learning-tjänsten för att använda de nya nycklarna.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/08/2019
ms.openlocfilehash: 7c6b85bd1f5935fb3722f82efcdfc921fc9cb2ec
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990544"
---
# <a name="regenerate-storage-account-access-keys"></a>Återskapa åtkomst nycklar för lagrings kontot

Lär dig hur du ändrar åtkomst nycklarna för Azure Storage konton som används av Azure Machine Learnings tjänsten. Azure Machine Learning kan använda lagrings konton för att lagra data eller utbildade modeller.

Av säkerhets synpunkt kan du behöva ändra åtkomst nycklarna för ett Azure Storage-konto. När du återskapar åtkomst nyckeln måste Azure Machine Learning uppdateras för att använda den nya nyckeln. Azure Machine Learning kan använda lagrings kontot för både modell lagring och som ett data lager.

## <a name="prerequisites"></a>Förutsättningar

* En arbetsyta för Azure Machine Learning-tjänsten. Mer information finns i artikeln [skapa en arbets yta](how-to-manage-workspace.md) .

* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* [Azure Machine Learning CLI-tillägget](reference-azure-machine-learning-cli.md).

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>Vad som behöver uppdateras

Lagrings konton kan användas av Azure Machine Learning service-arbetsytan (lagrings loggar, modeller, ögonblicks bilder osv.) och som ett data lager. Processen för att uppdatera arbets ytan är ett enda Azure CLI-kommando och kan köras efter uppdatering av lagrings nyckeln. Processen för att uppdatera data lager är mer involverad och du måste identifiera vilka data lager som för närvarande använder lagrings kontot och sedan registrera dem på nytt.

> [!IMPORTANT]
> Uppdatera arbets ytan med hjälp av Azure CLI och data lagret med python, på samma tidpunkt. Uppdatering av endast en eller flera är inte tillräckligt och kan orsaka fel tills båda uppdateras.

Använd följande kod för att identifiera de lagrings konton som används av dina data lager:

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " +
      default_ds.account_name + ", container name: " + ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob" or ds.datastore_type == "AzureFile":
        print("datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
```

Den här koden söker efter registrerade data lager som använder Azure Storage och visar följande information:

* Data lager namn: Namnet på det data lager som lagrings kontot är registrerat under.
* Lagringskontots namn: Namnet på Azure Storage kontot.
* Fönster Den behållare i lagrings kontot som används vid registreringen.

Om det finns en post för det lagrings konto som du planerar att återskapa åtkomst nycklar för, sparar du data lagrings namnet, lagrings kontots namn och behållar namnet.

## <a name="update-the-access-key"></a>Uppdatera åtkomst nyckeln

Använd följande steg för att uppdatera Azure Machine Learning-tjänsten för att använda den nya nyckeln:

> [!IMPORTANT]
> Utför alla steg, uppdatera både arbets ytan med hjälp av CLI och data lager med python. Uppdatering av bara en av dem kan orsaka fel tills båda uppdateras.

1. Återskapa nyckeln. Information om hur du återskapar en åtkomst nyckel finns i artikeln [hantera ett lagrings konto](/azure/storage/common/storage-account-manage#access-keys) . Spara den nya nyckeln.

1. Använd följande steg för att uppdatera arbets ytan till att använda den nya nyckeln:

    1. Logga in på Azure-prenumerationen som innehåller arbets ytan med hjälp av följande Azure CLI-kommando:

        ```azurecli-interactive
        az login
        ```

    1. Använd följande kommando för att uppdatera arbets ytan för att använda den nya nyckeln. Ersätt `myworkspace` med namnet på din Azure Machine Learning arbets yta och `myresourcegroup` Ersätt med namnet på den Azure-resurs grupp som innehåller arbets ytan.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

        Det här kommandot synkroniserar automatiskt de nya nycklarna för Azure Storage-kontot som används av arbets ytan.

1. Om du vill registrera data lager på nytt som använder lagrings kontot använder du värdena från avsnittet [vad som behöver uppdateras](#whattoupdate) och nyckeln från steg 1 med följande kod:

    ```python
    ds = Datastore.register_azure_blob_container(workspace=ws, 
                                              datastore_name='your datastore name', 
                                              container_name='your container name',
                                              account_name='your storage account name', 
                                              account_key='new storage account key',
                                              overwrite=True)
    ```

    Eftersom `overwrite=True` har angetts skriver den här koden över den befintliga registreringen och uppdaterar den för att använda den nya nyckeln.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du registrerar data lager finns i [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) klass referensen.
