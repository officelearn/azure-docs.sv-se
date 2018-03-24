---
title: Skriptexempel Azure PowerShell - vägtrafik via en virtuell nätverksenhet | Microsoft Docs
description: Azure PowerShell skriptexempel - vägtrafik via en virtuell nätverksenhet för brandväggen.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: 766d800a7875536b729f564a3d9948e5598de6aa
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="route-traffic-through-a-network-virtual-appliance"></a>Vidarebefordra trafik via en virtuell nätverksenhet

Det här exemplet i skriptet skapar ett virtuellt nätverk med frontend och backend-undernät. Dessutom skapas en virtuell dator med IP-vidarebefordring är aktiverat för att vidarebefordra trafik mellan två undernät. Du kan distribuera programvara för nätverk, till exempel en brandvägg för programmet, till den virtuella datorn när skriptet har körts.

Du kan köra skriptet från Azure [moln Shell](https://shell.azure.com/powershell), eller från en lokal installation av PowerShell. Om du använder PowerShell lokalt skriptet kräver AzureRM PowerShell Modulversion 5.4.1 eller senare. Du hittar den installerade versionen genom att köra `Get-Module -ListAvailable AzureRM`. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript


[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.ps1 "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, virtuell dator och alla relaterade resurser:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```
## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa en resursgrupp, virtuella nätverk och nätverkssäkerhetsgrupper. Varje kommando i följande tabell länkar till kommando-specifika dokumentation:

| Kommando | Anteckningar |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)  | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Skapar en Azure-nätverket och frontend-undernät. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Skapar backend- och DMZ undernät. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Skapar en offentlig IP-adress för att komma åt den virtuella datorn från internet. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Skapar ett virtuellt nätverksgränssnitt och aktivera IP-vidarebefordring för den. |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | Skapar en nätverkssäkerhetsgrupp (NSG). |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Skapar NSG-regler som tillåter HTTP och HTTPS-portar inkommande till den virtuella datorn. |
| [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)| Associerar NSG: er och vägtabeller till undernät. |
| [New-AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable)| Skapar en routningstabell för alla flöden. |
| [New-AzureRMRouteConfig](/powershell/module/azurerm.network/new-azurermrouteconfig)| Skapar vägar för att vidarebefordra trafik mellan undernät och internet via den virtuella datorn. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Skapar en virtuell dator och bifogar nätverkskortet till den. Det här kommandot anger också avbildning av virtuell dator ska användas och administrativa autentiseringsuppgifter. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup)  | Tar bort en resursgrupp och alla resurser som den innehåller. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/azure/overview).

Ytterligare virtuella nätverk PowerShell-skript-exempel finns i [virtuella nätverk PowerShell-exempel](../powershell-samples.md).