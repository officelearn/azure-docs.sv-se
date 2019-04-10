---
title: Exempel på PowerShell – skapa en hanterad instans i Azure SQL Database | Microsoft Docs
description: Azure PowerShell-exempelskript för att skapa en hanterad instans i Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop-msft
ms.reviewer: sstein
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: dfd81735b7dfd95a38caf3934fe9057adbcde5a7
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357079"
---
# <a name="use-powershell-with-azure-resource-manager-template-to-create-a-managed-instance-in-azure-sql-database"></a>Använda PowerShell med Azure Resource Manager-mall för att skapa en hanterad instans i Azure SQL Database

Hanterad Azure SQL Database-instans kan skapas med hjälp av Azure PowerShell-biblioteket och Azure Resource Manager-mallar.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien AZ PowerShell 1.4.0 eller senare. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

Azure PowerShell-kommandon kan starta distribution med hjälp av en fördefinierad Azure Resource Manager-mall. Följande egenskaper kan anges i mallen:

- Instansnamn
- Användarnamn och lösenord för SQL-administratör.
- Storleken på instansen (antal kärnor och högsta tillåtna lagringsstorlek).
- Virtuellt nätverk och undernät där instansen ska placeras.
- Sorteringen av instansen på servernivå (förhandsversion).

Instansnamn, SQL-administratörsanvändarnamn, virtuellt nätverk/undernät och sortering kan inte ändras senare. Andra instansegenskaper kan ändras.

## <a name="prerequisites"></a>Förutsättningar

Det här exemplet förutsätter att du har [skapat en giltig nätverksmiljö](../sql-database-managed-instance-create-vnet-subnet.md) eller [ändrat befintligt virtuellt nätverk](../sql-database-managed-instance-configure-vnet-subnet.md) för din hanterade instans. Exemplet använder cmdletarna [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) och [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) så se till att du har installerat följande PowerShell-moduler:

```powershell
Install-Module Az.Network
Install-Module Az.Resources
```

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall

Följande innehåll ska placeras i en fil som representerar en mall som används för att skapa instansen:

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
                "name": "GP_Gen4",
                "tier": "GeneralPurpose"
            },
            "properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen4",
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

Förutsättningen är att ett virtuellt Azure-nätverk med korrekt konfigurerat undernät redan finns. Om du inte har ett korrekt konfigurerade undernät, Förbered nätverksmiljön använder separata [mall för Azure Resource hanterade](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) som kan köras separat eller ingår i den här mallen.

Spara innehållet i den här filen som en .json-fil, placera filsökvägen i följande PowerShell-skript och ändra namnen på objekten i skriptet:

```powershell
$subscriptionId = "ed827499-xxxx-xxxx-xxxx-xxxxxxxxxx"
Select-AzSubscription -SubscriptionId $subscriptionId

# Managed Instance properties
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

# Deploy Instance using Azure Resource Manager template:
New-AzResourceGroupDeployment  -Name MyDeployment -ResourceGroupName $resourceGroup  `
                                    -TemplateFile 'C:\...\create-managed-instance.json' `
                                    -instance $name -user $user -pwd $secpasswd -subnetId $subnetId
```

När skriptet har körts utan problem kan SQL-databasen nås från alla Azure-tjänster och den konfigurerade IP-adressen.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för SQL Database finns i [PowerShell-skript för Azure SQL Database](../sql-database-powershell-samples.md).
