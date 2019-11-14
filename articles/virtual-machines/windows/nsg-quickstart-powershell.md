---
title: Öppna portar till en virtuell dator med hjälp av Azure PowerShell
description: Lär dig hur du öppnar en port/skapar en slut punkt för din virtuella Windows-dator med hjälp av distributions läget i Azure Resource Manager och Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: cf45f7d8-451a-48ab-8419-730366d54f1e
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: 3577c97b55baebe08ca4020b0c570160d551dfd3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033082"
---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Så här öppnar du portar och slut punkter till en virtuell dator i Azure med hjälp av PowerShell
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Snabbkommandon
Om du vill skapa en nätverks säkerhets grupp och ACL-regler behöver du [den senaste versionen av Azure PowerShell installerad](/powershell/azureps-cmdlets-docs). Du kan också [utföra dessa steg med hjälp av Azure Portal](nsg-quickstart-portal.md).

Logga in på ditt Azure-konto:

```powershell
Connect-AzAccount
```

I följande exempel ersätter du parameter namn med dina egna värden. Exempel på parameter namn som ingår *myResourceGroup*, *myNetworkSecurityGroup*och *myVnet*.

Skapa en regel med [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig). I följande exempel skapas en regel med namnet *myNetworkSecurityGroupRule* för att tillåta *TCP* -trafik på port *80*:

```powershell
$httprule = New-AzNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" `
    -Access "Allow" `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority "100" `
    -SourceAddressPrefix "Internet" `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 80
```

Skapa sedan din nätverks säkerhets grupp med [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup) och tilldela den http-regel som du nyss skapade på följande sätt. I följande exempel skapas en nätverks säkerhets grupp med namnet *myNetworkSecurityGroup*:

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

Nu ska vi tilldela din nätverks säkerhets grupp till ett undernät. I följande exempel tilldelas ett befintligt virtuellt nätverk med namnet *myVnet* till variabeln *$VNet* med [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork):

```powershell
$vnet = Get-AzVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Koppla din nätverks säkerhets grupp till ditt undernät med [set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworksubnetconfig). I följande exempel associeras under nätet med namnet *mitt undernät* med nätverks säkerhets gruppen:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Uppdatera slutligen det virtuella nätverket med [set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork) för att ändringarna ska börja gälla:

```powershell
Set-AzVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Mer information om nätverks säkerhets grupper
Med hjälp av snabb kommandona kan du komma igång med trafik som flödar till den virtuella datorn. Nätverks säkerhets grupper ger många fantastiska funktioner och granularitet för att kontrol lera åtkomsten till dina resurser. Du kan läsa mer om att [skapa en nätverks säkerhets grupp och ACL-regler här](tutorial-virtual-network.md#secure-network-traffic).

För webb program med hög tillgänglighet bör du placera de virtuella datorerna bakom en Azure Load Balancer. Belastningsutjämnaren distribuerar trafik till virtuella datorer, med en nätverks säkerhets grupp som tillhandahåller trafik filtrering. Mer information finns i [så här kan du belastningsutjämna virtuella Linux-datorer i Azure för att skapa ett program med hög](tutorial-load-balancer.md)tillgänglighet.

## <a name="next-steps"></a>Nästa steg
I det här exemplet har du skapat en enkel regel för att tillåta HTTP-trafik. Du hittar information om att skapa mer detaljerade miljöer i följande artiklar:

* [Översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [Vad är en nätverks säkerhets grupp?](../../virtual-network/security-overview.md)
* [Azure Resource Manager översikt för belastningsutjämnare](../../load-balancer/load-balancer-arm.md)

