---
title: Självstudiekurs - Lagra terraformtillstånd i Azure Storage
description: En introduktion till lagring av Terraform-tillstånd i Azure Storage.
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: c94c66969c517bd0e51117ab7c6ed6a889149518
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459023"
---
# <a name="tutorial-store-terraform-state-in-azure-storage"></a>Självstudiekurs: Lagra terraformtillstånd i Azure Storage

Terraform-tillstånd används för att stämma av distribuerade resurser med Terraform-konfigurationer. Tillstånd gör att Terraform kan veta vilka Azure-resurser som ska läggas till, uppdatera eller ta bort. Som standard lagras Terraform-tillståndet lokalt när `terraform apply` du kör kommandot. Den här konfigurationen är inte idealisk av följande skäl:

- Lokala staten fungerar inte bra i ett team eller en samarbetsmiljö.
- Terraform-tillstånd kan innehålla känslig information.
- Om du lagrar tillståndet lokalt ökar risken för oavsiktlig borttagning.

Terraform stöder det beständiga tillståndet i fjärrlagring. En sådan back end som stöds är Azure Storage. Det här dokumentet visar hur du konfigurerar och använder Azure Storage för detta ändamål.

## <a name="configure-storage-account"></a>Konfigurera lagringskonto

Innan du använder Azure Storage som en back end måste du skapa ett lagringskonto. Lagringskontot kan skapas med Azure-portalen, PowerShell, Azure CLI eller Terraform själv. Använd följande exempel för att konfigurera lagringskontot med Azure CLI.

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

Ta del av lagringskontots namn, behållarnamn och åtkomstnyckel för lagring. Dessa värden behövs när du konfigurerar fjärrtillståndet.

## <a name="configure-state-back-end"></a>Konfigurera tillståndsbakslut

Terraform-tillståndsryggen konfigureras när `terraform init` du kör kommandot. Följande data behövs för att konfigurera tillståndsbaksluten:

- **storage_account_name**: Namnet på Azure Storage-kontot.
- **container_name**: Namnet på blob-behållaren.
- **nyckel**: Namnet på den tillståndslagringsfil som ska skapas.
- **access_key**: Åtkomstnyckeln för lagring.

Vart och ett av dessa värden kan anges i konfigurationsfilen för Terraform eller på kommandoraden. Vi rekommenderar att du använder `access_key` en miljövariabel för värdet. Om du använder en miljövariabel kan nyckeln skrivas till disken.

Skapa en miljövariabel med namnet `ARM_ACCESS_KEY` med värdet för Azure Storage-åtkomstnyckeln.

```bash
export ARM_ACCESS_KEY=<storage access key>
```

Om du vill skydda åtkomstnyckeln för Azure Storage-kontot ytterligare lagrar du den i Azure Key Vault. Miljövariabeln kan sedan ställas in med hjälp av ett kommando som liknar följande. Mer information om Azure Key Vault finns i [dokumentationen till Azure Key Vault](../key-vault/secrets/quick-create-cli.md).

```bash
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

För att konfigurera Terraform för att använda serverdelen måste följande steg göras:
- Inkludera `backend` ett konfigurationsblock `azurerm`med en typ av .
- Lägg `storage_account_name` till ett värde i konfigurationsblocket.
- Lägg `container_name` till ett värde i konfigurationsblocket.
- Lägg `key` till ett värde i konfigurationsblocket.

I följande exempel konfigureras en Terraform-serverdel och en Azure-resursgrupp skapas.

```hcl
terraform {
  backend "azurerm" {
    resource_group_name   = "tstate"
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

Initiera konfigurationen genom att göra följande steg:

1. Kör `terraform init`-kommandot.
1. Kör `terraform apply`-kommandot.

Du kan nu hitta tillståndsfilen i Azure Storage-bloben.

## <a name="state-locking"></a>Tillståndslåsning

Azure Storage-blobbar låses automatiskt före en åtgärd som skriver tillstånd. Det här mönstret förhindrar samtidiga tillståndsåtgärder, vilket kan orsaka skador. 

Mer information finns i [Tillståndslåsning](https://www.terraform.io/docs/state/locking.html) i Terraform-dokumentationen.

Du kan se låset när du undersöker blobben via Azure-portalen eller andra Azure-hanteringsverktyg.

![Azure-blob med lås](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>Vilande kryptering

Data som lagras i en Azure-blob krypteras innan de sparas. Vid behov hämtar Terraform tillståndet från baksidan och lagrar det i lokalt minne. Med det här mönstret skrivs tillstånd aldrig till den lokala disken.

Mer information om Azure Storage-kryptering finns i [Azure Storage-tjänstkryptering för data i vila](../storage/common/storage-service-encryption.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Läs mer om hur du använder Terraform i Azure](/azure/terraform)
