---
title: Distribuera en virtuell dator från Azure Marketplace
description: Förklarar hur du distribuerar en virtuell dator från en förkonfigurerad virtuell dator i Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: 2888d7643fd4f624634dc2ec520bec6e753382f1
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73816831"
---
# <a name="deploy-a-virtual-machine-from-the-azure-marketplace"></a>Distribuera en virtuell dator från Azure Marketplace

Den här artikeln beskriver hur du distribuerar en förkonfigurerad virtuell dator (VM) från en Azure Marketplace med hjälp av det tillhandahållna Azure PowerShell skriptet.  Det här skriptet visar också WinRM HTTP-och HTTPS-slutpunkter på den virtuella datorn.  Skriptet kräver att du redan har ett certifikat som har överförts till Azure Key Vault, enligt beskrivningen i [Skapa certifikat för Azure Key Vault](./cpp-create-key-vault-cert.md). 

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vm-deployment-template"></a>Mall för distribution av virtuell dator

Mallen för att distribuera virtuella Azure-datorer är tillgänglig som online-filen [azuredeploy. JSON](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json).  Den innehåller följande parametrar:

|  **ProfileServiceApplicationProxy**        |   **Beskrivning**                                 |
|  -------------        |   ---------------                                 |
| newStorageAccountName | Namn på lagrings kontot                       |
| dnsNameForPublicIP    | DNS-namn för den offentliga IP-adressen. Måste vara gemener.    |
| adminUserName         | Administratörens användar namn                          |
| adminPassword         | Administratörens lösen ord                          |
| imagePublisher        | Avbildnings utgivare                                   |
| imageOffer            | Bild erbjudande                                       |
| imageSKU              | Bild-SKU                                         |
| vmSize                | Storlek på den virtuella datorn                                    |
| vmName                | Namn på den virtuella datorn                                    |
| vaultName             | Nyckel valvets namn                             |
| vaultResourceGroup    | Nyckel valvets resurs grupp                   |
| certificateUrl        | URL för certifikatet, inklusive version i nyckel valv, till exempel `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
|  |  |


## <a name="deployment-script"></a>Distributions skript

Redigera följande Azure PowerShell skript och kör det för att distribuera den angivna virtuella Azure Marketplace-datorn.

```powershell

New-AzResourceGroupDeployment -Name "dplvm$postfix" -ResourceGroupName "$rgName" -TemplateUri "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json" -newStorageAccountName "test$postfix" -dnsNameForPublicIP $vmName -adminUserName "isv" -adminPassword $pwd -vmSize "Standard_A2" -vmName $vmName -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id 

```


## <a name="next-steps"></a>Nästa steg

När du har distribuerat en förkonfigurerad virtuell dator kan du konfigurera och komma åt de lösningar och tjänster som den innehåller, eller använda den för ytterligare utveckling. 
