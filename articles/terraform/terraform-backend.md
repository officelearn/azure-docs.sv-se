---
title: Använd Azure Storage som terraform-backend
description: En introduktion till att lagra terraform-tillstånd i Azure Storage.
services: terraform
author: tomarchermsft
ms.service: azure
ms.topic: article
ms.date: 09/20/2019
ms.author: tarcher
ms.openlocfilehash: e9b447f4f4dc9d0ee090da9729e483cc17ac7c15
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169940"
---
# <a name="store-terraform-state-in-azure-storage"></a>Lagra terraform-tillstånd i Azure Storage

Terraform-status används för att stämma av distribuerade resurser med terraform-konfigurationer. Med hjälp av State vet terraform vilka Azure-resurser som ska läggas till, uppdateras eller tas bort. Som standard lagras terraform-tillstånd lokalt när du kör *terraform Apply*. Den här konfigurationen är inte idealisk av några anledningar:

- Lokalt tillstånd fungerar inte bra i ett team eller en samarbets miljö
- Terraform-tillstånd kan innehålla känslig information
- Lagrings tillstånd lokalt ökar risken för oavsiktlig borttagning

Terraform innehåller konceptet för en tillstånds Server del, som är Fjärrlagring för terraform-tillstånd. När du använder en tillstånds Server del lagras tillstånds filen i ett data lager, till exempel Azure Storage. Det här dokumentet innehåller information om hur du konfigurerar och använder Azure Storage som terraform tillstånds Server.

## <a name="configure-storage-account"></a>Konfigurera lagrings konto

Innan du använder Azure Storage som Server del måste du skapa ett lagrings konto. Lagrings kontot kan skapas med Azure Portal, PowerShell, Azure CLI eller terraform. Använd följande exempel för att konfigurera lagrings kontot med Azure CLI.

```azurecli
#!/bin/bash

RESOURCE_GROUP_NAME=tstate
STORAGE_ACCOUNT_NAME=tstate$RANDOM
CONTAINER_NAME=tstate

# Create resource group
az group create --name $RESOURCE_GROUP_NAME --location eastus

# Create storage account
az storage account create --resource-group $RESOURCE_GROUP_NAME --name $STORAGE_ACCOUNT_NAME --sku Standard_LRS --encryption-services blob

# Get storage account key
ACCOUNT_KEY=$(az storage account keys list --resource-group $RESOURCE_GROUP_NAME --account-name $STORAGE_ACCOUNT_NAME --query [0].value -o tsv)

# Create blob container
az storage container create --name $CONTAINER_NAME --account-name $STORAGE_ACCOUNT_NAME --account-key $ACCOUNT_KEY

echo "storage_account_name: $STORAGE_ACCOUNT_NAME"
echo "container_name: $CONTAINER_NAME"
echo "access_key: $ACCOUNT_KEY"
```

Anteckna lagrings kontots namn, behållar namnet och lagrings åtkomst nyckeln. De här värdena behövs när du konfigurerar fjärran slutet.

## <a name="configure-state-backend"></a>Konfigurera tillstånds Server del

Den terraform tillstånds Server delen konfigureras när du kör *terraform init*. Följande data krävs för att du ska kunna konfigurera tillstånds Server delen.

- storage_account_name – namnet på Azure Storage kontot.
- container_name – namnet på BLOB-behållaren.
- Key – namnet på den tillstånds lager fil som ska skapas.
- access_key – lagrings åtkomst nyckeln.

Vart och ett av dessa värden kan anges i konfigurations filen för terraform eller på kommando raden, men vi rekommenderar att du använder en miljö variabel för `access_key`. Om du använder en miljö variabel förhindrar du att nyckeln skrivs till disk.

Skapa en miljö variabel med `ARM_ACCESS_KEY` namnet med värdet för Azure Storage åtkomst nyckel.

```bash
export ARM_ACCESS_KEY=<storage access key>
```

Om du vill skydda åtkomst nyckeln för Azure Storage konto måste du lagra den i Azure Key Vault. Miljövariabeln kan sedan anges med ett kommando som liknar följande. Mer information om Azure Key Vault finns i Azure Key Vault- [dokumentationen][azure-key-vault].

```bash
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

Om du vill konfigurera terraform för att använda Server delen inkluderar du en *Server dels* konfiguration med en typ av *azurerm* inuti terraform-konfigurationen. Lägg till värdena *storage_account_name*, *container_name*och *Key* i konfigurations blocket.

I följande exempel konfigureras en terraform-Server del och en Azure-resurs grupp skapas. Ersätt värdena med värden från din miljö.

```hcl
terraform {
  backend "azurerm" {
    storage_account_name  = "tstate09762"
    container_name        = "tstate"
    key                   = "terraform.tfstate"
  }
}

resource "azurerm_resource_group" "state-demo-secure" {
  name     = "state-demo"
  location = "eastus"
}
```

Initiera nu konfigurationen med *terraform init* och kör sedan konfigurationen med *terraform Apply*. När du är klar kan du hitta tillstånds filen i Azure Storage Blob.

## <a name="state-locking"></a>Tillstånds låsning

När du använder en Azure Storage Blob för tillstånds lagring låses bloben automatiskt före varje åtgärd som skriver tillstånd. Den här konfigurationen förhindrar flera samtidiga tillstånds åtgärder, vilket kan orsaka skada. Mer information finns i [tillstånds låsning][terraform-state-lock] i terraform-dokumentationen.

Du kan se låset när du undersöker blobben via Azure Portal eller andra hanterings verktyg för Azure.

![Azure Blob med lås](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>Vilande kryptering

Som standard krypteras data som lagras i en Azure-Blob innan de sparas i lagrings infrastrukturen. När terraform behöver status hämtas den från Server delen och lagras i minnet i utvecklings systemet. I den här konfigurationen skyddas tillstånd i Azure Storage och skrivs inte till din lokala disk.

Mer information om Azure Storage kryptering finns i [Azure Storage tjänst kryptering för vilande data][azure-storage-encryption].

## <a name="next-steps"></a>Nästa steg

Läs mer om terraform-backend-konfiguration i [terraform-backend-dokumentationen][terraform-backend].

<!-- LINKS - internal -->
[azure-key-vault]: ../key-vault/quick-create-cli.md
[azure-storage-encryption]: ../storage/common/storage-service-encryption.md

<!-- LINKS - external -->
[terraform-azurerm]: https://www.terraform.io/docs/backends/types/azurerm.html
[terraform-backend]: https://www.terraform.io/docs/backends/
[terraform-state-lock]: https://www.terraform.io/docs/state/locking.html
