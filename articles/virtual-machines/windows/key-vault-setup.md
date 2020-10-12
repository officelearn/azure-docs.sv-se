---
title: Konfigurera Key Vault
description: Så här konfigurerar du Key Vault för användning med en virtuell dator.
author: mimckitt
manager: vashan
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/24/2017
ms.author: mimckitt
ms.openlocfilehash: d5e4bb1d3f62685c90e95eebf5f8f07720a23b82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "86087001"
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Konfigurera Key Vault för virtuella datorer i Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

I Azure Resource Manager stack modelleras hemligheter/certifikat som resurser som tillhandahålls av resurs leverantören av Key Vault. Mer information om Key Vault finns i [Vad är Azure Key Vault?](../../key-vault/general/overview.md)

> [!NOTE]
> 1. För att Key Vault ska kunna användas med Azure Resource Manager virtuella datorer måste egenskapen **EnabledForDeployment** på Key Vault vara inställd på True. Du kan göra detta på olika klienter.
> 2. Key Vault måste skapas i samma prenumeration och på samma plats som den virtuella datorn.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Använd PowerShell för att konfigurera Key Vault
Om du vill skapa ett nyckel valv med hjälp av PowerShell läser du [Ange och hämta en hemlighet från Azure Key Vault med hjälp av PowerShell](../../key-vault/secrets/quick-create-powershell.md).

Du kan använda den här PowerShell-cmdleten för nya nyckel valv:

```azurepowershell
New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment
```

Du kan använda den här PowerShell-cmdleten för befintliga nyckel valv:

```azurepowershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment
```

## <a name="use-cli-to-set-up-key-vault"></a>Använd CLI för att konfigurera Key Vault
Information om hur du skapar ett nyckel valv med hjälp av kommando rads gränssnittet (CLI) finns i [hantera Key Vault med CLI](../../key-vault/general/manage-with-cli2.md#create-a-key-vault).

För CLI måste du skapa nyckel valvet innan du tilldelar distributions principen. Det kan du göra med hjälp av följande kommando:

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "EastAsia"
```

Kör sedan följande kommando för att aktivera Key Vault för användning med mall-distribution:

```azurecli
az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
```

## <a name="use-templates-to-set-up-key-vault"></a>Använd mallar för att konfigurera Key Vault
När du använder en mall måste du ange `enabledForDeployment` egenskapen till `true` för Key Vault resursen.

```config
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

För andra alternativ som du kan konfigurera när du skapar ett nyckel valv med hjälp av mallar, se [skapa ett nyckel valv](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
