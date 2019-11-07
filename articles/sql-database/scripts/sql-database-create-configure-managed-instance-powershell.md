---
title: PowerShell-exempel – skapa en hanterad instans i Azure SQL Database
description: Azure PowerShell exempel skript för att skapa en hanterad instans i Azure SQL Database
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
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691637"
---
# <a name="use-powershell-to-create-an-azure-sql-database-managed-instance"></a>Använd PowerShell för att skapa en Azure SQL Database Hanterad instans

Det här PowerShell-skript exemplet skapar en Azure SQL Database Hanterad instans i ett dedikerat undernät i ett nytt virtuellt nätverk. Den konfigurerar också en routningstabell och en nätverks säkerhets grupp för det virtuella nätverket. När skriptet har körts kan den hanterade instansen nås från det virtuella nätverket eller från en lokal miljö. Se [Konfigurera virtuell Azure-dator för att ansluta till en Azure SQL Database Hanterad instans](../sql-database-managed-instance-configure-vm.md) och [Konfigurera en punkt-till-plats-anslutning till en Azure SQL Database Hanterad instans från den lokala](../sql-database-managed-instance-configure-p2s.md)datorn.

> [!IMPORTANT]
> För begränsningar, se [regioner som stöds](../sql-database-managed-instance-resource-limits.md#supported-regions) och [prenumerations typer som stöds](../sql-database-managed-instance-resource-limits.md#supported-subscription-types).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien AZ PowerShell-1.4.0 eller senare. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="sample-script"></a>Exempelskript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>Rensa distribution

Använd följande kommando för att ta bort resurs gruppen och alla resurser som är kopplade till den.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras.
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Skapar ett virtuellt nätverk |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | Lägger till en under näts konfiguration i ett virtuellt nätverk |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | Hämtar ett virtuellt nätverk i en resurs grupp |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | Anger mål tillstånd för ett virtuellt nätverk |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | Hämtar ett undernät i ett virtuellt nätverk |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | Konfigurerar mål tillstånd för en under näts konfiguration i ett virtuellt nätverk |
| [New-AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | Skapar en routningstabell |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | Hämtar routningstabeller |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | Anger mål status för en routningstabell |
| [New-AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | Skapar en Azure SQL Database Hanterad instans |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för SQL Database finns i [PowerShell-skript för Azure SQL Database](../sql-database-powershell-samples.md).
