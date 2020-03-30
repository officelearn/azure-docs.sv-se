---
title: Distribuera en virtuell Azure-dator från en virtuell dator för användare | Azure Marketplace
description: Förklarar hur du distribuerar en virtuell användare-avbildning för att skapa en Azure VM-instans.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 957bc187b0123338947f62333c913cf82a6c66a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277964"
---
# <a name="deploy-an-azure-vm-from-a-user-vhd"></a>Distribuera en virtuell Azure-dator från en virtuell dator för användare

I den här artikeln beskrivs hur du distribuerar en generaliserad VHD-avbildning för att skapa en ny Azure VM-resurs med hjälp av den medföljande Azure Resource Manager-mallen och Azure PowerShell-skriptet.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vhd-deployment-template"></a>Mall för VHD-distribution

Kopiera Azure Resource Manager-mallen för [VHD-distribution](cpp-deploy-json-template.md) till en lokal fil med namnet `VHDtoImage.json`.  Redigera den här filen om du vill ange värden för följande parametrar. 

|  **Parametern**             |   **Beskrivning**                                                              |
|  -------------             |   ---------------                                                              |
| ResourceGroupName          | Befintligt Azure-resursgruppnamn.  Använd vanligtvis samma RG som är associerat med nyckelvalvet  |
| TemplateFile               | Fullständigt sökvägsnamn till filen`VHDtoImage.json`                                    |
| användareStoraRedovisningsnamn     | Namn på lagringskontot                                                    |
| sNameForPublicIP           | DNS-namn för den offentliga IP-adressen. Måste vara gemener                                  |
| subscriptionId             | Azure-prenumerationsidentifierare                                                  |
| Location                   | Standard Azure geografisk plats för resursgruppen                       |
| vmName                     | Namnet på den virtuella datorn                                                    |
| vaultName (valvNamn)                  | Namnet på nyckelvalvet                                                          |
| valvResourceGroup         | Resursgruppen för nyckelvalvet
| certifikatUrl             | Url för certifikatet, inklusive version som lagras i nyckelvalvet, till exempel:`https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
| vhdUrl (på)                     | URL för den virtuella hårddisken                                                   |
| vmSize                     | Storlek på instansen för den virtuella datorn                                           |
| publicIPAddressName        | Namnet på den offentliga IP-adressen                                                  |
| virtualNetworkName         | Namn på det virtuella nätverket                                                    |
| nicName (nicName)                    | Namn på nätverkskortet för det virtuella nätverket                     |
| adminUserName              | Användarnamn för administratörskontot                                          |
| adminPassword              | Administratörslösenord                                                          |
|  |  |


## <a name="powershell-script"></a>Powershell-skript

Kopiera och redigera följande skript för `$storageaccount` `$vhdUrl` att ange värden för variablerna och variablerna.  Kör den för att skapa en Azure VM-resurs från din befintliga generaliserade VIRTUELLA HÅRDDISK.

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

När den virtuella datorn har distribuerats är du redo att [certifiera avbildningen för den virtuella datorn](./cpp-certify-vm.md).
