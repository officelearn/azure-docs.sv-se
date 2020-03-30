---
title: Distribuera en virtuell dator från Azure Marketplace
description: Förklarar hur du distribuerar en virtuell dator från en azure marketplace-förkonfigurerad virtuell dator.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 7d5269cf8865faeb65356bc8fd3eea087cb7653c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277981"
---
# <a name="deploy-a-virtual-machine-from-the-azure-marketplace"></a>Distribuera en virtuell dator från Azure Marketplace

I den här artikeln beskrivs hur du distribuerar en förkonfigurerad virtuell dator (VM) från en Azure Marketplace med det medföljande Azure PowerShell-skriptet.  Det här skriptet exponerar också WinRM HTTP- och HTTPS-slutpunkterna på den virtuella datorn.  Skriptet kräver att du redan har ett certifikat som överförs till Azure Key Vault, enligt beskrivningen i [Skapa certifikat för Azure Key Vault](./cpp-create-key-vault-cert.md). 

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vm-deployment-template"></a>Distributionsmall för virtuell dator

Snabbstart azure VM-distributionsmallen är tillgänglig som onlinefilen [azuredeploy.json](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json).  Den innehåller följande parametrar:

|  **Parametern**        |   **Beskrivning**                                 |
|  -------------        |   ---------------                                 |
| newStorageAccountName | Namn på lagringskontot                       |
| dnsNameForPublicIP    | DNS-namn för den offentliga IP-adressen. Måste vara gemener.    |
| adminUserName         | Administratörens användarnamn                          |
| adminPassword         | Administratörens lösenord                          |
| bildPublicerare        | Bildutgivare                                   |
| imageOffer            | Bild erbjudande                                       |
| bildSKU              | Bild SKU                                         |
| vmSize                | Storleken på den virtuella datorn                                    |
| vmName                | Den virtuella datorns namn                                    |
| vaultName (valvNamn)             | Namnet på nyckelvalvet                             |
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
