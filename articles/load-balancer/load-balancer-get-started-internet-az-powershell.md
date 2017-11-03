---
title: "Skapa en offentlig belastningen belastningsutjämnaren Standard med zonredundant klientdel för offentlig IP-adress med hjälp av PowerShell | Microsoft Docs"
description: "Lär dig hur du skapar offentliga Load Balancer Standard med en zonredundant offentlig IP-adress klientdel med hjälp av PowerShell"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2017
ms.author: kumud
ms.openlocfilehash: 2945e04814f3d1e5b281391485def47f4b463d88
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-powershell"></a>Skapa en offentlig belastningen belastningsutjämnaren Standard med zonredundant klientdel för offentlig IP-adress med hjälp av PowerShell

Den här artikeln går igenom hur du skapar en offentlig [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) med en zonredundant klientdel med en Standard offentliga IP-adress.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="register-for-availability-zones-load-balancer-standard-and-public-ip-standard-preview"></a>Registrera sig för tillgänglighet zoner, Load Balancer Standard och offentliga IP-Standard förhandsversion

Den här artikeln kräver att du har version 4.4.0 eller högre för modulen AzureRM installerad. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver installera eller uppgradera, installera den senaste versionen av AzureRM-modul från den [PowerShell-galleriet](https://www.powershellgallery.com/packages/AzureRM).

>[!NOTE]
[Läsa in belastningsutjämning Standard SKU](https://aka.ms/azureloadbalancerstandard) är för närvarande under förhandsgranskning. Som förhandsversion kanske funktionen inte har samma tillgänglighet och pålitlighet som funktioner som är allmänt tillgängliga. Mer information finns i [de kompletterande villkoren för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Använd den allmänt tillgängliga [Load Balancer grundläggande SKU](load-balancer-overview.md) för produktion-tjänster. 

> [!NOTE]
> Tillgänglighet zoner i förhandsversionen och är redo för din utveckla och testa scenarier. Support är tillgänglig för väljer Azure-resurser och regioner och familjer för VM-storlek. Mer information om hur du kommer igång och vilka Azure-resurser, regioner, och Virtuella storlek familjer kan du försöka tillgänglighet zoner med finns [översikt av tillgänglighet zoner](https://docs.microsoft.com/azure/availability-zones/az-overview). Du kan få support via [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) eller genom att [öppna ett Azure-supportärende](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

Innan du väljer en zon eller zonredundant alternativ för klientdel offentliga IP-adressen för belastningsutjämnaren, måste du slutföra stegen i [registrera sig för tillgänglighet zoner förhandsversion](https://docs.microsoft.com/azure/availability-zones/az-overview).

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot `Login-AzureRmAccount` och följ anvisningarna på skärmen.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en resursgrupp med följande kommando:

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location westeurope
```

## <a name="create-a-public-ip-standard"></a>Skapa en offentlig IP-Standard 
Skapa en offentlig IP-Standard med följande kommando:

```powershell
$publicIp = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Name 'myPublicIP' `
  -Location westeurope -AllocationMethod Static -Sku Standard 
```

## <a name="create-a-front-end-ip-configuration-for-the-website"></a>Skapa en frontend IP-konfiguration för webbplats

Skapa en klientdelens IP-konfiguration med hjälp av följande kommando:

```powershell
$feip = New-AzureRmLoadBalancerFrontendIpConfig -Name 'myFrontEndPool' -PublicIpAddress $publicIp
```

## <a name="create-the-back-end-address-pool"></a>Skapa backend-adresspool

Skapa en backend-adresspool med följande kommando:

```powershell
$bepool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'
```

## <a name="create-a-load-balancer-probe-on-port-80"></a>Skapa en belastningsutjämningsavsökning på port 80

Skapa en hälsa avsökning på port 80 för belastningsutjämnaren med följande kommando:

```powershell
$probe = New-AzureRmLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
```

## <a name="create-a-load-balancer"></a>Skapa en belastningsutjämnare
Skapa en belastningen belastningsutjämnaren Standard med följande kommando:

```powershell
$lb = New-AzureRmLoadBalancer -ResourceGroupName myResourceGroup -Name 'MyLoadBalancer' -Location westeurope `
  -FrontendIpConfiguration $feip -BackendAddressPool $bepool `
  -Probe $probe -LoadBalancingRule $rule -Sku Standard
```

## <a name="next-steps"></a>Nästa steg
- Lär dig hur [skapa en offentlig IP-adress i en zon för tillgänglighet](../virtual-network/create-public-ip-availability-zone-portal.md)



