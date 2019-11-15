---
title: Självstudie – lagra terraform tillstånd i Azure Storage
description: En introduktion till att lagra terraform-tillstånd i Azure Storage.
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 374936c39221d79d59fc8a54dc2bc4a49800240d
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74078565"
---
# <a name="tutorial-store-terraform-state-in-azure-storage"></a>Självstudie: lagra terraform-tillstånd i Azure Storage

Terraform-status används för att stämma av distribuerade resurser med terraform-konfigurationer. Tillstånd gör att terraform vet vilka Azure-resurser som ska läggas till, uppdateras eller tas bort. Som standard lagras terraform-tillstånd lokalt när du kör kommandot `terraform apply`. Den här konfigurationen är inte idealisk av följande anledningar:

- Det lokala läget fungerar inte bra i ett team eller i en samarbets miljö.
- Terraform-tillstånd kan innehålla känslig information.
- Lagrings tillstånd lokalt ökar risken för oavsiktlig borttagning.

Terraform stöder tillstånds beständighet i Fjärrlagring. En sådan backend-server som stöds är Azure Storage. Det här dokumentet visar hur du konfigurerar och använder Azure Storage för detta ändamål.

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

Anteckna lagrings kontots namn, behållar namnet och lagrings åtkomst nyckeln. Dessa värden behövs när du konfigurerar fjärran sluten tillstånd.

## <a name="configure-state-back-end"></a>Konfigurera tillstånds Server delen

Server delen för terraform-tillstånd konfigureras när du kör kommandot `terraform init`. Följande data krävs för att konfigurera tillstånds Server delen:

- **storage_account_name**: namnet på det Azure Storage kontot.
- **container_name**: namnet på BLOB-behållaren.
- **nyckel**: namnet på den tillstånds lager fil som ska skapas.
- **access_key**: lagrings åtkomst nyckeln.

Vart och ett av dessa värden kan anges i konfigurations filen för terraform eller på kommando raden. Vi rekommenderar att du använder en miljö variabel för `access_key` svärdet. Om du använder en miljö variabel förhindrar du att nyckeln skrivs till disk.

Skapa en miljö variabel med namnet `ARM_ACCESS_KEY` med värdet för åtkomst nyckeln Azure Storage.

```bash
export ARM_ACCESS_KEY=<storage access key>
```

Om du vill skydda åtkomst nyckeln för Azure Storage konto måste du lagra den i Azure Key Vault. Miljövariabeln kan sedan anges med hjälp av ett kommando som liknar följande. Mer information om Azure Key Vault finns i Azure Key Vault- [dokumentationen](../key-vault/quick-create-cli.md).

```bash
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

Följande steg måste utföras för att konfigurera terraform så att Server delen används:
- Inkludera ett konfigurations block för `backend` med en typ av `azurerm`.
- Lägg till ett `storage_account_name`-värde i konfigurations blocket.
- Lägg till ett `container_name`-värde i konfigurations blocket.
- Lägg till ett `key`-värde i konfigurations blocket.

I följande exempel konfigureras en terraform-Server del och en Azure-resurs grupp skapas.

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

Initiera konfigurationen genom att utföra följande steg:

1. Kör `terraform init`-kommandot.
1. Kör `terraform apply`-kommandot.

Nu kan du hitta tillstånds filen i Azure Storage-blobben.

## <a name="state-locking"></a>Tillstånds låsning

Azure Storage blobbar låses automatiskt före varje åtgärd som skriver tillstånd. Det här mönstret förhindrar samtidiga tillstånds åtgärder, vilket kan orsaka skada. 

Mer information finns i [tillstånds låsning](https://www.terraform.io/docs/state/locking.html) i terraform-dokumentationen.

Du kan se låset när du undersöker blobben via Azure Portal eller andra hanterings verktyg för Azure.

![Azure Blob med lås](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>Vilande kryptering

Data som lagras i en Azure-Blob krypteras innan de sparas. Vid behov hämtar terraform tillstånd från Server delen och lagrar det i lokalt minne. Med det här mönstret skrivs tillstånd aldrig till din lokala disk.

Mer information om Azure Storage kryptering finns i [Azure Storage tjänst kryptering för vilande data](../storage/common/storage-service-encryption.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Lär dig mer om hur du använder terraform i Azure](/azure/terraform)