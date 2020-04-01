---
title: PowerShell-exempel- Redundansgrupp - Hanterad azure SQL-databas-hanterad instans
description: Azure PowerShell-exempelskript för att skapa en hanterad Azure SQL-databas-hanterad instans, lägga till den i en redundansgrupp och testa redundans.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: e50877f6f3194885b139683fe865144384716b48
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73691756"
---
# <a name="use-powershell-to-add-an-azure-sql-database-managed-instance-to-a-failover-group"></a>Använda PowerShell för att lägga till en hanterad Azure SQL-databas-hanterad instans i en redundansgrupp 

Det här PowerShell-skriptexempeln skapar två hanterade instanser, lägger till dem i en redundansgrupp och testar sedan redundans från den primära hanterade instansen till den sekundära hanterade instansen. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien AZ PowerShell 1.4.0 eller senare. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="sample-scripts"></a>Exempelskript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

## <a name="clean-up-deployment"></a>Rensa distribution

Använd följande kommando för att ta bort resursgruppen och alla resurser som är associerade med den. Du måste ta bort resursgruppen två gånger. Om du tar bort resursgruppen första gången tas den hanterade instansen `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.`och de virtuella klustren bort, men felmeddelandet misslyckas sedan . Kör kommandot Ta bort AzResourceGroup en andra gång för att ta bort eventuella kvarvarande resurser samt resursgruppen.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en Azure-resursgrupp.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Skapar ett virtuellt nätverk.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Lägger till en undernätskonfiguration i ett virtuellt nätverk. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hämtar ett virtuellt nätverk i en resursgrupp. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Hämtar ett undernät i ett virtuellt nätverk. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Skapar en nätverkssäkerhetsgrupp. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Skapar en flödestabell. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Uppdaterar en undernätskonfiguration för ett virtuellt nätverk.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Uppdaterar ett virtuellt nätverk.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Hämtar en nätverkssäkerhetsgrupp. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Lägger till en nätverkssäkerhetsregelkonfiguration i en nätverkssäkerhetsgrupp. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Uppdaterar en nätverkssäkerhetsgrupp.  | 
| [Tillägg-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Lägger till en rutt i en flödestabell. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Uppdaterar en vägtabell.  |
| [Ny-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Skapar en hanterad Azure SQL-databas-hanterad instans.  |
| [Få-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Returnerar information om Azure SQL-hanterad databasinstans. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Skapar en offentlig IP-adress.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Skapar en IP-konfiguration för en virtuell nätverksgateway |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Skapar en virtuell nätverksgateway |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Skapar en anslutning mellan de två virtuella nätverksgatewayerna.   |
| [Nya-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Skapar en ny Azure SQL Database-hanterad instans redundansgrupp.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Hämtar eller listar hanterade förekomstöverundansättningar grupper.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Kör en redundansväxling för en hanterad instans redundansgrupp. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp. | 

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för SQL Database finns i [PowerShell-skript för Azure SQL Database](../sql-database-powershell-samples.md).
