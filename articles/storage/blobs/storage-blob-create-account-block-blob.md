---
title: Skapa ett Block Blob Storage-konto – Azure Storage | Microsoft Docs
description: Visar hur du skapar ett Azure BlockBlobStorage-konto med förstklassiga prestanda egenskaper.
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 1df1d5180d951e7a720ec82c548438892a47a426
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72881870"
---
# <a name="create-a-blockblobstorage-account"></a>Skapa ett BlockBlobStorage-konto

Med BlockBlobStorage-kontots typ kan du skapa block-blobbar med förstklassiga prestanda egenskaper. Den här typen av lagrings konto är optimerad för arbets belastningar med höga transaktions priser eller som kräver mycket snabba åtkomst tider. Den här artikeln visar hur du skapar ett BlockBlobStorage-konto med hjälp av Azure Portal, Azure CLI eller Azure PowerShell.

Mer information om BlockBlobStorage-konton finns i [Översikt över Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

## <a name="portaltabazure-portal"></a>[Portalen](#tab/azure-portal)
Följ dessa steg om du vill skapa ett BlockBlobStorage-konto i Azure Portal:

1. I Azure Portal väljer du **alla tjänster** > **lagrings** kategorin > **lagrings konton**.

1. Under **lagrings konton**väljer du **Lägg till**.

1. I fältet **prenumeration** väljer du den prenumeration där du vill skapa lagrings kontot.

1. I fältet **resurs grupp** väljer du en befintlig resurs grupp eller väljer **Skapa ny**, och anger ett namn för den nya resurs gruppen.

1. I fältet **namn på lagrings konto** anger du ett namn för kontot. Observera följande rikt linjer:

   - Namnet måste vara unikt i Azure.
   - Namnet måste innehålla mellan tre och 24 tecken.
   - Namnet får bara innehålla siffror och gemena bokstäver.

1. I fältet **plats** väljer du en plats för lagrings kontot eller använder standard platsen.

1. Konfigurera följande för resten av inställningarna:

   |Fält     |Värde  |
   |---------|---------|
   |**Prestanda**    |  Välj **Premium**.   |
   |**Typ av konto**    | Välj **BlockBlobStorage**.      |
   |**Replikering**    |  Lämna standardinställningen för **Lokalt Redundant lagring (LRS)** .      |

   ![Visar Portal gränssnittet för att skapa ett Block Blob Storage-konto](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. Välj **Granska + skapa** för att granska inställningarna för lagrings kontot.

1. Välj **Skapa**.

## <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Öppna en upphöjd Windows PowerShell-session (kör som administratör).

1. Kör följande kommando för att kontrol lera att den senaste versionen av `Az` PowerShell-modulen är installerad.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. Öppna en ny PowerShell-konsol och logga in med ditt Azure-konto.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. Om det behövs skapar du en ny resurs grupp. Ersätt värdena i citat tecken och kör följande kommando.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. Skapa BlockBlobStorage-kontot. Ersätt värdena i citat tecken och kör följande kommando.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill skapa ett block-BLOB-konto med hjälp av Azure CLI måste du först installera Azure CLI v. 2.0.46 eller en senare version. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

1. Logga in på din Azure-prenumeration.

   ```azurecli
   az login
   ```

1. Om det behövs skapar du en ny resurs grupp. Ersätt värdena inom hakparenteser (inklusive hakparenteser) och kör följande kommando.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. Skapa BlockBlobStorage-kontot. Ersätt värdena inom hakparenteser (inklusive hakparenteser) och kör följande kommando.

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
