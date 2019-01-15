---
title: Distribuera en virtuell Azure-dator från en användare VHD | Microsoft Docs
description: Förklarar hur du distribuerar en VHD-avbildning för användaren för att skapa en Azure VM-instans.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/29/2018
ms.author: pbutlerm
ms.openlocfilehash: 48be60a7ba5770f8c329cb6323a5caa8fcf7f961
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265064"
---
# <a name="deploy-an-azure-vm-from-a-user-vhd"></a>Distribuera en virtuell Azure-dator från en användare VHD

Den här artikeln förklarar hur du distribuerar en generaliserad avbildning av virtuell Hårddisk för att skapa en ny virtuell dator i Azure-resurs med hjälp av den angivna Azure Resource Manager-mall och Azure PowerShell-skript.


## <a name="vhd-deployment-template"></a>Mall för distribution av virtuell Hårddisk

Kopiera Azure Resource Manager-mall för [VHD distribution](cpp-deploy-json-template.md) till en lokal fil med namnet `VHDtoImage.json`.  Redigera den här filen för att ange värden för följande parametrar. 

|  **Parametern**             |   **Beskrivning**                                                              |
|  -------------             |   ---------------                                                              |
| ResourceGroupName          | Befintliga Azure resursgruppens namn.  Vanligtvis Använd samma RG som är associerade med ditt nyckelvalv  |
| TemplateFile               | Fullständig sökväg till filen `VHDtoImage.json`                                    |
| userStorageAccountName     | Namnet på lagringskontot                                                    |
| sNameForPublicIP           | DNS-namnet för den offentliga IP-Adressen. Måste vara gemener                                  |
| subscriptionId             | Azure-prenumerations-ID                                                  |
| Plats                   | Standard Azure geografisk plats för resursgruppen                       |
| vmName                     | Namnet på den virtuella datorn                                                    |
| vaultName                  | Namnet på nyckelvalvet                                                          |
| vaultResourceGroup         | Resursgrupp för nyckelvalvet
| certificateUrl             | URL: en för certifikatet med version som lagras i nyckelvalvet, till exempel:  `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
| vhdUrl                     | URL: en för den virtuella hårddisken                                                   |
| vmSize                     | Storleken på den virtuella datorinstansen                                           |
| publicIPAddressName        | Namnet på den offentliga IP-adressen                                                  |
| virtualNetworkName         | Namnet på det virtuella nätverket                                                    |
| nicName                    | Namnet på nätverkskortet för det virtuella nätverket                     |
| adminUserName              | Användarnamnet för administratörskontot                                          |
| adminPassword              | Lösenord                                                          |
|  |  |


## <a name="powershell-script"></a>PowerShell-skript

Kopiera och redigera följande skript för att ange värden för den `$storageaccount` och `$vhdUrl` variabler.  Kör den för att skapa en virtuell dator i Azure-resurs från din befintliga generaliserad virtuell Hårddisk.

```powershell
# storage account of existing generalized VHD 
$storageaccount = "testwinrm11815"

# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzureRMResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

#deploying VM with existing VHD
New-AzureRMResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd 

```

## <a name="next-steps"></a>Nästa steg

När den virtuella datorn har distribuerats kan du är redo att [certifiera din avbildning](./cpp-certify-vm.md).
