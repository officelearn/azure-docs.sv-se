---
title: 'Skapa en instans (ARM-mall & PowerShell) '
titleSuffix: Azure SQL Managed Instance
description: Använd det här Azure PowerShell exempel skriptet för att skapa en hanterad Azure SQL-instans.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: seo-dt-2019
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: 55b0c8f569a91075d4cd87541af7aeff5da69f9a
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053971"
---
# <a name="use-powershell-with-azure-resource-manager-template-to-create-an-azure-sql-managed-instance"></a>Använd PowerShell med Azure Resource Manager mall för att skapa en hanterad Azure SQL-instans
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Azure SQL-hanterad instans kan skapas med Azure PowerShell biblioteks-och Azure Resource Manager mallar.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien AZ PowerShell-1.4.0 eller senare. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt kör `Connect-AzAccount` du för att skapa en anslutning till Azure.

Azure PowerShell-kommandon kan starta distribution med hjälp av en fördefinierad Azure Resource Manager-mall. Följande egenskaper kan anges i mallen:

- SQL-hanterad instans namn
- Användarnamn och lösenord för SQL-administratör.
- Storleken på instansen (antal kärnor och högsta tillåtna lagringsstorlek).
- Virtuellt nätverk och undernät där instansen ska placeras.
- Sorteringen av instansen på servernivå (förhandsversion).

Instansnamn, SQL-administratörsanvändarnamn, virtuellt nätverk/undernät och sortering kan inte ändras senare. Andra instansegenskaper kan ändras.

## <a name="prerequisites"></a>Förutsättningar

Det här exemplet förutsätter att du har [skapat en giltig nätverks miljö](../virtual-network-subnet-create-arm-template.md) eller [ändrat ett befintligt VNet](../vnet-existing-add-subnet.md) för din SQL-hanterade instans. Du kan förbereda nätverks miljön med en separat [Azure-resurs hanterad mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment), om det behövs. 


Exemplet använder cmdletarna [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) och [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) så kontrol lera att du har installerat följande PowerShell-moduler:

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

När skriptet har slutförts kan SQL-hanterad instans nås från alla Azure-tjänster och den konfigurerade IP-adressen.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skript exempel för SQL-hanterad instans finns i [PowerShell-skripten för Azure SQL-hanterad instans](../../database/powershell-script-content-guide.md).
