---
title: Konfigurera Virtuella Azure Key Vault för virtuella Linux-datorer
description: Konfigurera Key Vault för användning med en virtuell Azure Resource Manager-dator med Azure CLI.
author: mimckitt
manager: vashan
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/24/2017
ms.author: mimckitt
ms.openlocfilehash: 59b11d22f054a98fe176e4393843606bd01cc872
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879487"
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli"></a>Konfigurera Key Vault för virtuella datorer med Azure CLI

I Azure Resource Manager-stacken modelleras hemligheter/certifikat som resurser som tillhandahålls av Key Vault. Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](../../key-vault/key-vault-overview.md) För att Key Vault ska kunna användas med virtuella Azure Resource Manager-datorer måste egenskapen *EnabledForDeployment* i Key Vault anges till true. Den här artikeln visar hur du konfigurerar Key Vault för användning med virtuella Azure-datorer (VMs) med Hjälp av Azure CLI. 

För att kunna utföra dessa steg behöver du den senaste [Azure CLI](/cli/azure/install-az-cli2) installerad och inloggad på ett Azure-konto med [az-inloggning](/cli/azure/reference-index).

## <a name="create-a-key-vault"></a>Skapa en Key Vault-lösning
Skapa ett nyckelvalv och tilldela distributionsprincipen med [az keyvault create](/cli/azure/keyvault). I följande exempel skapas `myKeyVault` ett `myResourceGroup` nyckelvalv med namnet i resursgruppen:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Uppdatera ett nyckelvalv för användning med virtuella datorer
Ange distributionsprincipen för ett befintligt nyckelvalv med [az keyvault-uppdatering](/cli/azure/keyvault). Följande uppdaterar nyckelvalvet `myKeyVault` som `myResourceGroup` nämns i resursgruppen:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Använda mallar för att konfigurera Key Vault
När du använder en mall måste `enabledForDeployment` du `true` ange egenskapen till för Key Vault-resursen på följande sätt:

```json
{
    "type": "Microsoft.KeyVault/vaults",
    "name": "ContosoKeyVault",
    "apiVersion": "2015-06-01",
    "location": "<location-of-key-vault>",
    "properties": {
    "enabledForDeployment": "true",
    ....
    ....
    }
}
```

## <a name="next-steps"></a>Nästa steg
Andra alternativ som du kan konfigurera när du skapar ett Nyckelvalv med hjälp av mallar finns i [Skapa ett nyckelvalv](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
