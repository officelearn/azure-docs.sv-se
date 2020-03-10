---
title: Konfigurera Azure Key Vault för virtuella Linux-datorer
description: Konfigurera Key Vault för användning med en Azure Resource Manager virtuell dator med Azure CLI.
author: singhkays
manager: gwallace
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/24/2017
ms.author: kasing
ms.openlocfilehash: c9659c38e349bd62f22ed0d0839d97651b1b3b66
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944822"
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli"></a>Konfigurera Key Vault för virtuella datorer med Azure CLI

I Azure Resource Manager stack modelleras hemligheter/certifikat som resurser som tillhandahålls av Key Vault. Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](../../key-vault/key-vault-overview.md) För att Key Vault ska kunna användas med Azure Resource Manager virtuella datorer måste egenskapen *EnabledForDeployment* på Key Vault vara inställd på True. Den här artikeln visar hur du konfigurerar Key Vault för användning med Azure Virtual Machines (VM) med hjälp av Azure CLI. 

För att utföra de här stegen måste du ha den senaste versionen av [Azure CLI](/cli/azure/install-az-cli2) installerat och inloggad på ett Azure-konto med [AZ-inloggning](/cli/azure/reference-index).

## <a name="create-a-key-vault"></a>Skapa en Key Vault-lösning
Skapa ett nyckel valv och tilldela distributions principen med [AZ-valv skapa](/cli/azure/keyvault). I följande exempel skapas ett nyckel valv med namnet `myKeyVault` i resurs gruppen `myResourceGroup`:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Uppdatera en Key Vault för användning med virtuella datorer
Ange distributions principen för ett befintligt nyckel valv med [AZ-uppdatering](/cli/azure/keyvault)av nyckel valv. Följande uppdaterar nyckel valvet med namnet `myKeyVault` i `myResourceGroup` resurs grupp:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Använd mallar för att konfigurera Key Vault
När du använder en mall måste du ange `enabledForDeployment`-egenskapen till `true` för Key Vault resursen enligt följande:

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
För andra alternativ som du kan konfigurera när du skapar en Key Vault med hjälp av mallar, se [skapa ett nyckel valv](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
