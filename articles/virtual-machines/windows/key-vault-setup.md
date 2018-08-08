---
title: Konfigurera Key Vault för Windows virtuella datorer i Azure Resource Manager | Microsoft Docs
description: Hur du ställer in Key Vault för användning med en Azure Resource Manager-dator.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 33a483e2-cfbc-4c62-a588-5d9fd52491e2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: kasing
ms.openlocfilehash: 599b16f633d9a0de5165bdf5cb3d7b82abca655b
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2018
ms.locfileid: "39597718"
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Konfigurera Key Vault för virtuella datorer i Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

I Azure Resource Manager-stacken modelleras hemligheter/certifikat som resurser som tillhandahålls av resursprovidern för Key Vault. Läs mer om Key Vault i [vad är Azure Key Vault?](../../key-vault/key-vault-whatis.md)

> [!NOTE]
> 1. För Key Vault som ska användas med Azure Resource Manager-datorer, den **EnabledForDeployment** egenskapen Key Vault måste anges till true. Du kan göra detta i olika klienter.
> 2. Key Vault måste skapas i samma prenumeration och plats som den virtuella datorn.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Använd PowerShell för att konfigurera Key Vault
För att skapa ett nyckelvalv med hjälp av PowerShell, se [Kom igång med Azure Key Vault](../../key-vault/key-vault-get-started.md#vault).

Du kan använda det här PowerShell-cmdlet för nya nyckelvalv:

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Du kan använda det här PowerShell-cmdlet för befintliga nyckelvalv:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="use-cli-to-set-up-key-vault"></a>Använd CLI för att konfigurera Key Vault
För att skapa ett nyckelvalv med hjälp av kommandoradsgränssnittet (CLI), se [hantera Key Vault med CLI](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault).

Du måste skapa nyckelvalvet innan du tilldelar distributionsprincipen CLI. Det kan du göra med hjälp av följande kommando:

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>Använd mallar för att konfigurera Key Vault
När du använder en mall kan du behöva ange den `enabledForDeployment` egenskap `true` för Key Vault-resursen.

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

Andra alternativ som du kan konfigurera när du skapar ett nyckelvalv med hjälp av mallar finns i [skapa key vault](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
