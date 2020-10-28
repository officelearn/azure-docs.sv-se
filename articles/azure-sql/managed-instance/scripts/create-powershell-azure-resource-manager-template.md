---
title: Skapa en hanterad instans (ARM-mall & PowerShell)
titleSuffix: Azure SQL Managed Instance
description: Använd det här Azure PowerShell exempel skriptet för att skapa en hanterad instans.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-dt-2019, devx-track-azurepowershell
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: 7c364fe7fa0ac4dd70a01a75478289ea861dee7f
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790839"
---
# <a name="use-powershell-with-an-azure-resource-manager-template-to-create-a-managed-instance"></a>Använd PowerShell med en Azure Resource Manager mall för att skapa en hanterad instans

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Du kan skapa en hanterad instans med hjälp av Azure PowerShell bibliotek och Azure Resource Manager mallar.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell 1.4.0 eller senare. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt kör `Connect-AzAccount` du för att skapa en anslutning till Azure.

Azure PowerShell-kommandon kan starta distribution med hjälp av en fördefinierad Azure Resource Manager-mall. Följande egenskaper kan anges i mallen:

- Namn på hanterad instans
- Användarnamn och lösenord för SQL-administratör.
- Storleken på instansen (antal kärnor och högsta tillåtna lagringsstorlek).
- Virtuellt nätverk och undernät där instansen ska placeras.
- Sortering på server nivå för instansen (förhands granskning).

Instans namn, SQL-administratörens användar namn, VNet/undernät och sortering kan inte ändras senare. Andra instansegenskaper kan ändras.

## <a name="prerequisites"></a>Förutsättningar

Det här exemplet förutsätter att du har [skapat en giltig nätverks miljö](../virtual-network-subnet-create-arm-template.md) eller [ändrat ett befintligt virtuellt](../vnet-existing-add-subnet.md) nätverk för din hanterade instans. Du kan förbereda nätverks miljön med en separat [Azure Resource Manager mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment), om det behövs. 


Exemplet använder cmdletarna [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) och [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork), så kontrol lera att du har installerat följande PowerShell-moduler:

```powershell
Install-Module Az.Network
Install-Module Az.Resources
```

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall


Spara följande skript i en. JSON-fil och anteckna fil platsen: 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "instance": {
            "type": "string"
        },
        "user": {
            "type": "string"
        },
        "pwd": {
            "type": "securestring"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('instance')]",
            "location": "West Central US",
            "tags": {
                "Description":"GP Instance with custom instance collation - Serbian_Cyrillic_100_CS_AS"
            },
            "sku": {
                "name": "GP_Gen5",
                "tier": "GeneralPurpose"
            },
            "properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
                "collation": "Serbian_Cyrillic_100_CS_AS"
            },
            "type": "Microsoft.Sql/managedInstances",
            "identity": {
                "type": "SystemAssigned"
            },
            "apiVersion": "2015-05-01-preview"
        }
    ]
}
```

Uppdatera följande PowerShell-skript med rätt fil Sök väg för den. JSON-fil som du sparade tidigare och ändra namnen på objekten i skriptet:

```powershell
$subscriptionId = "ed827499-xxxx-xxxx-xxxx-xxxxxxxxxx"
Select-AzSubscription -SubscriptionId $subscriptionId

# Managed instance properties
$resourceGroup = "rg_mi"
$location = "West Central US"
$name = "managed-instance-name"
$user = "miSqlAdmin"
$secpasswd = ConvertTo-SecureString "<Put some strong password here>" -AsPlainText -Force

# Network configuration
$vNetName = "my_vnet"
$vNetResourceGroup = "rg_mi_vnet"
$subnetName = "ManagedInstances"
$vNet = Get-AzVirtualNetwork -Name $vNetName -ResourceGroupName $vNetResourceGroup
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vNet
$subnetId = $subnet.Id

# Deploy instance using Azure Resource Manager template:
New-AzResourceGroupDeployment  -Name MyDeployment -ResourceGroupName $resourceGroup  `
                                    -TemplateFile 'C:\...\create-managed-instance.json' `
                                    -instance $name -user $user -pwd $secpasswd -subnetId $subnetId
```

När skriptet har slutförts kan den hanterade instansen nås från alla Azure-tjänster och den konfigurerade IP-adressen.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell dokumentation](/powershell/azure/).

Ytterligare PowerShell-skript exempel för Azure SQL-hanterad instans finns i [PowerShell-skript för Azure SQL-hanterad instans](../../database/powershell-script-content-guide.md).