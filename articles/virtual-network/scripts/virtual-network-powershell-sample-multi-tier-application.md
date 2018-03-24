---
title: Azure PowerShell-skript exempel – skapa ett nätverk för flera nivåer program | Microsoft Docs
description: Azure PowerShell-skript exempel – skapa ett virtuellt nätverk för program på flera nivåer.
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
ms.openlocfilehash: d393752c899274c3af845e8a52a965c19ac81dc4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-network-for-multi-tier-applications"></a>Skapa ett nätverk för flera nivåer program

Det här exemplet i skriptet skapar ett virtuellt nätverk med frontend och backend-undernät. Trafik till undernätet för frontend är begränsad till http- och SSH, medan trafik till backend-undernät är begränsad till MySQL port 3306. När du har kört skriptet har två virtuella datorer i varje undernät som du kan distribuera webbservern och MySQL programvaran till.

Du kan köra skriptet från Azure [moln Shell](https://shell.azure.com/powershell), eller från en lokal installation av PowerShell. Om du använder PowerShell lokalt skriptet kräver AzureRM PowerShell Modulversion 5.4.1 eller senare. Du hittar den installerade versionen genom att köra `Get-Module -ListAvailable AzureRM`. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript


[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.ps1  "Virtual network for multi-tier application")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, virtuell dator och alla relaterade resurser:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa en resursgrupp, virtuella nätverk och nätverkssäkerhetsgrupper. Varje kommando i följande tabell länkar till kommando-specifika dokumentation:

| Kommando | Anteckningar |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Skapar en Azure-nätverket och frontend-undernät. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Skapar en backend-undernät. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Skapar en offentlig IP-adress för att komma åt den virtuella datorn från Internet. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Skapar virtuella nätverksgränssnitt och kopplar dem till det virtuella nätverket frontend och backend-undernät. |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | Skapar säkerhetsgrupper (NSG) som är kopplade till frontend och backend-undernät för nätverket. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) |Skapar NSG-regler som tillåter eller blockerar specifika portar till specifika undernät. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Skapar virtuella datorer och bifogar ett nätverkskort på varje virtuell dator. Det här kommandot anger också avbildning av virtuell dator ska användas och administrativa autentiseringsuppgifter. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Tar bort en resursgrupp och alla resurser som den innehåller. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare virtuella nätverk PowerShell-skript-exempel finns i [virtuella nätverk PowerShell-exempel](../powershell-samples.md).