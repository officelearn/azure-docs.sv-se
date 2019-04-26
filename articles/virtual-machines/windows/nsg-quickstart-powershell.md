---
title: Öppna portar till en virtuell dator med Azure PowerShell | Microsoft Docs
description: Lär dig hur du öppnar en port / skapa en slutpunkt för din Windows-VM med hjälp av Azure resource manager-distribution-läge och Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: cf45f7d8-451a-48ab-8419-730366d54f1e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: f536b32ebf5267f407d2c32eb425ea45469cd3b9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60405390"
---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Hur du öppnar portar och slutpunkter till en virtuell dator i Azure med PowerShell
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Snabbkommandon
Att skapa en Nätverkssäkerhetsgrupp och ACL-regler måste [den senaste versionen av Azure PowerShell installerad](/powershell/azureps-cmdlets-docs). Du kan också [utföra dessa steg med Azure-portalen](nsg-quickstart-portal.md).

Logga in på ditt Azure-konto:

```powershell
Connect-AzAccount
```

I följande exempel, ersätter du parameternamn med dina egna värden. Parametern exempelnamnen ingår *myResourceGroup*, *myNetworkSecurityGroup*, och *myVnet*.

Skapa en regel med [New AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig). I följande exempel skapas en regel med namnet *myNetworkSecurityGroupRule* att tillåta *tcp* trafik på port *80*:

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

Skapa sedan din nätverkssäkerhet grupp med [New AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup) och tilldela den HTTP-regeln som du just har skapat på följande sätt. I följande exempel skapas en Nätverkssäkerhetsgrupp med namnet *myNetworkSecurityGroup*:

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

Nu ska vi tilldela Nätverkssäkerhetsgruppen till ett undernät. I följande exempel tilldelas ett befintligt virtuellt nätverk med namnet *myVnet* till variabeln *$vnet* med [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork):

```powershell
$vnet = Get-AzVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Associera Nätverkssäkerhetsgruppen med ditt undernät med [Set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworksubnetconfig). I följande exempel kopplar undernätet med namnet *mySubnet* med din grupp:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Slutligen uppdaterar det virtuella nätverket med [Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork) för att ändringarna ska börja gälla:

```powershell
Set-AzVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Mer information om Nätverkssäkerhetsgrupper
Snabbkommandon här kan du komma igång med trafiken som flödar till den virtuella datorn. Nätverkssäkerhetsgrupper ger många bra funktioner och kornighet för att styra åtkomst till resurser. Du kan läsa mer om [skapar en Nätverkssäkerhetsgrupp och ACL-regler här](tutorial-virtual-network.md#secure-network-traffic).

För webbprogram med hög tillgänglighet, bör du placera dina virtuella datorer bakom en belastningsutjämnare för Azure. Belastningsutjämnaren distribuerar trafik till virtuella datorer med en Nätverkssäkerhetsgrupp som tillhandahåller trafikfiltrering. Mer information finns i [läsa in belastningsutjämna Linux-datorer i Azure för att skapa ett program med hög tillgänglighet](tutorial-load-balancer.md).

## <a name="next-steps"></a>Nästa steg
I det här exemplet skapade du en enkel regel för att tillåta HTTP-trafik. Du hittar information om hur du skapar mer detaljerad miljöer i följande artiklar:

* [Översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [Vad är en nätverkssäkerhetsgrupp?](../../virtual-network/security-overview.md)
* [Azure Resource Manager-översikt för belastningsutjämnare](../../load-balancer/load-balancer-arm.md)

