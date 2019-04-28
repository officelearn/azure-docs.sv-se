---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 44ee258567ca357687feb24337f2d5974e2532b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60392808"
---
## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en Azure-resursgrupp med kommandot [az group create](/cli/azure/group). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Skapa ett allmänt lagringskonto med kommandot [az storage account create](/cli/azure/storage/account). Det allmänna lagringskontot kan användas för alla fyra tjänsterna: blobar, filer, tabeller och köer. 

```azurecli-interactive
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --sku Standard_LRS \
    --encryption blob
```

## <a name="specify-storage-account-credentials"></a>Ange autentiseringsuppgifterna för lagringskontot

Azure CLI behöver dina autentiseringsuppgifter för lagringskontot för de flesta kommandona i den här självstudien. Det finns flera alternativ för att göra det, och ett av de enklaste sätten att ange dem är att ställa in miljövariablerna `AZURE_STORAGE_ACCOUNT` och `AZURE_STORAGE_KEY`.

Visa först dina lagringskontonycklar med kommandot [az storage account keys list](/cli/azure/storage/account/keys):

```azurecli-interactive
az storage account keys list \
    --account-name mystorageaccount \
    --resource-group myResourceGroup \
    --output table
```

Ange nu miljövariablerna `AZURE_STORAGE_ACCOUNT` och `AZURE_STORAGE_KEY`. Det kan du göra med Bash-gränssnittet med kommandot `export`:

```bash
export AZURE_STORAGE_ACCOUNT="mystorageaccountname"
export AZURE_STORAGE_KEY="myStorageAccountKey"
```
