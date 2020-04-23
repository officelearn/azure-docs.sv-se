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
ms.openlocfilehash: e4bff4d1826d9586495207095eccf8f6c66164a0
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870011"
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Konfigurera Key Vault för virtuella datorer i Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

I Azure Resource Manager-stacken modelleras hemligheter/certifikat som resurser som tillhandahålls av resursleverantören för Key Vault. Mer information om Key Vault finns i [Vad är Azure Key Vault?](../../key-vault/general/overview.md)

> [!NOTE]
> 1. För att Key Vault ska kunna användas med virtuella Azure Resource Manager-datorer måste egenskapen **EnabledForDeployment** i Key Vault anges till true. Du kan göra detta i olika kunder.
> 2. Nyckelvalvet måste skapas på samma prenumeration och plats som den virtuella datorn.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Använda PowerShell för att konfigurera Key Vault
Information om hur du skapar ett nyckelvalv med PowerShell finns i [Ange och hämta en hemlighet från Azure Key Vault med PowerShell](../../key-vault/secrets/quick-create-powershell.md).

För nya nyckelvalv kan du använda den här PowerShell-cmdlet:

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

För befintliga nyckelvalv kan du använda den här PowerShell-cmdlet:

    Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="use-cli-to-set-up-key-vault"></a>Använd CLI för att konfigurera Key Vault
Om du vill skapa ett nyckelvalv med hjälp av kommandoradsgränssnittet (CLI) finns i [Hantera nyckelvalv med CLI](../../key-vault/general/manage-with-cli2.md#create-a-key-vault).

För CLI måste du skapa nyckelvalvet innan du tilldelar distributionsprincipen. Det kan du göra med hjälp av följande kommando:

    az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "EastAsia"
    
Kör sedan följande kommando för att aktivera Key Vault för användning med malldistribution:

    az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"

## <a name="use-templates-to-set-up-key-vault"></a>Använda mallar för att konfigurera Key Vault
När du använder en mall måste `enabledForDeployment` du `true` ange egenskapen till för Resursen Key Vault.

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

Andra alternativ som du kan konfigurera när du skapar ett nyckelvalv med hjälp av mallar finns i [Skapa ett nyckelvalv](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
