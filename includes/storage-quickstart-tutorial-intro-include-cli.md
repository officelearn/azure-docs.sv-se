---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.openlocfilehash: 4ad977dc8cbaa85360092dbfd391a3c3b88f67bb
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747915"
---
## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en Azure-resursgrupp med kommandot [az group create](/cli/azure/group). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

```azurecli-interactive
az group create \
    --name <resource-group-name> \
    --location <location>
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Skapa ett allmänt lagringskonto med kommandot [az storage account create](/cli/azure/storage/account). Det allmänna lagringskontot kan användas för alla fyra tjänsterna: blobar, filer, tabeller och köer.

Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group <resource-group-name> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption blob
```

## <a name="specify-storage-account-credentials"></a>Ange autentiseringsuppgifterna för lagringskontot

Azure CLI behöver dina autentiseringsuppgifter för lagringskontot för de flesta kommandona i den här självstudien. Det finns flera alternativ för att göra det, och ett av de enklaste sätten att ange dem är att ställa in miljövariablerna `AZURE_STORAGE_ACCOUNT` och `AZURE_STORAGE_KEY`.

> [!NOTE]
> Den här artikeln visar hur du ställer in miljövariabler med bash. Andra miljöer kan kräva modifiering av syntaxen.

Börja med att visa dina lagrings konto nycklar med hjälp av kommandot [AZ Storage Account Keys List](/cli/azure/storage/account/keys) . Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

```azurecli-interactive
az storage account keys list \
    --account-name <account-name> \
    --resource-group <resource-group-name> \
    --output table
```

Ange nu miljövariablerna `AZURE_STORAGE_ACCOUNT` och `AZURE_STORAGE_KEY`. Du kan göra detta i bash-gränssnittet med hjälp av kommandot `export`. Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

```bash
export AZURE_STORAGE_ACCOUNT="<account-name>"
export AZURE_STORAGE_KEY="<account-key>"
```

Mer information om hur du hämtar åtkomst nycklar för kontot med hjälp av Azure Portal finns i **åtkomst nycklar** i [Hantera inställningar för lagrings konton i Azure Portal](../articles/storage/common/storage-account-manage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#access-keys).