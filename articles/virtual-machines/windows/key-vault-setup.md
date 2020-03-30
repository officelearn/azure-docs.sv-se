---
title: Konfigurera Key Vault för virtuella Windows-datorer i Azure Resource Manager
description: Konfigurera Key Vault för användning med en virtuell Azure Resource Manager-dator.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 33a483e2-cfbc-4c62-a588-5d9fd52491e2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/24/2017
ms.author: kasing
ms.openlocfilehash: a64163da1dee2bceb567436dc18ba0fa5274cfcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243151"
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Konfigurera Key Vault för virtuella datorer i Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

I Azure Resource Manager-stacken modelleras hemligheter/certifikat som resurser som tillhandahålls av resursleverantören för Key Vault. Mer information om Key Vault finns i [Vad är Azure Key Vault?](../../key-vault/key-vault-overview.md)

> [!NOTE]
> 1. För att Key Vault ska kunna användas med virtuella Azure Resource Manager-datorer måste egenskapen **EnabledForDeployment** i Key Vault anges till true. Du kan göra detta i olika kunder.
> 2. Nyckelvalvet måste skapas på samma prenumeration och plats som den virtuella datorn.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Använda PowerShell för att konfigurera Key Vault
Information om hur du skapar ett nyckelvalv med PowerShell finns i [Ange och hämta en hemlighet från Azure Key Vault med PowerShell](../../key-vault/quick-create-powershell.md).

För nya nyckelvalv kan du använda den här PowerShell-cmdlet:

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

För befintliga nyckelvalv kan du använda den här PowerShell-cmdlet:

    Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="use-cli-to-set-up-key-vault"></a>Använd CLI för att konfigurera Key Vault
Om du vill skapa ett nyckelvalv med hjälp av kommandoradsgränssnittet (CLI) finns i [Hantera nyckelvalv med CLI](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault).

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
