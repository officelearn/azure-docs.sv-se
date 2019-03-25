---
title: Skapa ett block blob storage-konto – Azure Storage | Microsoft Docs
description: Visar hur du skapar ett Azure block blob storage-konto med premium prestandaegenskaper.
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
author: twooley
ms.author: twooley
ms.subservice: blobs
ms.openlocfilehash: f4d3e3ad923b6a603902bc007107cb41dae8cf6c
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400471"
---
# <a name="create-a-block-blob-storage-account"></a>Skapa ett block blob storage-konto

Kontotypen för block blob storage kan du skapa blockblob-objekt med egenskaper för premium-prestanda. Den här typen av lagringskonto är optimerad för arbetsbelastningar med hög transaktioner eller som kräver mycket snabbt åtkomsttider. Den här artikeln visar hur du skapar ett block blob storage-konto med hjälp av Azure-portalen, Azure CLI eller Azure PowerShell.

Mer information om block blob storage-konton finns i [översikt över Azure storage-konton](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

## <a name="create-account-in-the-azure-portal"></a>Skapa kontot i Azure portal

Följ dessa steg om du vill skapa ett block blob storage-konto i Azure portal:

1. I Azure-portalen väljer du **alla tjänster** > den **Storage** kategori > **lagringskonton**.

1. Under **lagringskonton**väljer **Lägg till**.

1. I den **prenumeration** fältet, Välj den prenumeration som du vill skapa lagringskontot.

1. I den **resursgrupp** fältet väljer du en befintlig resursgrupp eller **Skapa nytt**, och ange ett namn för den nya resursgruppen.

1. I den **lagringskontonamn** fältet, anger du ett namn för kontot. Observera följande riktlinjer:

   - Namnet måste vara unikt i Azure.
   - Namnet måste vara mellan 3 och 24 tecken långt.
   - Namnet kan innehålla endast siffror och gemener.

1. I den **plats** fältet, Välj en plats för lagringskontot eller Använd standardplatsen.

1. I resten av inställningarna för att konfigurera följande:

   |Fält     |Värde  |
   |---------|---------|
   |**Prestanda**    |  Välj **Premium**.   |
   |**Typ av konto**    | Select **BlockBlobStorage**.      |
   |**Replikering**    |  Lämna standardinställningen för **lokalt redundant lagring (LRS)**.      |

   ![Visar portalens användargränssnitt för att skapa ett block blob storage-konto](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. Välj **granska + skapa** att granska inställningarna för lagringskontot.

1. Välj **Skapa**.

## <a name="create-account-using-azure-powershell"></a>Skapa konto med hjälp av Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Öppna en upphöjd Windows PowerShell-session (Kör som administratör).

1. Kör följande kommando för att se till att den senaste versionen av den `Az` PowerShell-modulen är installerad.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. Öppna en ny PowerShell-konsolen och logga in med ditt Azure-konto.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. Om det behövs kan du skapa en ny resursgrupp. Ersätt värdena i offerter och kör följande kommando.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. Skapa block blob storage-konto. Ersätt värdena i offerter och kör följande kommando.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="create-account-using-azure-cli"></a>Skapa konto med Azure CLI

Om du vill skapa ett block blob-konto med hjälp av Azure CLI, måste du först installera Azure CLI v. 2.0.46 eller en senare version. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

1. Logga in på Azure-prenumerationen.

   ```azurecli
   az login
   ```

1. Om det behövs kan du skapa en ny resursgrupp. Ersätt värden inom hakparentes (inklusive hakparenteserna) och kör följande kommando.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. Skapa block blob storage-konto. Ersätt värden inom hakparentes (inklusive hakparenteserna) och kör följande kommando.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

## <a name="next-steps"></a>Nästa steg

- Mer information om lagringskonton finns i [kontoöversikten för Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

- Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
