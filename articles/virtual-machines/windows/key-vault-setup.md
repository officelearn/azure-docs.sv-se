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
ms.openlocfilehash: a8c29f015b6b3652361a886585cb4ccc3f3b7293
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58519960"
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
För att skapa ett nyckelvalv med hjälp av PowerShell, se [Set och hämta en hemlighet från Azure Key Vault med hjälp av PowerShell](../../key-vault/quick-create-powershell.md).

Du kan använda det här PowerShell-cmdlet för nya nyckelvalv:

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Du kan använda det här PowerShell-cmdlet för befintliga nyckelvalv:

    Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="use-cli-to-set-up-key-vault"></a>Använd CLI för att konfigurera Key Vault
För att skapa ett nyckelvalv med hjälp av kommandoradsgränssnittet (CLI), se [hantera Key Vault med CLI](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault).

Du måste skapa nyckelvalvet innan du tilldelar distributionsprincipen CLI. Det kan du göra med hjälp av följande kommando:

    az keyvault set-policy ContosoKeyVault –enabled-for-deployment true

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
