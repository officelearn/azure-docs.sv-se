---
title: Distribuera en virtuell dator från Azure Marketplace
description: Förklarar hur du distribuerar en virtuell dator från en azure marketplace-förkonfigurerad virtuell dator.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 6e39f1c70cd94c14b12e54817941ea9106aacfdd
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273876"
---
# <a name="deploy-a-virtual-machine-from-the-azure-marketplace"></a>Distribuera en virtuell dator från Azure Marketplace

> [!IMPORTANT]
> Från och med den 13 april 2020 börjar vi flytta hanteringen av dina Azure Virtual Machine-erbjudanden till Partner Center. Efter migreringen skapar och hanterar du dina erbjudanden i Partner center. Följ instruktionerna i [Azure VM-avbildningscertifiering](https://aks.ms/CertifyVMimage) för att hantera dina migrerade erbjudanden.

I den här artikeln beskrivs hur du distribuerar en förkonfigurerad virtuell dator (VM) från en Azure Marketplace med det medföljande Azure PowerShell-skriptet.  Det här skriptet exponerar också WinRM HTTP- och HTTPS-slutpunkterna på den virtuella datorn.  Skriptet kräver att du redan har ett certifikat som överförs till Azure Key Vault, enligt beskrivningen i [Skapa certifikat för Azure Key Vault](./cpp-create-key-vault-cert.md). 

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vm-deployment-template"></a>Distributionsmall för virtuell dator

Snabbstart azure VM-distributionsmallen är tillgänglig som onlinefilen [azuredeploy.json](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json).  Den innehåller följande parametrar:

|  **Parametern**        |   **Beskrivning**                                 |
|  -------------        |   ---------------                                 |
| newStorageAccountName    | Namn på lagringskontot                       |
| dnsNameForPublicIP    | DNS-namn för den offentliga IP-adressen. Måste vara gemener.    |
| adminUserName            | Administratörens användarnamn                          |
| adminPassword            | Administratörens lösenord                          |
| bildPublicerare        | Bildutgivare                                   |
| imageOffer            | Bild erbjudande                                       |
| bildSKU                | Bild SKU                                         |
| vmSize                | Storleken på den virtuella datorn                                    |
| vmName                | Den virtuella datorns namn                                    |
| vaultName (valvNamn)                | Namnet på nyckelvalvet                             |
| valvResourceGroup    | Resursgruppen för nyckelvalvet                   |
| certifikatUrl        | URL för certifikatet, inklusive version i KeyVault, till exempel`https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
|  |  |


## <a name="deployment-script"></a>Distributionsskript

Redigera följande Azure PowerShell-skript och kör det för att distribuera den angivna Virtuella Azure Marketplace-datorn.

```powershell

New-AzResourceGroupDeployment -Name "dplvm$postfix" -ResourceGroupName "$rgName" -TemplateUri "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json" -newStorageAccountName "test$postfix" -dnsNameForPublicIP $vmName -adminUserName "isv" -adminPassword $pwd -vmSize "Standard_A2" -vmName $vmName -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id 

```


## <a name="next-steps"></a>Nästa steg

När du har distribuerat en förkonfigurerad virtuell dator kan du konfigurera och komma åt de lösningar och tjänster som den innehåller, eller använda den för vidare utveckling. 
