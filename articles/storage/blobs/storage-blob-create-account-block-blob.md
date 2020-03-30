---
title: Skapa ett blockbloloblagringskonto – Azure Storage | Microsoft-dokument
description: Visar hur du skapar ett Azure BlockBlobStorage-konto med prestandaegenskaper för premium.
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 6303644ada5c6f093611dba94daf8006f8cc5819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536912"
---
# <a name="create-a-blockblobstorage-account"></a>Skapa ett BlockBlobStorage-konto

Med typen BlockBlobStorage-konto kan du skapa blockblobar med premiumprestandaegenskaper. Den här typen av lagringskonto är optimerat för arbetsbelastningar med hög transaktionsfrekvens eller som kräver mycket snabba åtkomsttider. Den här artikeln visar hur du skapar ett BlockBlobStorage-konto med hjälp av Azure-portalen, Azure CLI eller Azure PowerShell.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

Mer information om BlockBlobStorage-konton finns i [Översikt över Azure storage-konto](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Inga.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Den här how-to-artikeln kräver Azure PowerShell-modulen Az version 1.2.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta din nuvarande version. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Du kan logga in på Azure och köra Azure CLI-kommandon på ett av två sätt:

- Du kan köra CLI-kommandon från Azure-portalen i Azure Cloud Shell.
- Du kan installera CLI och köra CLI-kommandon lokalt.

### <a name="use-azure-cloud-shell"></a>Använda Azure Cloud Shell

Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure Portal. Azure CLI är förinstallerat och konfigurerat för användning med ditt konto. Klicka på knappen **Cloud Shell** på menyn i den övre högra delen av Azure-portalen:

[![Moln skal](../common/media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Knappen startar ett interaktivt skal som du kan använda för att köra stegen i den här artikeln:

[![Skärmbild som visar cloud shell-fönstret i portalen](../common/media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Installera CLI lokalt

Du kan även installera och använda Azure CLI lokalt. Den här how-to-artikeln kräver att du kör Azure CLI version 2.0.46 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). 

---

## <a name="sign-in-to-azure"></a>Logga in på Azure

# <a name="portal"></a>[Portal](#tab/azure-portal)

Logga in på [Azure-portalen](https://portal.azure.com).

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Logga in på din `Connect-AzAccount` Azure-prenumeration med kommandot och följ anvisningarna på skärmen för att autentisera.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill starta Azure Cloud Shell loggar du in på [Azure-portalen](https://portal.azure.com).

Om du vill logga in på din lokala installation av CLI kör du kommandot [az login:](/cli/azure/reference-index#az-login)

```azurecli
az login
```

---

## <a name="create-a-blockblobstorage-account"></a>Skapa ett BlockBlobStorage-konto

## <a name="portal"></a>[Portal](#tab/azure-portal)
Så här skapar du ett BlockBlobStorage-konto i Azure-portalen:

1. I Azure-portalen väljer du **Alla tjänster** > kategorin **Lagring** > **Lagringskonton**.

1. Under **Lagringskonton**väljer du **Lägg till**.

1. I fältet **Prenumeration** väljer du den prenumeration som lagringskontot ska skapas i.

1. I **fältet Resursgrupp** väljer du en befintlig resursgrupp eller väljer **Skapa ny**och anger ett namn för den nya resursgruppen.

1. Ange ett namn för kontot i fältet **Lagringskontonamn.** Observera följande riktlinjer:

   - Namnet måste vara unikt i Azure.
   - Namnet måste vara mellan tre och 24 tecken långt.
   - Namnet kan bara innehålla siffror och gemener.

1. Välj en plats för lagringskontot i fältet **Plats** eller använd standardplatsen.

1. Konfigurera följande för resten av inställningarna:

   |Field     |Värde  |
   |---------|---------|
   |**Prestanda**    |  Välj **Premium**.   |
   |**Typ av konto**    | Välj **BlockBlobStorage**.      |
   |**Replikering**    |  Lämna standardinställningen **för Lokalt redundant lagring (LRS)**.      |

   ![Visar portalgränssnittet för att skapa ett blockblobblagringskonto](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. Välj **Granska + skapa** om du vill granska inställningarna för lagringskontot.

1. Välj **Skapa**.

## <a name="azure-powershell"></a>[Azure Powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Öppna en förhöjd Windows PowerShell-session (Kör som administratör).

1. Kör följande kommando för att kontrollera `Az` att den senaste versionen av PowerShell-modulen är installerad.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. Öppna en ny PowerShell-konsol och logga in med ditt Azure-konto.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. Skapa en ny resursgrupp om det behövs. Ersätt värdena i offerter och kör följande kommando.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. Skapa Kontot BlockBlobStorage. Ersätt värdena i offerter och kör följande kommando.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill skapa ett blockblolobkonto med hjälp av Azure CLI måste du först installera Azure CLI v. 2.0.46 eller en senare version. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

1. Logga in på din Azure-prenumeration.

   ```azurecli
   az login
   ```

1. Skapa en ny resursgrupp om det behövs. Ersätt värdena inom parentes (inklusive hakparenteserna) och kör följande kommando.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. Skapa Kontot BlockBlobStorage. Ersätt värdena inom parentes (inklusive hakparenteserna) och kör följande kommando.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

---

## <a name="next-steps"></a>Nästa steg

- Mer information om lagringskonton finns i [Översikt över Azure storage-konto](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

- Mer information om resursgrupper finns i [översikt över Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
