---
title: Konfigurera TCP-tidsgräns för belastnings utjämning i Azure
titleSuffix: Azure Load Balancer
description: I den här artikeln lär du dig hur du konfigurerar Azure Load Balancer timeout för TCP-inaktivitet.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: 317f6a73812b0e4284564ca9b5593e09e22edf12
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2020
ms.locfileid: "89048745"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Konfigurera timeout-inställningar för TCP-inaktivitet för Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln version 5.4.1 eller senare av Azure PowerShell-modulen. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="tcp-idle-timeout"></a>Timeout för TCP-inaktivitet
Azure Load Balancer har en timeout-inställning på 4 minuter till 30 minuter. Som standard är den inställd på 4 minuter. Om en period av inaktivitet är längre än timeout-värdet finns det ingen garanti för att TCP-eller HTTP-sessionen upprätthålls mellan klienten och moln tjänsten.

När anslutningen är stängd kan klient programmet få följande fel meddelande: "den underliggande anslutningen stängdes: en anslutning som förväntades vara aktiv stängdes av servern."

En vanlig metod är att använda en TCP Keep-Alive. Den här metoden håller anslutningen aktiv under en längre period. Mer information finns i dessa [.net-exempel](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). När Keep-Alive är aktiverat skickas paketen under perioder av inaktivitet på anslutningen. Keep-Alive-paket se till att timeout-värdet för inaktivitet inte uppnås och att anslutningen upprätthålls under en längre period.

Inställningen fungerar endast för inkommande anslutningar. Undvik att förlora anslutningen genom att konfigurera TCP Keep-Alive med ett intervall som är lägre än tids gränsen för inaktivitet eller öka timeout-värdet för inaktivitet. För att stödja dessa scenarier har stöd för en konfigurerbar tids gräns för inaktivitet lagts till.

TCP Keep-Alive fungerar för scenarier där batteri tiden inte är en begränsning. Det rekommenderas inte för mobila program. Genom att använda en TCP Keep-Alive i ett mobilt program kan du tömma enhetens batteri snabbare.

![TCP-tidsgräns](./media/load-balancer-tcp-idle-timeout/image1.png)

I följande avsnitt beskrivs hur du ändrar inställningarna för inaktiv tids gräns för offentliga IP-och belastnings Utjämnings resurser.

>[!NOTE]
> Timeout för TCP-inaktivitet påverkar inte belastnings Utjämnings regler på UDP-protokoll.


## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Konfigurera TCP-tidsgräns för din offentliga IP-adress på instans nivå till 15 minuter

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

`IdleTimeoutInMinutes` är valfritt. Om den inte har angetts är standard tids gränsen 4 minuter. Det acceptabla tids gräns intervallet är 4 till 30 minuter.

## <a name="set-the-tcp-timeout-on-a-load-balanced-rule-to-15-minutes"></a>Ange TCP-tidsgräns för en belastningsutjämnad regel till 15 minuter

Om du vill ange tids gräns för inaktivitet för en belastningsutjämnare anges "IdleTimeoutInMinutes" i den belastningsutjämnade regeln. Exempel:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```
## <a name="next-steps"></a>Nästa steg

[Översikt över intern belastnings utjämning](load-balancer-internal-overview.md)

[Kom igång med att konfigurera en Internet-riktad belastningsutjämnare](quickstart-load-balancer-standard-public-powershell.md)

[Konfigurera ett distributionsläge för lastbalanserare](load-balancer-distribution-mode.md)
