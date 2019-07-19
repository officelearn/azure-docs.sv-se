---
title: Skapa en Load Balancer med zon-redundant frontend-Azure PowerShell
titlesuffix: Azure Load Balancer
description: Lär dig hur du skapar offentliga Standard Load Balancer med en zon-redundant offentlig IP-adress-frontend med PowerShell
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: allensu
ms.openlocfilehash: 3ce14a0d9d5b9d5ef7ca7be34faad3fa60795ab9
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275096"
---
#  <a name="create-a-standard-load-balancer-with-zone-redundant-frontend-using-azure-powershell"></a>Skapa en Standardbelastningsutjämnare med zonredundant klientdel med Azure PowerShell

Den här artikeln beskriver hur du skapar en offentlig [standard Load Balancer](https://aka.ms/azureloadbalancerstandard) med en zon-redundant klient del med en offentlig IP-standardadress.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

> [!NOTE]
>  Stöd för Tillgänglighetszoner är tillgänglig för väljer Azure-resurser och regioner och VM-storlekar. Mer information om hur du kommer igång och vilka Azure-resurser, regioner, och VM-storlekar som du kan prova tillgänglighetszoner med finns i [översikt över Tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview). Du kan få support via [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) eller genom att [öppna ett Azure-supportärende](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot `Connect-AzAccount` och följ anvisningarna på skärmen.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en resursgrupp med hjälp av följande kommando:

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westeurope
```

## <a name="create-a-public-ip-standard"></a>Skapa en offentlig IP-Standard 
Skapa en offentlig IP-Standard med följande kommando:

```azurepowershell-interactive
$publicIp = New-AzPublicIpAddress -ResourceGroupName myResourceGroup -Name 'myPublicIP' `
  -Location westeurope -AllocationMethod Static -Sku Standard
```

## <a name="create-a-front-end-ip-configuration-for-the-website"></a>Skapa en frontend IP-konfiguration för webbplatsen

Skapa en frontend IP-konfiguration med hjälp av följande kommando:

```azurepowershell-interactive
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEndPool' -PublicIpAddress $publicIp
```

## <a name="create-the-back-end-address-pool"></a>Skapa backend-adresspool

Skapa en backend-adresspool med följande kommando:

```azurepowershell-interactive
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'
```

## <a name="create-a-load-balancer-probe-on-port-80"></a>Skapa en belastningsutjämnaravsökning på port 80

Skapa en hälsoavsökning på port 80 för belastningsutjämnaren med följande kommando:

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
```

## <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel
 Skapa en regel för belastningsutjämnaren med följande kommando:

```azurepowershell-interactive
   $rule = New-AzLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $feip -BackendAddressPool  $bepool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

## <a name="create-a-load-balancer"></a>Skapa en lastbalanserare
Skapa en Standard Load Balancer med hjälp av följande kommando:

```azurepowershell-interactive
$lb = New-AzLoadBalancer -ResourceGroupName myResourceGroup -Name 'MyLoadBalancer' -Location westeurope `
  -FrontendIpConfiguration $feip -BackendAddressPool $bepool `
  -Probe $probe -LoadBalancingRule $rule -Sku Standard
```

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Standard Load Balancer och tillgänglighet zoner](load-balancer-standard-availability-zones.md).