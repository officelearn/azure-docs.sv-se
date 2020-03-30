---
title: Konfigurera tidsgränsen för belastningsutjämnad TCP-inaktiv i Azure
titleSuffix: Azure Load Balancer
description: I den här artikeln kan du läsa om hur du konfigurerar Azure Load Balancer TCP inaktiv timeout.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: d0bb73b58aa23e5f7eb784772acf37b05df463ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456836"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Konfigurera timeout-inställningar för inaktiv TCP för Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln version 5.4.1 eller senare av Azure PowerShell-modulen. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="tcp-idle-timeout"></a>Tidsgränsen för TCP-inaktiv
Azure Load Balancer har en inaktiv timeout-inställning på 4 minuter till 30 minuter. Som standard är den inställd på 4 minuter. Om en period av inaktivitet är längre än timeout-värdet finns det ingen garanti för att TCP- eller HTTP-sessionen upprätthålls mellan klienten och molntjänsten.

När anslutningen stängs kan klientprogrammet få följande felmeddelande: "Den underliggande anslutningen stängdes: En anslutning som förväntades hållas vid liv stängdes av servern."

En vanlig praxis är att använda en TCP keep-alive. Den här metoden håller anslutningen aktiv under en längre period. Mer information finns i dessa [.NET-exempel](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Med keep-alive aktiverat skickas paket under perioder av inaktivitet på anslutningen. Keep-alive paket se till att inaktiv timeout-värdet inte nås och anslutningen underhålls under en lång period.

Inställningen fungerar endast för inkommande anslutningar. Om du vill undvika att förlora anslutningen konfigurerar du TCP-keep-alive med ett intervall mindre än inställningen för inaktiv timeout eller ökar tidsgränsen för inaktiva lägen. Stöd för dessa scenarier har stöd för en konfigurerbar inaktiv timeout lagts till.

TCP keep-alive fungerar för scenarier där batteritiden inte är en begränsning. Det rekommenderas inte för mobila applikationer. Med hjälp av en TCP keep-alive i en mobil applikation kan tömma enhetens batteri snabbare.

![TCP-timeout](./media/load-balancer-tcp-idle-timeout/image1.png)

I följande avsnitt beskrivs hur du ändrar inaktiva timeout-inställningar för offentliga IP- och belastningsutjämnareresurser.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Konfigurera TCP-timeouten för den offentliga IP-adressen på instansnivå till 15 minuter

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

`IdleTimeoutInMinutes` är valfritt. Om den inte är inställd är standardtidsgränsen 4 minuter. Det godtagbara timeout-intervallet är 4 till 30 minuter.

## <a name="set-the-tcp-timeout-on-a-load-balanced-rule-to-15-minutes"></a>Ange TCP-timeouten på en belastningsbalanserad regel på 15 minuter

Om du vill ange tidsgränsen för inaktiv för en belastningsutjämning ställs indleTimeoutInMinutes in på den belastningsbalanserade regeln. Ett exempel:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```
## <a name="next-steps"></a>Nästa steg

[Översikt över intern belastningsutjämnare](load-balancer-internal-overview.md)

[Komma igång med att konfigurera en Internet-vänd belastningsutjämnare](quickstart-create-standard-load-balancer-powershell.md)

[Konfigurera ett distributionsläge för lastbalanserare](load-balancer-distribution-mode.md)
