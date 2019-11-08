---
title: Distribuera en virtuell Azure-dator från en användar-VHD | Azure Marketplace
description: Förklarar hur du distribuerar en användar-VHD-avbildning för att skapa en virtuell Azure-instans.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: 8421e9b7b7e2b7d13054e977da83be044b4e6af7
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73816633"
---
# <a name="deploy-an-azure-vm-from-a-user-vhd"></a>Distribuera en virtuell Azure-dator från en användar-VHD

Den här artikeln beskriver hur du distribuerar en generaliserad VHD-avbildning för att skapa en ny Azure VM-resurs med hjälp av den angivna Azure Resource Manager-mallen och Azure PowerShell skriptet.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vhd-deployment-template"></a>Mall för VHD-distribution

Kopiera Azure Resource Manager-mallen för [VHD-distribution](cpp-deploy-json-template.md) till en lokal fil med namnet `VHDtoImage.json`.  Redigera den här filen för att ange värden för följande parametrar. 

|  **ProfileServiceApplicationProxy**             |   **Beskrivning**                                                              |
|  -------------             |   ---------------                                                              |
| ResourceGroupName          | Befintligt namn på Azure-resurs gruppen.  Använder vanligt vis samma RG som är kopplad till ditt nyckel valv  |
| TemplateFile               | Fullständig sökväg till filen `VHDtoImage.json`                                    |
| userStorageAccountName     | Namn på lagrings kontot                                                    |
| sNameForPublicIP           | DNS-namn för den offentliga IP-adressen. Måste vara gemener                                  |
| subscriptionId             | Prenumerations-ID för Azure                                                  |
| Plats                   | Standard Azure-geografisk plats för resurs gruppen                       |
| vmName                     | Namn på den virtuella datorn                                                    |
| vaultName                  | Nyckel valvets namn                                                          |
| vaultResourceGroup         | Nyckel valvets resurs grupp
| certificateUrl             | URL för certifikatet, inklusive version som lagras i nyckel valvet, till exempel: `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
| vhdUrl                     | URL för den virtuella hård disken                                                   |
| vmSize                     | Storlek på den virtuella dator instansen                                           |
| publicIPAddressName        | Namn på den offentliga IP-adressen                                                  |
| virtualNetworkName         | Namn på det virtuella nätverket                                                    |
| nicName                    | Nätverks gränssnitts kortets namn för det virtuella nätverket                     |
| adminUserName              | Användar namn för administratörs kontot                                          |
| adminPassword              | Administratörs lösen ord                                                          |
|  |  |


## <a name="powershell-script"></a>PowerShell-skript

Kopiera och redigera följande skript för att ange värden för `$storageaccount` och `$vhdUrl` variabler.  Kör den för att skapa en Azure VM-resurs från din befintliga generaliserade virtuella hård disk.

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

När den virtuella datorn har distribuerats är du redo att [certifiera din VM-avbildning](./cpp-certify-vm.md).
