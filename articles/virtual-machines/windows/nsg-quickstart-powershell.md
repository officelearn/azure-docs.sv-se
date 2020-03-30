---
title: Öppna portar till en virtuell dator med Azure PowerShell
description: Lär dig hur du öppnar en port/skapar en slutpunkt till din Windows VM med distributionsläget för Azure Resource Manager och Azure PowerShell
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
ms.openlocfilehash: 547ca9c98d77b2aaa6d3630bff4b2ec10dcc5be0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75754167"
---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Öppna portar och slutpunkter till en virtuell dator i Azure med PowerShell
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Snabbkommandon
Om du vill skapa en nätverkssäkerhetsgrupp och ACL-regler behöver du [den senaste versionen av Azure PowerShell installerat](/powershell/azureps-cmdlets-docs). Du kan också [utföra dessa steg med Azure-portalen](nsg-quickstart-portal.md).

Logga in på ditt Azure-konto:

```powershell
Connect-AzAccount
```

I följande exempel ersätter du parameternamn med dina egna värden. Exempelparameternamn inkluderade *myResourceGroup,* *myNetworkSecurityGroup*och *myVnet*.

Skapa en regel med [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig). I följande exempel skapas en regel med namnet *myNetworkSecurityGroupRule* för att tillåta *tcp-trafik* på port *80:*

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

Skapa sedan din nätverkssäkerhetsgrupp med [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup) och tilldela HTTP-regeln som du just skapade enligt följande. I följande exempel skapas en nätverkssäkerhetsgrupp med namnet *myNetworkSecurityGroup:*

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

Nu ska vi tilldela din nätverkssäkerhetsgrupp till ett undernät. I följande exempel tilldelas ett befintligt virtuellt nätverk med namnet *myVnet* till variabeln *$vnet* med [Get-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork)

```powershell
$vnet = Get-AzVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Associera din nätverkssäkerhetsgrupp med ditt undernät med [Set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworksubnetconfig). I följande exempel associeras undernätet med namnet *mySubnet* med din nätverkssäkerhetsgrupp:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Uppdatera slutligen det virtuella nätverket med [Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork) för att ändringarna ska börja gälla:

```powershell
Set-AzVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Mer information om nätverkssäkerhetsgrupper
De snabba kommandona här gör att du kan komma igång med trafik som flödar till din virtuella dator. Nätverkssäkerhetsgrupper ger många bra funktioner och granularitet för att kontrollera åtkomsten till dina resurser. Du kan läsa mer om [hur du skapar en nätverkssäkerhetsgrupp och ACL-regler här](tutorial-virtual-network.md#secure-network-traffic).

För webbprogram med hög tillgänglig tillgång bör du placera dina virtuella datorer bakom en Azure Load Balancer. Belastningsutjämnaren distribuerar trafik till virtuella datorer, med en nätverkssäkerhetsgrupp som tillhandahåller trafikfiltrering. Mer information finns i [Så här laddar du fördelning av virtuella Linux-datorer i Azure för att skapa ett program med hög tillgänglig tillgång](tutorial-load-balancer.md).

## <a name="next-steps"></a>Nästa steg
I det här exemplet skapade du en enkel regel för att tillåta HTTP-trafik. Du hittar information om hur du skapar mer detaljerade miljöer i följande artiklar:

* [Översikt över Azure Resource Manager](../../azure-resource-manager/management/overview.md)
* [Vad är en nätverkssäkerhetsgrupp?](../../virtual-network/security-overview.md)
* [Översikt över Azure-belastningsutjämning](../../load-balancer/load-balancer-overview.md)

