---
title: Exempel på PowerShell – skapa en hanterad instans i Azure SQL Database | Microsoft Docs
description: Azure PowerShell-exempelskript för att skapa en hanterad instans i Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: 955f13376ac899f66b0ec4e1ed99166164508fbe
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449879"
---
# <a name="use-powershell-to-create-an-azure-sql-database-managed-instance"></a>Använd PowerShell för att skapa en Azure SQL Database-hanterad instans

Det här PowerShell-Skriptexemplet skapar en hanterad Azure SQL Database-instans i ett dedikerat undernät i ett nytt virtuellt nätverk. Den konfigurerar också en routningstabell och en nätverkssäkerhetsgrupp för det virtuella nätverket. När skriptet har körts utan problem, kan den hanterade instansen nås från det virtuella nätverket eller från en lokal miljö. Snabbstart som visar hur du gör detta, se [konfigurera Azure VM för att ansluta till en Azure SQL Database Managed Instance](../sql-database-managed-instance-configure-vm.md) och [konfigurera en punkt-till-plats-anslutning till en Azure SQL Database Managed Instance från den lokala](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> Begränsningar, finns i [regioner som stöds](../sql-database-managed-instance-resource-limits.md#supported-regions) och [stöds prenumerationstyper](../sql-database-managed-instance-resource-limits.md#supported-subscription-types).

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien AZ PowerShell 1.4.0 eller senare. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="sample-script"></a>Exempelskript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>Rensa distribution

När exempelskriptet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. 
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Skapar ett virtuellt nätverk |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | Lägger till en undernätskonfiguration till ett virtuellt nätverk |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | Hämtar ett virtuellt nätverk i en resursgrupp |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | Konfigurerar målstatusen för ett virtuellt nätverk |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | Hämtar ett undernät i ett virtuellt nätverk |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | Konfigurerar målstatusen för ett undernät i ett virtuellt nätverk |
| [New-AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | Skapar en routningstabell |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | Hämtar routningstabeller |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | Konfigurerar målstatusen för en routningstabell |
| [Ny AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | Skapar en hanterad Azure SQL Database-instans |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för SQL Database finns i [PowerShell-skript för Azure SQL Database](../sql-database-powershell-samples.md).
