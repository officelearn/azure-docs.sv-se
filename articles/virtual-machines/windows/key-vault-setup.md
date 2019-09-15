---
title: Konfigurera Key Vault för virtuella Windows-datorer i Azure Resource Manager | Microsoft Docs
description: Konfigurera Key Vault för användning med en Azure Resource Manager virtuell dator.
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
ms.openlocfilehash: 225ce9fcbb18aa374f413e8e237c911c85cc77a6
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2019
ms.locfileid: "70999357"
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Konfigurera Key Vault för virtuella datorer i Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

I Azure Resource Manager stack modelleras hemligheter/certifikat som resurser som tillhandahålls av resurs leverantören av Key Vault. Mer information om Key Vault finns i [Vad är Azure Key Vault?](../../key-vault/key-vault-overview.md)

> [!NOTE]
> 1. För att Key Vault ska kunna användas med Azure Resource Manager virtuella datorer måste egenskapen **EnabledForDeployment** på Key Vault vara inställd på True. Du kan göra detta på olika klienter.
> 2. Key Vault måste skapas i samma prenumeration och på samma plats som den virtuella datorn.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Använd PowerShell för att konfigurera Key Vault
Om du vill skapa ett nyckel valv med hjälp av PowerShell läser du [Ange och hämta en hemlighet från Azure Key Vault med hjälp av PowerShell](../../key-vault/quick-create-powershell.md).

Du kan använda den här PowerShell-cmdleten för nya nyckel valv:

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Du kan använda den här PowerShell-cmdleten för befintliga nyckel valv:

    Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="use-cli-to-set-up-key-vault"></a>Använd CLI för att konfigurera Key Vault
Information om hur du skapar ett nyckel valv med hjälp av kommando rads gränssnittet (CLI) finns i [hantera Key Vault med CLI](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault).

För CLI måste du skapa nyckel valvet innan du tilldelar distributions principen. Det kan du göra med hjälp av följande kommando:

    az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "EastAsia"
    
Kör sedan följande kommando för att aktivera Key Vault för användning med mall-distribution:

    az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"

## <a name="use-templates-to-set-up-key-vault"></a>Använd mallar för att konfigurera Key Vault
När du använder en mall måste du ange `enabledForDeployment` egenskapen till `true` för Key Vault resursen.

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

För andra alternativ som du kan konfigurera när du skapar ett nyckel valv med hjälp av mallar, se [skapa ett nyckel valv](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
