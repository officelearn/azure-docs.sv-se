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
ms.openlocfilehash: 0885b187c4b9e9636144a3ab2a94b941008bec20
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59046743"
---
# <a name="deploy-an-azure-vm-from-a-user-vhd"></a>Distribuera en virtuell Azure-dator från en användare VHD

Den här artikeln förklarar hur du distribuerar en generaliserad avbildning av virtuell Hårddisk för att skapa en ny virtuell dator i Azure-resurs med hjälp av den angivna Azure Resource Manager-mall och Azure PowerShell-skript.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

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

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

#deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd 

```

## <a name="next-steps"></a>Nästa steg

När den virtuella datorn har distribuerats kan du är redo att [certifiera din avbildning](./cpp-certify-vm.md).
