---
title: Konfigurera TCP-återställning av belastnings utjämning och tids gräns för inaktivitet i Azure
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
ms.date: 10/09/2020
ms.author: allensu
ms.openlocfilehash: 26c4c01aaf6abe6b9c9ac6daf6836d7b660ba21e
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2020
ms.locfileid: "91649872"
---
# <a name="configure-tcp-idle-timeout-for-azure-load-balancer"></a>Konfigurera timeout för TCP-inaktivitet för Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln version 5.4.1 eller senare av Azure PowerShell-modulen. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

Azure Load Balancer har en timeout-inställning på 4 minuter till 120 minuter. Som standard är den inställd på 4 minuter. Om en period av inaktivitet är längre än timeout-värdet finns det ingen garanti för att TCP-eller HTTP-sessionen upprätthålls mellan klienten och moln tjänsten. Läs mer om [timeout för TCP-inaktivitet](load-balancer-tcp-reset.md).

I följande avsnitt beskrivs hur du ändrar inställningarna för inaktiv tids gräns för offentliga IP-och belastnings Utjämnings resurser.


## <a name="configure-the-tcp-idle-timeout-for-your-public-ip"></a>Konfigurera timeout för TCP-inaktivitet för din offentliga IP

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

`IdleTimeoutInMinutes` är valfritt. Om den inte har angetts är standard tids gränsen 4 minuter. Det acceptabla tids gräns intervallet är 4 till 120 minuter.

## <a name="set-the-tcp-idle-timeout-on-rules"></a>Ange timeout för TCP-inaktivitet på regler

Om du vill ange tids gräns för inaktivitet för en belastningsutjämnare anges "IdleTimeoutInMinutes" i den belastningsutjämnade regeln. Exempel:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```

## <a name="next-steps"></a>Nästa steg

[Översikt över intern belastnings utjämning](load-balancer-internal-overview.md)

[Kom igång med att konfigurera en Internet-riktad belastningsutjämnare](quickstart-load-balancer-standard-public-powershell.md)

[Konfigurera ett distributionsläge för lastbalanserare](load-balancer-distribution-mode.md)
