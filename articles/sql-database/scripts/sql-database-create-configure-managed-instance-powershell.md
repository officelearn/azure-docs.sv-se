---
title: PowerShell-exempel – skapa en hanterad instans i Azure SQL Database
description: Azure PowerShell-exempelskript för att skapa en hanterad instans i Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: 3e72a2f6754ad8e9c5bcfabe7eeee299468fa8f3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73691637"
---
# <a name="use-powershell-to-create-an-azure-sql-database-managed-instance"></a>Använda PowerShell för att skapa en hanterad Azure SQL-databas-hanterad instans

Det här PowerShell-skriptexemplet skapar en Azure SQL Database-hanterad instans i ett dedikerat undernät i ett nytt virtuellt nätverk. Den konfigurerar också en vägtabell och en nätverkssäkerhetsgrupp för det virtuella nätverket. När skriptet har körts kan den hanterade instansen nås från det virtuella nätverket eller från en lokal miljö. Se [Konfigurera Azure VM för att ansluta till en hanterad Azure SQL-databas-instans](../sql-database-managed-instance-configure-vm.md) och konfigurera en [point-to-site-anslutning till en Hanterad Azure SQL-databas-instans från lokal](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> Om du har begränsningar finns i [regioner som stöds](../sql-database-managed-instance-resource-limits.md#supported-regions) och [prenumerationstyper som stöds](../sql-database-managed-instance-resource-limits.md#supported-subscription-types).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien AZ PowerShell 1.4.0 eller senare. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="sample-script"></a>Exempelskript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>Rensa distribution

Använd följande kommando för att ta bort resursgruppen och alla resurser som är associerade med den.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras.
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Skapar ett virtuellt nätverk |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | Lägger till en nätkonfiguration i ett virtuellt nätverk |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | Hämtar ett virtuellt nätverk i en resursgrupp |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | Anger måltillstånd för ett virtuellt nätverk |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | Hämtar ett undernät i ett virtuellt nätverk |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | Konfigurerar måltillståndet för en undernätskonfiguration i ett virtuellt nätverk |
| [New-AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | Skapar en flödestabell |
| [Få-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | Hämtar rutttabeller |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | Anger målläge för en rutttabell |
| [Ny-AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | Skapar en hanterad Azure SQL-databas-instans |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för SQL Database finns i [PowerShell-skript för Azure SQL Database](../sql-database-powershell-samples.md).
