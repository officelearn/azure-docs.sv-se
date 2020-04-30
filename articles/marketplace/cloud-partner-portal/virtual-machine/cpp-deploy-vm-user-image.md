---
title: Distribuera en virtuell Azure-dator från en användar-VHD | Azure Marketplace
description: Förklarar hur du distribuerar en användar-VHD-avbildning för att skapa en virtuell Azure-instans.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: e3bad2dc63f6a75f52c537aabfa6e85d1846ef15
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147922"
---
# <a name="deploy-an-azure-vm-from-a-user-vhd"></a>Distribuera en virtuell Azure-dator från en användar-VHD

> [!IMPORTANT]
> Med början den 13 april 2020 kommer vi att börja flytta hanteringen av dina virtuella Azure-datorer till Partner Center. Efter migreringen kommer du att skapa och hantera dina erbjudanden i Partner Center. Följ anvisningarna i [Azures avbildnings certifiering för virtuella datorer](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-image-certification) för att hantera dina migrerade erbjudanden.

Den här artikeln beskriver hur du distribuerar en generaliserad VHD-avbildning för att skapa en ny Azure VM-resurs med hjälp av den angivna Azure Resource Manager-mallen och Azure PowerShell skriptet.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vhd-deployment-template"></a>Mall för VHD-distribution

Kopiera Azure Resource Manager-mallen för [VHD-distribution](cpp-deploy-json-template.md) till en lokal fil `VHDtoImage.json`med namnet.  Redigera den här filen för att ange värden för följande parametrar. 

|  **ProfileServiceApplicationProxy**             |   **Beskrivning**                                                              |
|  -------------             |   ---------------                                                              |
| ResourceGroupName          | Befintligt namn på Azure-resurs gruppen.  Använder vanligt vis samma RG som är kopplad till ditt nyckel valv  |
| TemplateFile               | Fullständig sökväg till filen`VHDtoImage.json`                                    |
| userStorageAccountName     | Namn på lagrings kontot                                                    |
| sNameForPublicIP           | DNS-namn för den offentliga IP-adressen. Måste vara gemener                                  |
| subscriptionId             | Prenumerations-ID för Azure                                                  |
| Plats                   | Standard Azure-geografisk plats för resurs gruppen                       |
| vmName                     | Namn på den virtuella datorn                                                    |
| vaultName                  | Nyckel valvets namn                                                          |
| vaultResourceGroup         | Nyckel valvets resurs grupp
| certificateUrl             | URL för certifikatet, inklusive version som lagras i nyckel valvet, till exempel:`https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
| vhdUrl                     | URL för den virtuella hård disken                                                   |
| vmSize                     | Storlek på den virtuella dator instansen                                           |
| publicIPAddressName        | Namn på den offentliga IP-adressen                                                  |
| virtualNetworkName         | Namn på det virtuella nätverket                                                    |
| nicName                    | Nätverks gränssnitts kortets namn för det virtuella nätverket                     |
| adminUserName              | Användar namn för administratörs kontot                                          |
| adminPassword              | Administratörs lösen ord                                                          |
|  |  |


## <a name="powershell-script"></a>PowerShell-skript

Kopiera och redigera följande skript för att ange värden för `$storageaccount` variablerna `$vhdUrl` och.  Kör den för att skapa en Azure VM-resurs från din befintliga generaliserade virtuella hård disk.

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
