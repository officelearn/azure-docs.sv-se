---
title: "Ställ in nyckeln valvet för virtuella Windows-datorer i Azure Resource Manager | Microsoft Docs"
description: "Hur du ställer in Key Vault för användning med en virtuell dator i Azure Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 33a483e2-cfbc-4c62-a588-5d9fd52491e2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: kasing
ms.openlocfilehash: a5083a5216efbfd76fd912ec48c2f0ec3b30c4a1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Ställ in Key Vault för virtuella datorer i Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

I Azure Resource Manager-stacken modelleras hemligheter/certifikat som resurser som tillhandahålls av Key Vault-resursprovidern. Mer information om Key Vault finns [vad är Azure Key Vault?](../../key-vault/key-vault-whatis.md)

> [!NOTE]
> 1. För Key Vault som ska användas med Azure Resource Manager virtuella datorer i **EnabledForDeployment** egenskapen i Nyckelvalvet måste anges till true. Du kan göra detta i olika klienter.
> 2. Key Vault måste skapas i samma prenumeration och plats som den virtuella datorn.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Använd PowerShell för att ställa in Key Vault
För att skapa ett nyckelvalv med hjälp av PowerShell Se [Kom igång med Azure Key Vault](../../key-vault/key-vault-get-started.md#vault).

Du kan använda följande PowerShell-cmdlet för nytt nyckelvalv:

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Du kan använda följande PowerShell-cmdlet för befintliga nyckelvalv:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="us-cli-to-set-up-key-vault"></a>Oss CLI för att ställa in Key Vault
För att skapa ett nyckelvalv med hjälp av kommandoradsgränssnittet (CLI), se [hantera Key Vault med hjälp av CLI](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault).

Du måste skapa nyckelvalvet innan du tilldelar princip för programdistribution för CLI. Det kan du göra med hjälp av följande kommando:

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>Använda mallar för att ställa in Key Vault
När du använder en mall måste du ange den `enabledForDeployment` egenskapen `true` för Key Vault-resurs.

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

Andra alternativ som du kan konfigurera när du skapar ett nyckelvalv med hjälp av mallar, se [skapa ett nyckelvalv](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
